### iOS 关于程序执行时间

[toc]

### 1 OC 获取时间

#### 1.1 类和方法

1. `NSDate` <- `Foundation` 

2. `CFAbsoluteTimeGetCurrent()` <- `CoreFoundation`

3. `CACurrentMediaTime()` <- `QuartzCore`

4. `mach_absolute_time()` 函数

   ```c
   /*
   通过一个均匀变化的属性值来描述时间，而在我们的iPhone上刚好有一个这样的值存在，就是CPU的时钟周期数（ticks）。
   这个tick的数值可以用来描述时间，而 mach_absolute_time() 返回的就是CPU已经运行的tick的数量。将这个tick数经过一定的转换就可以变成秒数，或者纳秒数，这样就和时间直接关联了。
   不过这个tick数，在每次手机重启之后，会重新开始计数，而且iPhone锁屏进入休眠之后tick也会暂停计数。
   mach_absolute_time()不会受系统时间影响，只受设备重启和休眠行为影响。
   */
   ```

   

5. `gettimeofday` 函数 Unix/Linux 下的标准

   ```c
    #include <sys/time.h>
   
   int gettimeofday(struct timeval *restrict tp, void *restrict tzp);
   // NSDate，CFAbsoluteTimeGetCurrent()一样，都是受当前设备的系统时间影响。只不过是参考的时间基准点不一样而已。Unix time是以UTC（Coordinated Universal Time ）标准时间 1970年1月1号 00：00：00为基准时间，当前时间距离基准点偏移的秒数。
   
   
   struct timeval now;
   struct timezone tz;
   gettimeofday(&now, &tz);
   NSLog(@"gettimeofday: %ld", now.tv_sec);
   ```

6. `sysctl` 函数

   ```c
   #include <sys/sysctl.h>
   // iOS 系统记录了上次设备重启的时间。可以通过如下API调用获取：
   - (long)bootTime
   {
   #define MIB_SIZE 2
       int mib[MIB_SIZE];
       size_t size;
       struct timeval  boottime;
       
       mib[0] = CTL_KERN;
       mib[1] = KERN_BOOTTIME;
       size = sizeof(boottime);
       if (sysctl(mib, MIB_SIZE, &boottime, &size, NULL, 0) != -1)
       {
           return boottime.tv_sec;
       }
       return 0;
   }
   // 返回的值是上次设备重启的Unix time。
   // 这个API返回的值也会受系统时间影响，用户如果修改时间，值也会随着变化。
   
   ```

   


> `NSDate` 或 `CFAbsoluteTimeGetCurrent()` 返回的时钟时间将会会网络时间同步，从时钟偏移量的角度，`mach_absolute_time() `和 `CACurrentMediaTime()` 是基于内建时钟的，能够更精确更原子化地测量，并且不会因为外部时间变化而变化（例如时区变化、夏时制、秒突变等），但它和系统的`uptime`有关,系统重启后`CACurrentMediaTime()`会被重置。
>
> 上面提到的`NSDate`，`CFAbsoluteTimeGetCurrent`，`gettimeofday`，`sysctl`都是跟随系统时间变化的，`mach_absolute_time`和`CACurrentMediaTime`虽然是依据CPU时钟数，不受系统时间影响，但在休眠和重启的时候还是会被影响。
>
> `uptime` 是系统重启后到目前为止的时间间隔，这个时间级别是内核时间。



> [UNIX 时间](https://zh.wikipedia.org/wiki/UNIX%E6%97%B6%E9%97%B4) 维基百科
>
> 从[UTC](https://zh.wikipedia.org/wiki/協調世界時)1970年1月1日0时0分0秒起至现在的总秒数，不考虑[闰秒](https://zh.wikipedia.org/wiki/閏秒)[[1\]](https://zh.wikipedia.org/wiki/UNIX时间#cite_note-1)。
>
> #### Unix负时间导致部分iPhone手机无法启动[[编辑](https://zh.wikipedia.org/w/index.php?title=UNIX时间&action=edit&section=4)]
>
> 在2016年2月12日，据披露，如果把苹果[iPhone](https://zh.wikipedia.org/wiki/IPhone)、[iPad](https://zh.wikipedia.org/wiki/IPad)等设备的系统时间设置为1970年1月1日，随后重启设备，它会无法正常启动。目前苹果公司正式承认了漏洞存在，但是尚未公布具体的引发原因。
>
> [[2\]](https://zh.wikipedia.org/wiki/UNIX时间#cite_note-2)部分中国大陆用户猜想这是因为调整当地时间到1970.1.1 0:00后，如果时区为正，那么GMT时间就早于UNIX定义的0时间。例如北京时间 1970.1.1 0:00 （UTC+0800） 是UTC 1969.12.31 16:00 对应的UNIX时间是负的。但是有人回应尝试设为正时区重启后仍然无法正常启动。
>
> [[3\]](https://zh.wikipedia.org/wiki/UNIX时间#cite_note-3)苹果对此采用的策略是在随后的固件更新中将时间禁止调整到2000年以前。



#### 1.2 常见用法

`NSDate`、`CFAbsoluteTimeGetCurrent()`常用于日常时间、时间戳的表示，与服务器之间的数据交互

其中 `CFAbsoluteTimeGetCurrent()` 相当于`[[NSDate data] timeIntervalSinceReferenceDate]`;

`CACurrentMediaTime() `常用于测试代码的效率。

##### 1.2.1 准确时间

对于一些秒杀、签到、一时间为基础做任务的业务需求对于时间的保真要求非常高。但是客户端的系统时间能够被用户修改，因此我们需要有一些措施来防止拿到的数据时被恶意修改的时间，减少对业务的破坏。

- 系统启动后到当前的时间间隔是无法被外界修改的，因此我们可以把这个时间和服务器拿到的时间作为准确时间来计算要使用的时间。
- 拿到服务器的时间 T0，此时获取到的 uptime 时间 T1
- 在业务使用的时间点，获取uptime T2，则 T2-T1 就是间隔时间，与服务端 T0 + T2 - T1 应该相等！

```c
// uptimeh获取系统重启后的至今运行的时间
func uptime() -> time_t {
  var boottime = timeval();
  var mib: [Int32] = [CTL_KERN, KERN_BOOTTIME];
  var size = MemoryLayout.stride(ofValue:timeval());
  var now = time_t();
  var uptime:time_t = -1;
  time(&now);
  if(sysctl(&mib,2, &boottime, &size,nil,0) !=-1 &&  boottime.tv_sec!=0) {
    uptime = now - boottime.tv_sec;
  }
  return uptime;
}
```



##### 1.2.2 代码块执行时间

```objective-c
- (void)test {
  CGFloat beginTime = CACurrentMediaTime();
  // do something
  CGFloat endTime = CACurrentMediaTime();
  NSInteger costTime = (NSInteger)((endTime - beginTime)*1000+0.5);//四舍五入 ms
  NSLog(@"beginTime == %f endTime == %f costTime == %f",beginTime,endTime,costTime);   
}
```



### 2 NSTimer

> Timers work in conjunction with run loops. Run loops maintain strong references to their timers, so you don’t have to maintain your own strong reference to a timer after you have added it to a run loop.

NSRunloop 对该定时器会有一个强引用，所以我们不对 NSRunloop 中的定时器进行强引用。



> `- (void)invalidate;`
>
> This method is the only way to remove a timer from an `NSRunLoop` object. The `NSRunLoop` object removes its strong reference to the timer, either just before the `invalidate`  method returns or at some later point.
>
> If it was configured with target and user info objects, the receiver removes its strong references to those objects as well.

- 把 timer 从 NSRunloop 移除
- 释放 `target` 对象的强引用

```

Timer →1→ Target
  ↑   ←3← 
  2
  ↑
RunLoop      

```

1 timer 强引用 target

2 RunLoop 强引用 timer

3 target 通常会强持有 timer 做定时任务

在开发中我们可能会遇到某些需求，只有在 customClock 对象要被释放时才去释放timer（此处要注意释放的先后顺序及释放条件），如果提前向timer发送了`invalidate`消息，那么 customClock 对象可能会因为timer被提前释放而导致数据错了，就像闹钟失去了`秒针`一样，就无法正常工作了。所以我们要做的是在向 customClock 对象发送`dealloc`消息前在给timer发送`invalidate`消息，从而避免本末倒置的问题。这种情况就像一个死循环（因为如果不给timer发送`invalidate`消息， customClock 对象根本不会被销毁，dealloc方法根本不会执行），那么该怎么做呢？

##### 2.1 iOS 10 开始，系统新增了 block 形式的初始化方式：

```objective-c
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:0.25 repeats:YES block:^(NSTimer * _Nonnull timer) {
    NSLog(@"TestTimerBlockController timer start");
}];

[[NSRunLoop mainRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
self.timer = timer;
```



##### 2.2 Proxy

[NSProxy](https://developer.apple.com/documentation/foundation/nsproxy?language=objc) 替身😝

> An abstract superclass defining an API for objects that act as **stand-ins** for other objects or for objects that don’t exist yet.

```objective-c
@interface TestTimerProxy : NSProxy

// 注意此处weak
@property (weak, nonatomic, readonly) id target;

+ (instancetype)timerProxyWithTarget:(id)target;

- (instancetype)initWithTarget:(id)target;

@end
@implementation TestTimerProxy
- (instancetype)initWithTarget:(id)target {
    _target = target;
    return self;
}

+ (instancetype)timerProxyWithTarget:(id)target {
    return [[TestTimerProxy alloc] initWithTarget:target];
}

- (id)forwardingTargetForSelector:(SEL)selector {
    return _target;
}

- (void)forwardInvocation:(NSInvocation *)invocation {
    void *null = NULL;
    [invocation setReturnValue:&null];
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)selector {
    return [NSObject instanceMethodSignatureForSelector:@selector(init)];
}

- (BOOL)respondsToSelector:(SEL)aSelector {
    return [_target respondsToSelector:aSelector];
}

- (BOOL)isEqual:(id)object {
    return [_target isEqual:object];
}

- (NSUInteger)hash {
    return [_target hash];
}

- (Class)superclass {
    return [_target superclass];
}

- (Class)class {
    return [_target class];
}

- (BOOL)isKindOfClass:(Class)aClass {
    return [_target isKindOfClass:aClass];
}

- (BOOL)isMemberOfClass:(Class)aClass {
    return [_target isMemberOfClass:aClass];
}

- (BOOL)conformsToProtocol:(Protocol *)aProtocol {
    return [_target conformsToProtocol:aProtocol];
}

- (BOOL)isProxy {
    return YES;
}

- (NSString *)description {
    return [_target description];
}

- (NSString *)debugDescription {
    return [_target debugDescription];
}

@end
```

```objective-c
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:0.25 target:[TestTimerProxy timerProxyWithTarget:self] selector:@selector(startTimer) userInfo:nil repeats:YES];

[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];

self.timer = timer;
```



推荐文章 [浅析NSTimer & CADisplayLink内存泄漏](https://tingxins.com/2016/11/NSTimer-CADisplayLink-leaks/)



##### 2.3 我们创建的 Timer 默认情况下是添加到当前 RunLoop 的 default 模式下，这个时候如果切换到 TrackingRunLoopMode 上面，定时器就不会在生效了

解决方案：可以通过 CFRunLoopAddTimer 函数把 NSTimer 添加到当前 RunLoop 的 `commonMode` 当中，前面讲过 commonMode 不是实际的 mode，它只是把一些 mode 打上一个 common 的标记，然后可以把某一个事件源比如说 timer 同步到多个 mode 当中。

```objective-c
NSRunLoop* myRunLoop = [NSRunLoop currentRunLoop];
 
// Create and schedule the first timer.The first timer has an initial delay of 1 second but then fires regularly every 0.1 seconds after that. 
NSDate* futureDate = [NSDate dateWithTimeIntervalSinceNow:1.0];
NSTimer* myTimer = [[NSTimer alloc] initWithFireDate:futureDate
                        interval:0.1
                        target:self
                        selector:@selector(myDoFireTimer1:)
                        userInfo:nil
                        repeats:YES];
[myRunLoop addTimer:myTimer forMode:NSRunLoopCommonModes];
 
// Create and schedule the second timer. The second timer begins firing after an initial 0.2 second delay and then fires every 0.2 seconds after that.
[NSTimer scheduledTimerWithTimeInterval:0.2
                        target:self
                        selector:@selector(myDoFireTimer2:)
                        userInfo:nil
                        repeats:YES];
```



```c
CFRunLoopRef runLoop = CFRunLoopGetCurrent();
CFRunLoopTimerContext context = {0, NULL, NULL, NULL, NULL};
CFRunLoopTimerRef timer = CFRunLoopTimerCreate(kCFAllocatorDefault, 0.1, 0.3, 0, 0,
                                        &myCFTimerCallback, &context);
 
CFRunLoopAddTimer(runLoop, timer, kCFRunLoopCommonModes);

```

推荐文章 [Run Loops](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1)
