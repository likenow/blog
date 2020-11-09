## 了解 const、宏、static、extern



之前的一篇文章 [C语言学习static和extern关键字](./articles/2014-8-17-C语言学习static和extern关键字.md)

### const

const 修饰**右边的变量**，具体的场景比如：修饰指针变量、修饰函数参数、修饰函数返回值、修饰成员函数。。。等

```c
int a = 10;
int b = 9;
/** 修饰指针的情况结论： p 没有被const修饰，它访问 内存空间的值 和 指向的地址 都可以被修改 **/
// 1
const int *p = &a;
// 2
int *const p = &a;
// 3
int const *p = &a;
// 4
const int *const p = &a；

// 1 3 等价，都是修饰 *p，那么 *p 不可变，所以如下：
p = &b;
*p = b; // errror: Read-only variable is not assignable

// 2 修饰的是 p
p = &b; // error: Cannot assign to variable p with const-qualified type 'int *const'
*p = b;

// 4 此时 *p 和 p 都被修饰了，那么 p 中存放的内存单元的地址和内存单元中的内容都不可变。

```



主要作用是**限定类型**，目的是使外界无法修改变量，保持只读。Xcode 下还可以提高编译的速度和时间，苹果也推荐使用 const 修饰全局变量。



### 宏

> 宏是一种批量处理的称谓。
>
> 一般说来，宏是一种规则或模式，或称语法替换 ，用于说明某一特定输入（通常是字符串）如何根据预定义的规则转换成对应的输出（通常也是字符串)。
>
> 这种替换在预编译时进行，称作宏展开。编译器会在编译前扫描代码，如果遇到我们已经定义好的宏那么就会进行代码替换，宏只会在内存中复制一份，然后全局替换。
>
> 更深入的可以看下：
>
> [ios宏定义学习](https://www.jianshu.com/p/4c29703becee)
>
> [宏定义的黑魔法 - 宏菜鸟起飞手册](https://onevcat.com/2014/01/black-magic-in-macro/)

**宏展开**在**预编译阶段**，所以没有编译检查。如果大量使用宏，预编译阶段会增加耗时。



### static

**static修饰局部变量**：

将局部变量的本来分配在栈区改为分配在静态存储区，也就改变了局部变量的生命周期。



**static修饰全局变量**：

全局变量本来是在整个源程序的所有文件都可见，static修饰后，改为只在申明自己的文件可见，即修改了作用域。



在 OC 中，`static` 变量声明一般在源文件（ `.m` ）中，如果放在头文件（ `.h` ）中，其他文件引入这个头文件时，容易引起命名冲突。被 `static` 修饰的全局变量，作用域为当前文件。所以，如果一个变量在当前文件中被多处使用，建议使用 static 定义为当前类的全局变量。



### extern

extern主要是用来引用全局变量，它的原理就是：先在本文件中查找，本文件中查找不到再到其他文件中查找。

`extern` 关键字，主要用来定义**外部全局变量**。上面说用 `static` 定义作用域为当前文件的全局变量。那如果想定义作用域为整个工程文件全局变量，即外部全局变量，则用 `extern`。

一般在头文件中使用 `extern` 声明变量，在源文件中赋值，尽量不要将外部全局变量的值暴露在头文件中；或者在头文件中声明，在其他文件中使用的时候再进行赋值。`extern` 关键字只对变量进行声明，表明该变量可能在本模块使用也可以在其他模块使用。



#### extern 进阶

在 Apple API 中，我们可以看到一些与 `extern` 相关的宏定义，例如 `FOUNDATION_EXTERN` 、 `UIKIT_EXTERN`等。我们可以看一下其中一个的定义：

```objective-c

// FOUNDATION_EXTERN 定义 NSObjCRuntime.h
#if defined(__cplusplus)
#define FOUNDATION_EXTERN extern "C"
#else
#define FOUNDATION_EXTERN extern
#endif

// UIKIT_EXTERN 定义 UIKitDefines.h
#ifdef __cplusplus
#define UIKIT_EXTERN		extern "C" __attribute__((visibility ("default")))
#else
#define UIKIT_EXTERN	        extern __attribute__((visibility ("default")))
#endif
```

OC 是支持与 C++ 混编的。`__cplusplus` 是 C++ 中自定义宏，上面这段宏表示如果这是一段 C++ 代码，则使用 `extern "C"`。

那么问题来了，为什么要用 `extern "C"` 呢？在 C++ 中，是支持重载的。就是函数名可以一样，在编译处理时，会将“函数名及返回类型+参数及返回类型”合成一个字段，以此判断是哪个函数；但是 C 中是不支持重载的，编译时只会将函数名合成一个字段，即 C 和 C++ 对函数名的处理是不一样的。C++ 为了兼容 C，在C++代码中调用 C 编码的文件，就需要用 `extern"C"` 来告诉编译器:这是一个用 C 编码的文件，请用 C 的方式来链接它们。因此在进行 OC 与 C++ 混编时，用`FOUNDATION_EXTERN` 去修饰全局方法。

其他的例如 `UIKIT_EXTERN` 等与此类似，只是名字不同，目的是为了在不同的 framework 中使用时命名区分。平常定义一些外部全局变量时，直接使用 `extern` 即可。



### 组合使用

#### static + const

`BASE_URL` 本文件内可引用

```objective-c
#import "UrlDemo.h"
static NSString * const BASE_URL  =  @"http://www.baidu.com/";

@implementation UrlDemo

@end
```

小插曲：

如果不加 `static` 符号，那么编译器就会对该变量创建一个 **外部符号** 所以如果你在两个互不相关的`.m`文件中定义了同名的常量，即使这两个文件之间没有任何关联，但是编译的时候会报错。所以当你在你自己的`.m`文件中需要声明一个只有你自己可见的局部变量(`k开头`)的变量的时候一定要同时使用 `static`和 `const` 两个符号。



#### extern + const

UrlDemo的`.h`文件 声明

```objectivec
#import "UrlDemo.h"
// 声明  BASE_URL
extern NSString * const BASE_URL;

@implementation UrlDemo

@end
```

UrlDemo的`.m`文件

```objectivec
#import "UrlDemo.h"
// 定义并初始化（ps. extern 定义的常量必须也只能初始化一次）
NSString * const BASE_URL  =  @"http://www.baidu.com/";

@implementation UrlDemo

@end
```

> 提示：定义全局的东西，遵循规定，用专门的全局的文件来管理全局变量，以避免全局变量重复定义

