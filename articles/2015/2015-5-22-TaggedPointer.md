## Tagged Pointer

> 我看了 [唐巧的博客-深入理解Tagged Pointer](https://blog.devtang.com/2014/05/30/understand-tagged-pointer/) 自己做了个笔记，笔记可能不适合大众阅读，自己记录学习了。


### 1 未引入 Tagged Pointer
对象的指针大小一般是与机器字长有关，在32位系统中，一个指针的大小是32位（4字节），而在64位系统中，一个指针的大小64位（8字节）

所以，iOS 对象在 32位机器 -> 64位机器 内存占用会翻番。拿原文 `NSNumber` 对象为例：

```
32 位
|---------------------------     ----------------------------------|
|                          |     |                                 |
|  NSNumber 对象的指针 4byte | --> |NSNumber 对象的值 4byte + isa 4byte|
|                          |     |                                 |
|---------------------------     ----------------------------------|
                           12 字节

64 位
|---------------------------     ----------------------------------|
|                          |     |                                 |
|  NSNumber 对象的指针 8byte | --> |NSNumber 对象的值 8byte + isa 8byte|
|                          |     |                                 |
|---------------------------     ----------------------------------|
                           24 字节
```


### 2 Tagged Pointer
由于 `NSNumber` `NSDate` 一类的变量本身的值需要占用的内存大小常常不需要8个字节，拿整数来说，4个字节所能表示的有符号整数就可以达到 `2^31=2147483648` 对于绝大多数情况都是而已处理的。当 8 字节可以承载用于表示的数值时，系统就会以Tagged Pointer的方式生成指针，如果 8 字节承载不了时，则又用以前的方式来生成普通的指针。所以，引入 Tagged Pointer 对象以后，64 位在CPU下 `NSNumber` 的内存图变成：

```
32 位
|---------------------------     ----------------------------------|
|                          |     |                                 |
|  NSNumber 对象的指针 4byte | --> |NSNumber 对象的值 4byte + isa 4byte|
|                          |     |                                 |
|---------------------------     ----------------------------------|
                           12 字节

64 位
|---------------------------|
|                           |
|  NSNumber 对象特殊标记      |  Tag
|                           |
|---------------------------|
|                           |
|  NSNumber 对象的数据       |   Data
|                           |
|---------------------------|
          8 字节
```

#### 2.1 特点
- Tagged Pointer 专门用来存储**小的对象**
- Tagged Pointer 指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。**所以，它的内存并不存储在堆中，也不需要 malloc 和 free**。
- Tagged Pointer **没有 isa 指针**



### 3 效率

不使用 Tagged Pointer 的情况下：

- NSNumber 对象需要动态分配内存、维护引用计数、管理它的生命周期等

- 方法调用需要 objc_msgSend 的执行流程（消息发送、动态方法解析、消息转发）

使用 Tagged Pointer 的情况下：

- objc_msgSend 先识别 是否为 Tagged Pointer，
  - 若是，直接返回 不进行其他流程；
  - 若不是，进行其他流程（消息发送、动态方法解析、消息转发） 。
- 节省了调用开销



#### 推荐文章[iOS内存管理之Tagged Pointer](https://www.infoq.cn/article/r5s0budukwyndafrivh4)

