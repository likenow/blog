---
title: Windows操作系统的电脑如何学习OC
date: 2015-03-07 22:01:50
tags:
- 工具
- OC
categories:
- OC
---

## 背景

本人想学习`Objective-C`语言开发，不过，目前使用的还是一台`windows`操作系统且机器老旧的`联想G470`电脑。想着如何使用现在手里的工具进行`Objective-C`学习呢？接下来就是我搜集的资料的整理。

<!--more-->

## OC 开发环境的搭建

### 几种可行方案
#### 1. 手里不差钱的做法

- 买一台苹果电脑。ps.这是最直接了当的方法
- 买一台高性能的电脑安装虚拟机。ps.安装虚拟机肯定不如直接买苹果电脑

#### 2. 差钱但是想体验苹果操作系统

- 安装黑苹果。ps.电脑有可能会装坏

#### 3. 只是想学习一下Objective-C开发的童鞋

- 安装`Codeblocks` **IDE**开发环境，搭建支持 `Objective-C` **编译**、**调试**、**运行**的集成开发环境。ps.这种方法对个人电脑的性能几乎没有要求，可以快速构建，本文介绍的是这一种方法。

### 上述 3  的方案执行如下

#### 一、安装Objective-C编译器--GNUstep

具体，参见[GNU Windows Installer](http://www.gnustep.org/windows/installer.html)

#### 二、安装 Codeblocks

下载地址是 [codeblocks](http://www.codeblocks.org/)

#### 三、Codeblocks 配置与使用

**1. 配置编译器**

进入 *Settings->Compiler anddebugger...* ，选择 *GNU GCC Compiler* 编译器，按 *Copy* 按钮，并重新命名为**GNUstep MinGW Compiler**并保存。之后进入*Other Options* 分页，录入**-fconstant-string-class=NSConstantString -std=c99** 

**2. 连接器设置 Linkerstettings**

在连接库`Link Libraries`中添加两个文件：`libgnustep-base.dll.a`和`libobjc.dll.a`

**3. 指定搜索目录 Searchdirectories**

**Compiler**（编译器）设置为上述安装的 **GNUstep**的所在路径

**linker**（连接器）设置为 上述的放 `libgnustep-base.dll.a`和`libobjc.dll.a`的文件路径

**4. 添加Objective-C文件类型支持，语法高亮**

进入 *Settings->Environment...*，选择**Files extension handling** 添加 ***.m**；

进入 *Project->Projecttree->Edit file types & categories...* ，在**Sources**, 下面添加***.m**到文件类型列表中；

进入 *Settings->Editor...*，选择 *Syntaxhighlighting*，点击*Filemasks....*按钮，在弹出框尾部添加***.m **到文件类型；

点击*Keywords...*按钮 (紧靠*Filemasks...*按钮) 添加下面`Objective-C`的关键字到*EditKeywords*列表中。

**5. 代码测试**
上述开发环境配置完成后，就可以开始代码测试了。
首先，新建一个工程，选择`File->New->Project…`，会出现一个工程类型窗口，选择`Console Application`，然后按照工程建立指引，建立一个`mytest`的工程，并将`main.c`的文件更名为`main.m`

```objective-c
#import <Foundation/Foundation.h>

int main (int argc, const char *argv[]) {

    NSAutoreleasePool *pool =[[NSAutoreleasePool alloc] init];

    NSLog(@"%@",@"hello world");

    [pool drain];

    return 0;

}
```

之后再开始编译运行：*Buid –> Run…* 如果`dos 命令行工具`没有报错，恭喜你，你已经成功的搭建了`Windows`下的`Objective-C`的集成开发环境。



## 参考文献

- [Windows下的Objective-C集成开发环境（IDE）](https://blog.csdn.net/ldl22847/article/details/7482971)
- [GNU Windows Installer](http://www.gnustep.org/windows/installer.html)
- [codeblocks](http://www.codeblocks.org/)
- [CodeBlocks使用方法](http://www.cj.zjut.edu.cn/zwl/courseshare/cplus/hs/hscplus/aboutCodeblocks.htm)