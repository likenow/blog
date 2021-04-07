---
title: C语言学习static和extern关键字
date: 2014-08-17 20:28:38
categories:
- C
tags:
- static
- extern
- C
---

## static 关键字

### 1. 定义内部函数

> 如果在当前文件中定义的函数不允许其他文件访问、调用，只能在内部使用，就称为内部函数。C语言规定不同的源文件可以有同名的内部函数，并且互不干扰。

```c
/*
 * a.c
 */
#include <stdio.h>
static void a() {
    printf("a 函数被调用了");
}
```

```c
/*
 * a.h
 */
static void a();
```

<!--more-->




```c
/*
 * main.c
 */
#include 'a.h'

int main() {
    // 报错
    a();
    
    return 0;
}
```

上述代码运行报错，是因为 `a()` 被定义为内部函数，不允许外部文件访问。



### 2. 声明内部函数

```c
#include <stdio.h>
// 声明内部函数
static void test();

int main()
{
    test();
    return 0;
}
// 定义内部函数
static void test() {
    printf("调用了test函数");
}
```

### 3. 定义静态变量

> 静态变量是存储在静态内存中的，也就是不属于堆栈。静态变量在程序运行之前创建，在程序的整个运行期间始终存在，直到程序结束。

```c
/*
 * d.c
 */
#include <stdio.h>

// 定义全局静态变量，作用于是定义位置往下;
// 默认情况下，一个函数不可以访问在它后面定义的全局变量
static int a;

int main()
{
    a = 10;
    printf("a=%d", a);
    return 0;
}
```

我们并不想让`d.c`源文件中的全局变量跟其他源文件共享，相当于私有的全局变量，那么你就得用`static`关键字来定义变量。所以，以下代码会报错:


```c
/*
 * e.c
 */
#include <stdio.h>

int main()
{
    // 报错，e.c 无法访问 d.c 中定义的全局静态变量
    a = 20;
    printf("a=%d", a);
    return 0;
}
```

## extern 关键字

### 1. 定义外部函数

因为，默认情况下，定义的函数就是外部函数，所以，定义外部函数可以不用加 `extern` 关键字

> 外部函数：如果在当前文件中定义的函数允许其他文件访问、调用，就称为外部函数。C语言规定，不允许有同名的外部函数。

```c
/*
 * f.c
 */
#include <stdio.h>

void f() {
    printf("f 函数被调用了");
}
// 等价于
extern void f() {
    printf("f 函数被调用了");
}
```

```c
/*
 * f.h
 */
extern void f();
```



###2. 调用外部函数

```c
/*
 * g.c
 * 在定义函数时，如果在函数的最左边加上关键字extern，则表示此函数是外部函数，可供其他文件调用。
 * C语言规定，如果在定义函数时省略extern，则隐含为外部函数。
 */
#include 'f.h'

int main() {
    // 打印"f 函数被调用了"
    f();
    
    return 0;
}
```



### 3. 声明全局变量

```c
// 声明全局变量
extern int a;
int main() {
    a = 10;
    return 0;
}
// 定义全局变量
int a;
```



## 参考文献

- [美]Prate,S. C Primer Plus[M]. 第5版. 云巅工作室，译. 北京：人民邮电出版社，2011.4：60-85.

- [【C语言】19-static和extern关键字1-对函数的作用](https://www.cnblogs.com/mjios/archive/2013/03/21/2974181.html)

- [【C语言】20-static和extern关键字2-对变量的作用](https://www.cnblogs.com/mjios/archive/2013/03/22/2976477.html)

- [【C语言】18-变量类型](https://www.cnblogs.com/mjios/archive/2013/03/21/2973719.html)



