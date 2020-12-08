---
title: C/C++语言struct与typedef
date: 2017-11-18 21:25:19
tags:
- C
- C++
- struct
- typedef

categories:
- C++
---

## 背景

写这篇文章的背景是，我在看一篇博客[面试精选：链表问题集锦](http://wuchong.me/blog/2014/03/25/interview-link-questions/)时，对文中对链表节点的定义产生了疑惑。

<!--more-->

## 阐述我的疑惑



> 下面是本文所要用到链表节点的定义：
>
> ```c++
> struct Node{
>     int data;
>     Node* next;
> };
> ```



#### 我们知道，C 语言的结构体

```c
struct　结构体名{
    
    类型名1　成员名1;
    
    类型名2　成员名2;
    
    ……
    
    类型名n　成员名n;　　　
    
};

struct 结构体名 变量名 = xxx;
```

而且，上学那会儿就知道，**不允许对结构体本身递归定义**所以，第一段代码不是 C 语言。

如果用 C，因该如下

```c

typedef struct LinkedNode {
  int data;
  struct LinkedNode *next;
} LNode;

...

LNode ln;
ln.data;
```

莫不是博主写错了？

## 解答我的疑惑

再看看 C++

```c++
struct LinkedNode {
  int data;
  LinkedNode *next;
};

...
    
LinkedNode ln;
ln.data;
```

原来，博主使用的C++的语法。