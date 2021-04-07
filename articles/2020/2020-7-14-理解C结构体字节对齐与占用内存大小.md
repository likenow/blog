---
title: 理解C结构体字节对齐与占用内存大小
date: 2020-07-14 13:59:51
categories:
- C
tags:
- 结构体
- C
- 字节对齐
---

## 写在前面的话
**最近有被问到：结构体怎么写占用内存小？** 很惭愧，当时没答到点子上 ~~呜呜呜

## 结构体占用内存计算
结构体中同样的变量个数，却可能导致占用内存的大小不同。直接上代码：

```
#include <stdio.h>
// 每个平台上的编译器都有默认对齐系数n,可以通过#pragma pack(n)来指定。
#pragma pack(8)

struct _A
{
    char a;
    int b;
    short c;
    long long d;
} A;

struct _B
{
    int c;
    double a;
    char b[10];
} B;

struct _C
{
    double a;
    int c;
    char b[10];
} C;

struct _D
{
    int a;
    //long double大小为16bytes
    long double b;
    char c[10];
} D;


void structSizeFunc(void)
{
    printf("size A.a = %lud \n", sizeof(A.a));
    printf("size A.b = %lud \n", sizeof(A.b));
    printf("size A.c = %lud \n", sizeof(A.c));
    printf("size A.d = %lud \n", sizeof(A.d));
    printf("size A = %lud \n", sizeof(A));

    printf("size B.a = %lud \n", sizeof(B.a));
    printf("size B.b = %lud \n", sizeof(B.b));
    printf("size B.c = %lud \n", sizeof(B.c));
    printf("size B = %lud \n", sizeof(B));

    printf("size C.a = %lud \n", sizeof(C.a));
    printf("size C.b = %lud \n", sizeof(C.b));
    printf("size C.c = %lud \n", sizeof(C.c));
    printf("size C = %lud \n", sizeof(C));

    printf("size D.a = %lud \n", sizeof(D.a));
    printf("size D.b = %lud \n", sizeof(D.b));
    printf("size D.c = %lud \n", sizeof(D.c));
    printf("size D = %lud \n", sizeof(D));
}




打印：
size A.a = 1d 
size A.b = 4d 
size A.c = 2d 
size A.d = 8d 
size A = 24d 
size B.a = 8d 
size B.b = 10d 
size B.c = 4d 
size B = 32d 
size C.a = 8d 
size C.b = 10d 
size C.c = 4d 
size C = 24d 
size D.a = 4d 
size D.b = 16d 
size D.c = 10d 
size D = 40d 

```


### 计算规则

#### 概念介绍

**C 语言中各类型占字节数**

**和机器字长及编译器有关系：**
所以，int，long int，short int 的宽度都可能随编译器而异。unsigned 是无符号的意思。但有几条铁定的原则（ANSI/ISO 制订的）： 
- sizeof(short int)<=sizeof(int) 
- sizeof(int)<=sizeof(long int) 
- short int 至少应为16位（2字节） 
- long int 至少应为32位。 
 

``` 
例如：
16位编译器
char ：1个字节
char*(即指针变量): 2个字节
short int : 2个字节
int：  2个字节
unsigned int : 2个字节
float:  4个字节
double:   8个字节
long:   4个字节
long long:  8个字节
unsigned long:  4个字节
 
 
32位编译器
 
char ：1个字节
char*（即指针变量）: 4个字节（32位的寻址空间是2^32, 即32个bit，也就是4个字节。同理64位编译器）
short int : 2个字节
int：  4个字节
unsigned int : 4个字节
float:  4个字节
double:   8个字节
long:   4个字节
long long:  8个字节
unsigned long:  4个字节
 
64位编译器
char ：1个字节
char*(即指针变量): 8个字节
short int : 2个字节
int：  4个字节
unsigned int : 4个字节
float:  4个字节
double:   8个字节
long:   8个字节
long long:  8个字节
unsigned long:  8个字节
```



#### 规则

结构体的内存计算方式遵循以下规则：

1. 数据成员对齐规则：第一个数据成员放在 `offset` 为 `0` 的地方，以后的每一个成员的 `offset` 都必须是该成员的大小与有效对齐值相比较小的数值的整数倍

2. 结构体作为成员：如果一个结构里有某些结构体成员，则结构体成员要从其内部有效对齐值的整数倍地址开始存储。

3. 结构体的总大小，必须是其有效对齐值的整数倍，不足的要补齐。

有了这些规则，再来印证上面的打印结果：

> 例子B结构体中，int 为第一个数据成员，所以下一个存储的起始位置是4，但是 double 占8个字节，按规则1，不满足8的倍数，填充到16，char 一共有10个字节（数组在内存中的表示会分解成单个的），即总共26，进行规则3，有效对齐值为8，26不满足8的倍数，填充到32。

> 例子C结构体中，double 为第一个数据成员，所以下一个存储的起始位置是8，按规则1，占用了12个字节，char 有10个字节，一共用了22个字节，规则3，该结构体中有效对齐值为8，22不满足8的倍数，填充到24。

## 字节对齐

上述的内存占用计算规则中就有**字节对齐**。字节对齐主要是为了提高内存的访问效率，其实就是为了方便指令对内存数据的读取。使每个成员的访问在尽量少的指令里完成，而不需要多次访问再拼接。个人理解有限，感兴趣的可以查阅资料继续深挖。

### 有效对齐值

每个平台上的编译器都有默认对齐系数n，可以通过`#pragma pack(n)`来指定。
有效对齐值就等与该对齐系数和结构体中最长的数据类型的长度两者最小的那一个值

> eg.比如对齐系数是8（`#pragma pack(8)`），而结构体中最长的是int，4个字节，那么有效对齐值为4。

上述，代码中，如果把对齐系数改为16，打印结果如下：

```
size A.a = 1d 
size A.b = 4d 
size A.c = 2d 
size A.d = 8d 
size A = 24d 
size B.a = 8d 
size B.b = 10d 
size B.c = 4d 
size B = 32d 
size C.a = 8d 
size C.b = 10d 
size C.c = 4d 
size C = 24d 
size D.a = 4d 
size D.b = 16d 
size D.c = 10d 
size D = 48d 
```

没错，D 结构体，占用内存编程了 48。


## 总结

所以，到现在为止，我们可以尝试回答 ”结构体怎么写占用内存小？“ 这个问题了：
两个影响要素：
- 对齐系数
- 结构体成员顺序

> 结论：在选择合适的对齐系数的情况下，结构体成员变量最好按照长度递增的顺序依次定义各个成员。

以 B 结构体为例：

```
struct _B
{
    char b[10];
    int c;
    double a;
} B;
打印：
size B.a = 8d 
size B.b = 10d 
size B.c = 4d 
size B = 24d 
```

ps.这个结论在有些情况下也不是绝对的。如下，也是24字节。

```
struct _B
{
    int c;
    double a;
    char b[10];
} B;
打印：
size B.a = 8d 
size B.b = 10d 
size B.c = 4d 
size B = 24d 
```

但是，按照上述的原则来写，结构体占用内存小。

## 参考文献

- [如何理解 struct 的内存对齐？](https://www.zhihu.com/question/27862634)
- [内存对齐规则之我见](https://levphy.github.io/2017/03/23/memory-alignment.html)