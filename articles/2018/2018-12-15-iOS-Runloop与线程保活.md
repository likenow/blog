---
title: iOS Runloop与线程保活
date: 2018-12-15 12:25:26
tags:
- iOS
- runloop
- 多线程
categories:
- iOS
---

## 前言

在前两篇文章[iOS 多线程编程知识整理](https://dnduuhn.com/2018/12/02/iOS-%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%BC%96%E7%A8%8B%E7%9F%A5%E8%AF%86%E6%95%B4%E7%90%86/)、[iOS 多线编程之线程安全](https://dnduuhn.com/2018/12/11/iOS-%E5%A4%9A%E7%BA%BF%E7%BC%96%E7%A8%8B%E4%B9%8B%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8/) 中，我整理了 iOS 多线程编程的知识，还有非常重要的一块没有讲到那就是 **Runloop** 与线程。

<!--more-->

## Runloop 与 线程

关于**Runloop**的理解，以我现在的知识基础，不觉得会比[深入理解RunLoop](https://blog.ibireme.com/2015/05/18/runloop/)这篇文章讲得好，所以读者可移步阅读。

**Runloop**是线程的基础架构部分，`Cocoa`和`CoreFundation`都提供了 runLoop 对象方便配置和管理线程的runLoop。每个线程，包括程序的主线程`main thread`都有与之相应的 runLoop 对象。

主线程的 runLoop 默认是启动的。对其它线程来说，runLoop 默认是没有启动的。另外，苹果不允许直接创建 runLoop，不过我们可以通过：

```objective-c
NSRunLoop *runloop = [NSRunLoop currentRunLoop];
```

来获取到当前线程的 run loop。

从 `CFRunLoopRef` 的[源码](https://opensource.apple.com/source/CF/CF-1153.18/CFRunLoop.c.auto.html)可以看出，线程和 RunLoop 之间是一一对应的，其关系是保存在一个全局的 `Dictionary` 里。

```c
/*
CF-1153.18/CFRunLoop.c
line: 1358-1395
*/
// should only be called by Foundation
// t==0 is a synonym for "main thread" that always works
CF_EXPORT CFRunLoopRef _CFRunLoopGet0(pthread_t t) {
    if (pthread_equal(t, kNilPthreadT)) {
	t = pthread_main_thread_np();
    }
    __CFLock(&loopsLock);
    if (!__CFRunLoops) {
        __CFUnlock(&loopsLock);
	CFMutableDictionaryRef dict = CFDictionaryCreateMutable(kCFAllocatorSystemDefault, 0, NULL, &kCFTypeDictionaryValueCallBacks);
	CFRunLoopRef mainLoop = __CFRunLoopCreate(pthread_main_thread_np());
	CFDictionarySetValue(dict, pthreadPointer(pthread_main_thread_np()), mainLoop);
	if (!OSAtomicCompareAndSwapPtrBarrier(NULL, dict, (void * volatile *)&__CFRunLoops)) {
	    CFRelease(dict);
	}
	CFRelease(mainLoop);
        __CFLock(&loopsLock);
    }
    CFRunLoopRef loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
    __CFUnlock(&loopsLock);
    if (!loop) {
	CFRunLoopRef newLoop = __CFRunLoopCreate(t);
        __CFLock(&loopsLock);
	loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
	if (!loop) {
	    CFDictionarySetValue(__CFRunLoops, pthreadPointer(t), newLoop);
	    loop = newLoop;
	}
        // don't release run loops inside the loopsLock, because CFRunLoopDeallocate may end up taking it
        __CFUnlock(&loopsLock);
	CFRelease(newLoop);
    }
    if (pthread_equal(t, pthread_self())) {
        _CFSetTSD(__CFTSDKeyRunLoop, (void *)loop, NULL);
        if (0 == _CFGetTSD(__CFTSDKeyRunLoopCntr)) {
            _CFSetTSD(__CFTSDKeyRunLoopCntr, (void *)(PTHREAD_DESTRUCTOR_ITERATIONS-1), (void (*)(void *))__CFFinalizeRunLoop);
        }
    }
    return loop;
}
```



- 线程刚创建时并没有 runLoop，如果你不主动获取，那它一直都不会有。
- RunLoop 的创建是发生在第一次获取时，runLoop 的销毁是发生在线程结束时。
- 你只能在一个线程的内部获取其 runLoop（*主线程除外*）。



## Runloop与线程保活

### 在 *AFNetworking(2.x) 源码* 的分析文章中，你可能会看到如下被多次提及的代码片段：

```objective-c
/*
AFNetworking/NSURLConnection/AFURLConnectionOperation.m
*/ 
+ (NSThread *)networkRequestThread {
    static NSThread *_networkRequestThread = nil;
    static dispatch_once_t oncePredicate;
    dispatch_once(&oncePredicate, ^{
        _networkRequestThread = [[NSThread alloc] initWithTarget:self selector:@selector(networkRequestThreadEntryPoint:) object:nil];
        [_networkRequestThread start];
    });

    return _networkRequestThread;
}
------------------------------------------------------------------------
/*
AFNetworking/NSURLConnection/AFURLConnectionOperation.m
*/    

+ (void)networkRequestThreadEntryPoint:(id)__unused object {
    @autoreleasepool {
        [[NSThread currentThread] setName:@"AFNetworking"];

        NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
        [runLoop addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
        [runLoop run];
    }
}
```

AFN 创建了一个新的线程命名为 `AFNetworking` ，然后在这个线程中创建了一个 runLoop ，一个 runLoop 中如果`source/timer/observer` 都为空则会直接退出，并不进入循环。所以，AFN在这里为 runLoop 添加了一个 `NSMachPort` ，这个`port`开启相当于添加了一个`Source1`事件源，但是这个事件源并没有真正的监听什么东西，只是为了不让 runLoop 退出。

### 在AF3.x下，子线程使用 NSURLSession 时，还需要手动启动 Runloop 来实现线程保活吗？

不需要。

使用了 `NSURLConnection` 来发起并处理网络连接。AFN 的做法是把网络请求的发起和解析都放在同一个子线程中进行，但由于子线程默认不开启 runloop，它会向一个 C语言程序那样在运行完所有代码后退出线程。而网络请求是异步的，这会导致获取到请求数据时，线程已经退出，代理方法没有机会执行。因此，AFN 的做法是使用一个 runloop 来保证线程不死，

从`iOS9.0`开始 `deprecated` 了`NSURLConnection`。 替代方案就是`NSURLSession`。

`NSURLSession`发起的请求，不再需要在当前线程进行代理方法的回调！可以指定回调的`delegateQueue`：

```objective-c
/*
AFNetworking/AFURLSessionManager.m
*/
- (NSURLSession *)session {
    
    @synchronized (self) {
        if (!_session) {
            _session = [NSURLSession sessionWithConfiguration:self.sessionConfiguration delegate:self delegateQueue:self.operationQueue];
        }
    }
    return _session;
}
```

这样我们就不用为了等待代理回调方法而苦苦保活线程了。

同时还要注意一下，指定的用于接收回调的`Queue`的`maxConcurrentOperationCount`设为了1：

```objective-c
/*
AFNetworking/AFURLSessionManager.m
*/
- (instancetype)initWithSessionConfiguration:(NSURLSessionConfiguration *)configuration {
    self = [super init];
    if (!self) {
        return nil;
    }

    if (!configuration) {
        configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
    }

    self.sessionConfiguration = configuration;

    self.operationQueue = [[NSOperationQueue alloc] init];
    // 并发的请求串行的进行回调
    self.operationQueue.maxConcurrentOperationCount = 1;

    self.responseSerializer = [AFJSONResponseSerializer serializer];

    self.securityPolicy = [AFSecurityPolicy defaultPolicy];

#if !TARGET_OS_WATCH
    self.reachabilityManager = [AFNetworkReachabilityManager sharedManager];
#endif

    self.mutableTaskDelegatesKeyedByTaskIdentifier = [[NSMutableDictionary alloc] init];

    self.lock = [[NSLock alloc] init];
    self.lock.name = AFURLSessionManagerLockName;

    __weak typeof(self) weakSelf = self;
    [self.session getTasksWithCompletionHandler:^(NSArray *dataTasks, NSArray *uploadTasks, NSArray *downloadTasks) {
        
        __strong typeof(weakSelf) strongSelf = weakSelf;
        for (NSURLSessionDataTask *task in dataTasks) {
            [strongSelf addDelegateForDataTask:task uploadProgress:nil downloadProgress:nil completionHandler:nil];
        }

        for (NSURLSessionUploadTask *uploadTask in uploadTasks) {
            [strongSelf addDelegateForUploadTask:uploadTask progress:nil completionHandler:nil];
        }

        for (NSURLSessionDownloadTask *downloadTask in downloadTasks) {
            [strongSelf addDelegateForDownloadTask:downloadTask progress:nil destination:nil completionHandler:nil];
        }
    }];

    return self;
}
```

这里目的是想要让并发的请求串行的进行回调。

如果，对 NSOperationQueue 还不熟悉，可以阅读[iOS 多线程编程知识整理](http://localhost:4000/2018/12/02/iOS-%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%BC%96%E7%A8%8B%E7%9F%A5%E8%AF%86%E6%95%B4%E7%90%86/) ：）



### 在阅读 *Weex(0.18.0) 源码* 的时候也看到了线程保活的用法

```objective-c
#define WX_COMPONENT_THREAD_NAME @"com.taobao.weex.component"

#define WX_BRIDGE_THREAD_NAME @"com.taobao.weex.bridge"
```

#### com.taobao.weex.bridge

```objective-c
/*
WeexSDK/Sources/Manager/WXBridgeManager.m
*/
#pragma mark Thread Management

- (void)_runLoopThread
{
    [[NSRunLoop currentRunLoop] addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
    
    while (!_stopRunning) {
        @autoreleasepool {
            [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
        }
    }
}

+ (NSThread *)jsThread
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        
        WXBridgeThread = [[NSThread alloc] initWithTarget:[[self class]sharedManager] selector:@selector(_runLoopThread) object:nil];
        [WXBridgeThread setName:WX_BRIDGE_THREAD_NAME];
        if(WX_SYS_VERSION_GREATER_THAN_OR_EQUAL_TO(@"8.0")) {
            [WXBridgeThread setQualityOfService:[[NSThread mainThread] qualityOfService]];
        } else {
            [WXBridgeThread setThreadPriority:[[NSThread mainThread] threadPriority]];
        }
        
        [WXBridgeThread start];
    });
    
    return WXBridgeThread;
}
```

所有的组件注册都在这个子线程中执行的。这个`jsThread`是一个单例，全局唯一。这里就给`jsThread`开启了一个 runloop 。这里是用`[NSMachPort port]`的方式开启的 runloop，之后再也无法获取到这个`port`了，而且这个 runloop 不是`CFRunloop`，所以用[官方文档](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1)上的那2个方法已经不能停止这个 runloop 了:

> There are two ways to make a run loop exit before it has processed an event: 
>
> - Configure the run loop to run with a timeout value. 
>
> - Tell the run loop to stop.
>
>   ps. Although removing a run loop’s input sources and timers may also cause the run loop to exit, this is not a reliable way to stop a run loop.

只能自己通过`while`的方式来停止。上述代码是一种写法，当然`StackOverFlow`上面推荐的是下面的写法。

```objective-c
BOOL shouldKeepRunning = YES;        // global 
NSRunLoop *rl = [NSRunLoop currentRunLoop]; 
while (shouldKeepRunning && [rl runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]]);

```

#### com.taobao.weex.component

每次刷新一次，都会先调用`Flexbox`算法的`Layout`进行布局，这个布局是在子线程`com.taobao.weex.component`执行的

```objective-c
/*
WeexSDK/Sources/Manager/WXComponentManager.mm
*/
#pragma mark Thread Management

+ (NSThread *)componentThread
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        WXComponentThread = [[NSThread alloc] initWithTarget:[self sharedManager] selector:@selector(_runLoopThread) object:nil];
        [WXComponentThread setName:WX_COMPONENT_THREAD_NAME];
        if(WX_SYS_VERSION_GREATER_THAN_OR_EQUAL_TO(@"8.0")) {
            [WXComponentThread setQualityOfService:[[NSThread mainThread] qualityOfService]];
        } else {
            [WXComponentThread setThreadPriority:[[NSThread mainThread] threadPriority]];
        }
        
        [WXComponentThread start];
    });
    
    return WXComponentThread;
}

- (void)_runLoopThread
{
    [[NSRunLoop currentRunLoop] addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
    
    while (!_stopRunning) {
        @autoreleasepool {
            [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
        }
    }
}
```

### 后记

```
RunLoop 卡顿检测
实现原理

1 Runloop观察者：向主线程的Runloop添加观察者，监听其活动状态（如处理事件前、进入休眠等）。
2 信号量超时机制：在子线程中通过信号量等待超时来判断主线程是否卡顿。若多次超时，则判定为卡顿。
3 堆栈收集：当检测到卡顿时，收集主线程的调用堆栈以定位问题。

typedef CF_OPTIIONS(CFOptionFlags, CFRunLoopActivity) {
  KCFRunLoopEntry,
  KCFRunLoopBeforeTimers,
  KCFRunLoopBeforeSources, // 触发 Source0 回调（进入休眠前的状态）
  KCFRunLoopBeforeWaiting, // 即将进入休眠，等待 mach_port 消息
  KCFRunLoopAfterWaiting, // 接收 mach_port 消息 （唤醒线程后的状态）
  KCFRunLoopExit, // 推出 loop
  KCFRunLoopAllActivities // loop 所有状态改变
}

如果 RunLoop 的线程，进入睡眠前的方法的执行时间过长而导致无法进入睡眠，或者线程唤醒后接收消息时间过长而无法进入下发一步的话，就可以认为是线程受阻了。
如果这线程是主线程，表现出来的就是出现了卡顿。所以，如果我们利用 RunLoop 原理来监控卡顿的话，就要关注这两个阶段。
BeforeSources
AfterWaiting

进入休眠之前（BeforeWaiting） 会执行 source0 等方法，唤醒 AfterWaiting 后接收 mach_port 消息。
如果在执行 source 0 或者 接收 mach_port 消息的时候太耗时，那么就会卡顿。
  把 BeforeSources 作为执行 sources0 等方法的开始时间节点
  把 AfterWaiting 作为接收 mach_port 方法作为接收 mach_port 消息的开始时间节点
只要监控这两个状态是否超过设置的时间阈值。

如果监控 BeforeWaiting 状态，当监听到 BeforeWaiting 状态时，其实已经执行完了 source0，无法监控 source 0 的耗时。


实现步骤
1 创建Runloop观察者：监听主线程Runloop的所有活动。
2 记录活动状态：在观察者回调中记录当前Runloop状态，并通过信号量通知子线程。
3 子线程检测超时：子线程等待信号量，若超时且Runloop处于处理事件状态，则判定为卡顿。
4 收集堆栈信息：触发卡顿时，收集并上报主线程的调用堆栈。

---
还有：
1 使用 CADisplayLink 检测掉帧，在回调中记录每次刷新的时间戳，如果连续几帧没有触发回调，则表示主线程被阻塞，发生了卡顿。
2 子线程定时 ping 主线程，创建一个子线程，定期向主线程发送任务，然后等待主线程响应。如果超过一定时间主线程还没响应，就认为是卡顿。
3 在后台线程中定期采集主线程的堆栈信息（Backtrace）。如果连续多次采样显示主线程卡在某个或某几个函数上，则可判定为卡顿。

```

代码实现：
```objc
#import <Foundation/Foundation.h>

@interface LagMonitor : NSObject

+ (instancetype)sharedInstance;
- (void)startMonitoring;
- (void)stopMonitoring;

@end

@implementation LagMonitor {
    CFRunLoopObserverRef _observer;
    dispatch_semaphore_t _semaphore;
    NSInteger _timeoutCount;
    CFRunLoopActivity _activity;
}

+ (instancetype)sharedInstance {
    static LagMonitor *instance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[LagMonitor alloc] init];
    });
    return instance;
}

- (void)startMonitoring {
    if (_observer) {
        return;
    }
    
    // 初始化信号量
    _semaphore = dispatch_semaphore_create(0);
    
    // 设置Runloop观察者上下文
    CFRunLoopObserverContext context = {
        0,
        (__bridge void *)self,
        NULL,
        NULL
    };
    
    // 创建观察者，监听所有活动
    _observer = CFRunLoopObserverCreate(
        kCFAllocatorDefault,
        kCFRunLoopAllActivities,
        YES,
        0,
        &runLoopObserverCallBack,
        &context
    );
    
    // 将观察者添加到主线程的Runloop
    CFRunLoopAddObserver(
        CFRunLoopGetMain(),
        _observer,
        kCFRunLoopCommonModes
    );
    
    // 在子线程中监控卡顿
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        while (YES) {
            // 等待信号量，超时时间设为50毫秒
            long st = dispatch_semaphore_wait(
                self->_semaphore,
                dispatch_time(DISPATCH_TIME_NOW, 50 * NSEC_PER_MSEC)
            );
            
            if (st != 0) { // 超时
                // 判断Runloop是否在处理事件
                if (self->_activity == kCFRunLoopBeforeSources ||
                    self->_activity == kCFRunLoopAfterWaiting) {
                    
                    // 连续超时3次（累计150毫秒）判定为卡顿
                    if (++self->_timeoutCount < 3) {
                        continue;
                    }
                    
                    // 收集堆栈信息
                    [self collectStackInfo];
                }
            }
            self->_timeoutCount = 0;
        }
    });
}

- (void)stopMonitoring {
    if (!_observer) {
        return;
    }
    CFRunLoopRemoveObserver(
        CFRunLoopGetMain(),
        _observer,
        kCFRunLoopCommonModes
    );
    CFRelease(_observer);
    _observer = NULL;
}

// Runloop观察者回调
static void runLoopObserverCallBack(
    CFRunLoopObserverRef observer,
    CFRunLoopActivity activity,
    void *info
) {
    LagMonitor *monitor = (__bridge LagMonitor *)info;
    monitor->_activity = activity;
    
    // 发送信号量
    dispatch_semaphore_signal(monitor->_semaphore);
}

// 收集堆栈信息
- (void)collectStackInfo {
    NSArray *callSymbols = [NSThread callStackSymbols];
    NSLog(@"检测到卡顿：\n%@", callSymbols);
    // 此处可上报callSymbols到服务器
}

@end

```


## 总结 

使用 runloop 实现线程保活。要注意的地方主要有以下点:

- runloop 与 线程之间的关系
-  runloop 实现线程保活的原理，注意添加的那个 `port`
- runloop 的启动方式以及彼此之间的关联
- runloop 的退出方式和原理

欢迎交流指正。



## 参考文献

- [Threading Programming Guide -- Runloop](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1)
- [多线程与 Runloop](https://github.com/ShannonChenCHN/iOSDevLevelingUp/issues/16)
- [深入理解RunLoop](https://blog.ibireme.com/2015/05/18/runloop/)
- [深入研究 Runloop 与线程保活](https://twitter.com/intent/tweet?text=%E6%B7%B1%E5%85%A5%E7%A0%94%E7%A9%B6%20Runloop%20%E4%B8%8E%E7%BA%BF%E7%A8%8B%E4%BF%9D%E6%B4%BB%20%C2%BB&hashtags=&url=https://bestswifter.com/runloop-and-thread/)
- [AFNetworking3.0后为什么不再需要常驻线程？](https://www.jianshu.com/p/b5c27669e2c1)
