---
title: C 语言学习笔记四
date: 2014-05-27 15:59:51
categories:
- C
tags:
- 结构体
- C
---
## 写在前面的话

上一篇博文[C 语言学习笔记三](http://kai-lee.com/2015/04/26/C%E8%AF%AD%E8%A8%80%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E4%B8%89/#more)我曾引用这样一句话：指针是 C 语言中非常重要的数据类型，如果你说C语言中除了指针，其他你都学得很好，那你干脆说没学过C语言。对于**结构体**可以这样说：结构体是 C 语言中非常重要的数据类型，如果你说结构体你没有掌握，那我问你就究竟掌握了什么？呵呵，说真的结构体不难掌握，令人欣喜的是很少人反应结构体如何如何晦涩难懂。
C 使用结构体这一复杂数据类型增强了它表示数据的能力，它的出现足已灵活地表示多种数据。这一篇我总结一下我对结构体的学习，可以说结构体的学习是接下来学习 OC 的非常重要的一环，它的重要性同样不亚于指针，而且掌握结构体对于我们这些热爱编程的“程序猿”来说就是小菜一碟。但是，我们不该轻敌，这一技能我们志在必得。现就结构体数据类型的学习做一下总结，希望对那些刚开始学习结构体的人提供些许的帮助。
<!--more-->

##  定义结构体

### 定义形式

    struct　结构体名{
        
        类型名1　成员名1;        
        类型名2　成员名2;        
        ……        
        类型名n　成员名n;　　　
        
    };

**Struct** 是关键字，是结构体类型的标志。结束花括号后面的分号表示结构设计定义的结束。可以把这个声明放在任何函数的外面，也可以放在一个函数定义内部。如果这个结构体声明置于一个函数内部，它的标记只能在该函数内部使用。如果是外部声明，它可以被本文件中该声明之后的所有函数使用。定义结构体类型并不分配存储空间，只有当定义属于结构体类型的变量时，系统才会分配存储空间给该变量。

## 定义结构体变量

结构体类型变量的定义与其它类型的变量的定义是一样的，但由于结构体类型需要针对问题事先自行定义，所以结构体类型变量的定义形式就增加了灵活性，共计有3种形式，分别介绍如下：

- 先定义结构体类型，再定义变量


    // 定义结构体类型   
    struct Person {
        char *name;
        int age;
    };
    // 定义结构体变量
    struct Person p;


- 定义结构体类型的同时定义变量


    struct Person {
        char *name;
        int age;
    } p;


- 直接定义结构体类型变量，省略类型名


    struct {
        char *name;
        int age;
    } p;


至于上述三种方式孰优孰劣一试便知毋庸多言，而且在使用了 typedef 之后会更加省劲儿。

## 结构体的注意事项

- 不允许对结构体本身递归定义


    struct Person {
        int age;
        struct Person p;
    };


- 结构体可以嵌套使用


    struct Date {
        int year;
        int month;
        int day;
    };
    
    struct Person {
        char *name;
        // 嵌套使用
        struct Date birthday;
    };


## 结构体的初始化


    struct Person {
        char *name;
        char grade;
    };
    
    struct Person p = {"kai", 'A'};


只能在定义变量的时候初始化，也就是说初始化变量和变量的定义不能分开。结构体变量占用的内存空间是其最大成员字节数的倍数，而且各成员在内存中按定义的顺序依次排列。比如，上述代码定义了两个变量，他所占的内存空间应该是指针类型的 name 字节数的2倍。内存分析：

![](../assets/bianliangcunchu5.png)

## 访问结构体成员

结构体就像是一个**“升级版的数组”**。在它内部，一个成员可以是 char 类型，下一个可以是 float 类型，再下一个可以是 int 类型，甚至下下个可以是结构体类型。那么如何访问结构体中的各个成员呢？用结构体成员运算符点（.）就可以。例如：
​    
​    struct Date {
​        int year;
​        int month;
​        int day;
​    };
​    
    struct Person {
        char *name;
        // 嵌套使用
        struct Date birthday;
    };
    
    struct Person p;
    
    p.birthday.year = 1992;
    p.birthday.month = 01;
    p.birthday.day  = 19;

此外，相同类型的结构体变量之间可以进行整体赋值

    struct Person {
        char *name;
        int age;
    };
    
    struct Person p1 = {"kailee", 24};
    
    // 将p1直接赋值给p2
    struct Person p2 = p1;
    
    printf("age is %d", p2.age);

## 结构体数组

结构体类型，注意它也是一种数据类型，只不过比起 int 或者 float 复杂一点而已，所以，既然可以有整形数组，浮点型数组，那么结构体类型数组也就见怪不怪了吧。结构体数组的定义和访问结合上边结构体变量的定义和访问来对比学习。这里不再赘述。

## 结构体作为函数参数

结构体变量作为函数参数进行传递时，其实传递的是全部成员的值，也就是将实参中成员的值一一赋值给对应的形参成员。因此，形参的改变不会影响到实参。

## 指向结构体的指针
- 每个结构体变量都有自己的存储空间和地址，因此指针也可以指向结构体变量
- 结构体指针变量的定义形式：struct 结构体名称 *指针变量名
- 有了指向结构体的指针，那么就有3种访问结构体成员的方式
    - 结构体变量名.成员名
    - (*指针变量名).成员名
    - 指针变量名->成员名


## 写在最后的话
不知不觉 C 语言学习笔记系列已经进行到第四篇，我接下来的安排是开始总结 OC 学习中的一些知识要点，当然并不是说 C 语言就不再学习了，NO! 有了 C 语言的铺垫接下来进行 OC 的学习将会更加得心应手！希望我的博文可以给看到文章最后的您带来些许的帮助，谢谢！敬请期待！

## 参考文献
- [【C语言】21-结构体](http://www.cnblogs.com/mjios/archive/2013/03/24/2977910.html)
- [美]Prate,S. C Primer Plus[M]. 第5版. 云巅工作室，译.
- [关于结构体数据存储的对齐问题](http://blog.chinaunix.net/uid-20937170-id-3053573.html)
- [Difference between 'struct' and 'typedef struct' in C++?](http://stackoverflow.com/questions/612328/difference-between-struct-and-typedef-struct-in-c)
- [typedef struct vs struct definitions](http://stackoverflow.com/questions/1675351/typedef-struct-vs-struct-definitions)
