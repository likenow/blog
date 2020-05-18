---
title: OC 中的方法“重写”小结（二）
date: 2015-04-30 18:01:51
categories:
- OC
tags:
- OC
---
## 写在前面的话

在之前写的[OC 中的方法“重写”小结（一）](http://kai-lee.com/2015/04/29/%E5%B0%8F%E7%BB%93OC%E4%B8%AD%E7%9A%84%E6%96%B9%E6%B3%95%E9%87%8D%E5%86%99/)一文中介绍了重写某些方法的知识点，而且了解到在实际的开发中，为了方便，经常会自定义某些方法，也就是说会重写某些方法。比如，在 OC 的**继承**中，**子类**可以**重写**其**父类**的方法，还有我们可以重写**构造方法**等。今天我继续总结 OC 中重写方法的知识点: **dealloc** 方法和 **description** 方法。希望对读者有所帮助。
<!--more-->

##  description 方法

### description 方法与 NSLog函数

description 方法用来**输出**对象的属性，这不得不让我们联想到 0C 的 **NSLog** 函数，因为我们可用 NSLog 函数来输出字符串和一些基本数据类。其实，它除了可以输出基本数据类型，NSLog 函数还可以输出任何 OC 对象。口说无凭，好吧让你心服口服：

    Student *stu = [[Student alloc] initWithAge:10];
    
    NSLog(@"%@", stu); // 输出：<Student: 0xffc0>   


用 NSLog 函数输出 stu 对象，注意左边的格式符 %@ ，以后想输出OC对象，就得用 %@ 这个格式符。 NSLog 函数一旦发现用 %@ 输出某个 OC 对象时，就会调用这个对象的 description 方法(这个方法返回值是 NSString 类型，是 OC 中的字符串类型)，并且将 description 方法返回的字符串代替 %@ 的位置进行输出。而且 %@ 只能用于输出 OC 对象，不能输出结构体等其他类型。 description 方法的默认实现是返回这样的格式：

    <类名: 对象的内存地址>

### 重写 description 方法

description 方法的默认实现是返回类名和对象的内存地址，很多时候我们并不关心对象的内存地址，而是比较关心对象内部的一些成变量的值。因此，重写 description 方法，覆盖 description 方法的默认实现就诞生了。比如，重写 Student 的 description 方法，返回成员变量 _age 的值。

    // 重写description方法
    - (NSString *)description 
    {
        // 调用了NSString这个类的静态方法stringWithFormat初始化一个字符串对象，并返回这个字符串
        return [NSString stringWithFormat:@"age=%d", _age];
    }


    
    // 接，上例测试一下
    Student *stu = [[Student alloc] initWithAge:10];
    
    NSLog(@"%@", stu); //  age=10


有一点要注意的是：不要在 description 方法中同时使用 %@ 和 self ，如果同时使用代表要调用 self 的 description 方法，因此最终会导致程序陷入死循环，循环调用 description 方法下面的写法是错误的：

    // 错误写法，引以为戒
    - (NSString *)description 
    {
        return [NSString stringWithFormat:@"%@", self];
    }


## dealloc 方法

这个 **dealloc 方法**要牵扯到 0C 的**内存管理**部分了，之前也稍微提到了内存管理也是一带而过，今天同样也不想花太多的精力讲这个东西。我避开他并不是说它难到学不会，只不过有了 ARC(Automatic Reference Counting) 之后，就缓解了程序员手动管理内存的痛苦，而且现在开发中大都是在用 ARC 来做开发，你怎么写都不用担心内存泄露问题。所以，并不像用我拙劣的语言来叙述手动管理内存。如果非要提一下的话就送大家“四个谁真言”。如果非要解释一下的话，就把它留到下一篇博文里了！呵呵呵······
> 谁创建，谁release；谁retain，谁release

好了，转过头来，我们来看 dealloc 方法。当一个对象被销毁时，系统会自动向对象发送一条 dealloc 消息。而，我们一般会重写dealloc方法，在这里释放相关资源，dealloc就像对象的遗言。一旦重写了dealloc方法，就必须调用 `[super dealloc]` ，并且放在最后面调用，不要直接调用dealloc方法。

### 重写 dealloc 方法

    // 当一个student对象被回收就会调用这个方法
    - (void)dealloc
    {
        // 当student对象被回收就会打印这一句
        NSLog(@"Student will be reclaimed");
    
        // super的dealloc一定要调用，且放在最后面
        [super dealloc];
    }



## 写在最后的话
希望我的博文可以给看到文章最后的您带来些许的帮助，同时文中的不足，还请加以指正，谢谢！在接下来的博文中，我将进一步总结一下**扩充类的方法**这一知识点，敬请期待！

## 参考文献
- [07-自定义构造方法和description方法](http://www.cnblogs.com/mjios/archive/2013/04/19/3031412.html#label1)
- [手把手教你ARC——iOS/Mac开发ARC入门和使用](http://www.onevcat.com/2012/06/arc-hand-by-hand/)
- [转向ARC的说明](http://www.cocoachina.com/industry/20131209/7497.html)
