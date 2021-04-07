---
title: Autorelease Pool不应该停留在知道（二）
date: 2018-12-20 21:03:54
tags:
- autorelease pool
- iOS
categories:
- iOS
---

## 前言

之前总结过一篇文章[Autorelease Pool不应该停留在知道](https://dnduuhn.com/2018/11/18/AutoreleasePool%E4%B8%8D%E5%BA%94%E8%AF%A5%E5%81%9C%E7%95%99%E5%9C%A8%E7%9F%A5%E9%81%93/)

但是没想到我还是停留在了不知道的境地~~~~脸打的piapia地。只怪自总结学习不到位

<!--more-->

## 看下这道题

这段代码摘自[Objective-C Autorelease Pool 的实现原理](http://blog.leichunfeng.com/blog/2015/05/31/objective-c-autorelease-pool-implementation-principle/)

```objective-c
__weak NSString *string_weak_ = nil;

- (void)viewDidLoad {
    [super viewDidLoad];

    // 场景 1
    NSString *string = [NSString stringWithFormat:@"leichunfeng"];
    string_weak_ = string;

    // 场景 2
//    @autoreleasepool {
//        NSString *string = [NSString stringWithFormat:@"leichunfeng"];
//        string_weak_ = string;
//    }

    // 场景 3
//    NSString *string = nil;
//    @autoreleasepool {
//        string = [NSString stringWithFormat:@"leichunfeng"];
//        string_weak_ = string;
//    }

    NSLog(@"string: %@", string_weak_);
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    NSLog(@"string: %@", string_weak_);
}

- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    NSLog(@"string: %@", string_weak_);
}
```

### 打印结果

我在 `Version 10.1 (10B61)`、`iOS 12.1` 模拟器 `iPhoneXR`上跑出的结果：

```objective-c
// 场景1
2018-12-20 21:10:25.755731+0800 AutoreleasePool[5763:233059] string: leichunfeng
2018-12-20 21:10:25.755987+0800 AutoreleasePool[5763:233059] string: leichunfeng
2018-12-20 21:10:25.768497+0800 AutoreleasePool[5763:233059] string: leichunfeng
// 场景2
2018-12-20 21:13:24.017458+0800 AutoreleasePool[5869:236206] string: leichunfeng
2018-12-20 21:13:24.017681+0800 AutoreleasePool[5869:236206] string: leichunfeng
2018-12-20 21:13:24.029524+0800 AutoreleasePool[5869:236206] string: leichunfeng
// 场景3    
2018-12-20 21:14:09.505997+0800 AutoreleasePool[5911:237157] string: leichunfeng
2018-12-20 21:14:09.506218+0800 AutoreleasePool[5911:237157] string: leichunfeng
2018-12-20 21:14:09.520660+0800 AutoreleasePool[5911:237157] string: leichunfeng    
```

## 分析一下为什么是上述打印

定义了一个`__weak`类型的全局字符串变量`string_weak_`指向通过 `[NSString stringWithFormat:@"leichunfeng"]` 创建的一个 **autoreleased** 对象。然后分别在

1 `viewDidLoad`
2 `viewWillAppear`
3 `viewDidAppear`

打印`string_weak_`

### 3种场景：

#### 场景 1

当使用 `[NSString stringWithFormat:@"leichunfeng"]` 创建一个对象时，这个对象的引用计数为 1 ，并且这个对象被系统自动添加到了当前的 **autoreleasepool** 中。

当使用局部变量 `string` 指向这个对象时，这个对象的引用计数 `+1` ，变成了` 2` 。因为在 **ARC** 下 `NSString *string` 本质上就是 `__strong NSString *string` 。所以在 `viewDidLoad` 方法返回前，这个对象是一直存在的，且引用计数为 `2` 。而当 `viewDidLoad` 方法返回时，局部变量 `string` 被回收，指向了 `nil` 。因此，其所指向对象的引用计数` -1` ，变成了 `1` 。

而在 `viewDidLoad` `viewWillAppear` `viewDidAppear` 3个方法中，我们仍然可以打印出这个对象的值，说明这个对象并没有被释放。前面我们提到了，这个对象是一个 **autoreleased** 对象，**autoreleased** 对象是被添加到了当前最近的 **autoreleasepool** 中的，只有当这个 **autoreleasepool** 自身 `drain` 的时候，**autoreleasepool** 中的 `autoreleased` 对象才会被` release` 。

#### 场景 2

同理，当通过 `[NSString stringWithFormat:@"leichunfeng"]` 创建一个对象时，这个对象的引用计数为` 1` 。而当使用局部变量 `string` 指向这个对象时，这个对象的引用计数 `+1` ，变成了` 2` 。而出了当前作用域时，局部变量 `string` 变成了 `nil` ，所以其所指向对象的引用计数变成 `1` 。另外，我们知道当出了 `@autoreleasepool {}` 的作用域时，其中的` autoreleased` 对象仍然没有` release `。

#### 场景 3

同理，当出了 `@autoreleasepool {}` 的作用域时，其中的 autoreleased 对象被 release ，对象的引用计数变成 1 。当出了局部变量 `string` 的作用域，即 `viewDidLoad` 方法返回时，`string` 指向了 `nil` ，其所指向对象的引用计数变成 0 ，对象最终被释放。

其中，场景 1 出现得最多，就是不需要我们手动添加 `@autoreleasepool {}` 的情况，直接使用系统维护的 **autoreleasepool** ；场景 2 就是需要我们手动添加 `@autoreleasepool {}` 的情况；场景 3 是为了区别场景 2 而引入的，在这种场景下并不能达到出了 `@autoreleasepool {}` 的作用域时 `autoreleased` 对象被释放的目的。

**Ps.在当前的实验环境下，场景2和场景3 完全说明了，出了 `@autoreleasepool {}` 的作用域时 `autoreleased` 对象并没有被释放。**

那么问题其实就到了，**autoreleasepool** 什么时候 `drain` ？Autorelease对象什么时候释放？

### About Autorelease Pool Blocks

An autorelease pool block is marked using `@autoreleasepool`, as illustrated in the following example:

```objective-c
@autoreleasepool {
    // Code that creates autoreleased objects.
}
```


At the end of the autorelease pool block, objects that received an `autorelease` message within the block are sent a `release` message—an object receives a `release` message for each time it was sent an `autorelease` message within the block.

这个说的，很含蓄啊：`Autorelease Pool Blocks` 中的对象在收到 `autorelease` 消息的时候，调用对象的`release`。**那么是么时候发送 `autorelease message`?**

### NSAutoreleasePool

>In a reference-counted environment (as opposed to one which uses garbage collection), an `NSAutoreleasePool` object contains objects that have received an [`autorelease`](apple-reference-documentation://hcpT-2JClH) message and when drained it sends a [`release`](apple-reference-documentation://hcsoHwQn1z) message to each of those objects. <font color=#DAA520 face="微软雅黑"> Thus, sending [`autorelease`](apple-reference-documentation://hcpT-2JClH) instead of [`release`](apple-reference-documentation://hcsoHwQn1z) to an object extends the lifetime of that object at least until the pool itself is drained (it may be longer if the object is subsequently retained). </font> An object can be put into the same pool several times, in which case it receives a [`release`](apple-reference-documentation://hcsoHwQn1z) message for each time it was put into the pool. 
>
>In a reference counted environment, Cocoa expects there to be an autorelease pool always available. If a pool is not available, autoreleased objects do not get released and you leak memory. In this situation, your program will typically log suitable warning messages.
>
> <font color=#DAA520 face="微软雅黑">The Application Kit creates an autorelease pool on the main thread at the beginning of every cycle of the event loop, and drains it at the end, thereby releasing any autoreleased objects generated while processing an event.</font> If you use the Application Kit, you therefore typically don’t have to create your own pools. If your application creates a lot of temporary autoreleased objects within the event loop, however, it may be beneficial to create “local” autorelease pools to help to minimize the peak memory footprint.
>
>You create an `NSAutoreleasePool` object with the usual `alloc` and `init` messages and dispose of it with [`drain`](apple-reference-documentation://hclA50KeES) (or [`release`](apple-reference-documentation://tc1807014)—to understand the difference, see [Garbage Collection](apple-reference-documentation://hcKbUO8Joq#1651537)). Since you cannot retain an autorelease pool (or autorelease it—see [`retain`](apple-reference-documentation://tc1807026) and [`autorelease`](apple-reference-documentation://tc1807021)), draining a pool ultimately has the effect of deallocating it. You should always drain an autorelease pool in the same context (invocation of a method or function, or body of a loop) that it was created. See [Using Autorelease Pool Blocks](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html#//apple_ref/doc/uid/20000047) for more details.
>
>Each thread (including the main thread) maintains its own stack of `NSAutoreleasePool` objects (see [Threads](apple-reference-documentation://hcKbUO8Joq#1651513)). As new pools are created, they get added to the top of the stack. When pools are deallocated, they are removed from the stack. Autoreleased objects are placed into the top autorelease pool for the current thread. When a thread terminates, it automatically drains all of the autorelease pools associated with itself.



### Threads

> If you are making Cocoa calls outside of the Application Kit’s main thread—for example if you create a Foundation-only application or if you detach a thread—you need to create your own autorelease pool.
>
> If your application or thread is long-lived and potentially generates a lot of autoreleased objects, you should periodically drain and create autorelease pools (like the Application Kit does on the main thread); otherwise, autoreleased objects accumulate and your memory footprint grows. If, however, your detached thread does not make Cocoa calls, you do not need to create an autorelease pool.



### NSRunloop

> **Overview**
>
> A `NSRunLoop` object processes input for sources such as mouse and keyboard events from the window system, [`NSPort`](apple-reference-documentation://hcIJBEuKY7) objects, and [`NSConnection`](apple-reference-documentation://hcUQrCw1bC) objects. A `NSRunLoop` object also processes [`NSTimer`](apple-reference-documentation://hcaODPrPmP) events.
>
> Your application neither creates or explicitly manages `NSRunLoop` objects. Each [`NSThread`](apple-reference-documentation://hccRCghG_a) object—including the application’s main thread—has an `NSRunLoop` object automatically created for it as needed. If you need to access the current thread’s run loop, you do so with the class method [`currentRunLoop`](apple-reference-documentation://hc7RgsSCrv).
>
> Note that from the perspective of `NSRunLoop`, [`NSTimer`](apple-reference-documentation://hcaODPrPmP) objects are not "input"—they are a special type, and one of the things that means is that they do not cause the run loop to return when they fire.
>
> Warning
>
> The `NSRunLoop` class is generally not considered to be thread-safe and its methods should only be called within the context of the current thread. You should never try to call the methods of an `NSRunLoop` object running in a different thread, as doing so might cause unexpected results.

### 小结

从上述文档中，找到了答案。 `Application Kit` 在每个 `event loop` 开始时前，在主线程上创建一个自动释放池，并在最后`drains`，从而释放处理事件时生成的任何自动释放的对象。

我们上面提到的场景1中创建的 `autoreleased` 对象就是被系统添加到了自动创建的` autoreleasepool` 中，并在这个 `autoreleasepool` 被 `drain` 时得到释放。同理 场景2 3 通过` Autorelease Pool Blocks` 创建的自动式释放池也是被 `drain` 时得到释放。

上述 场景 1 2 3 打印相同。我的结论是：**打印操作在一个 `runloop` 中，这个`runloop` 没有结束，系统不会发送 `autorelease 消息`。`autorelease pool` 就不会 `drain`。**



## 参考文献

- [Objective-C Autorelease Pool 的实现原理](http://blog.leichunfeng.com/blog/2015/05/31/objective-c-autorelease-pool-implementation-principle/)
- [Autorelease Pool不应该停留在知道](https://dnduuhn.com/2018/11/18/AutoreleasePool%E4%B8%8D%E5%BA%94%E8%AF%A5%E5%81%9C%E7%95%99%E5%9C%A8%E7%9F%A5%E9%81%93/)
- [About Memory Management](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)
- [Using Autorelease Pool Blocks](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html#//apple_ref/doc/uid/20000047-CJBFBEDI)