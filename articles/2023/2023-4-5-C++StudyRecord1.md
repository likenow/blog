## C ++ 学习笔记

> 感谢 https://www.youtube.com/@TheCherno 的视频教学！！！



[toc]



### using namespace

为什么不使用 Using namespace std？

阅读代码更加清晰的知道使用了**标准库**

```C++
std::vector<int> values = {1,3,4,5};

using namespace std;
vector<int> values = {1,3,4,5};
```

**避免在头文件内使用 using namespace**

**对自己的库使用 using namespace，在足够小可控的作用域下使用。**

### 名称空间 namespace

相同符号

相同函数签名

> C 没有 namespace
>
> 将库的实际名称或某种id嵌入到函数名中来避免符号冲突。

编程风格

```C++
// 把命名空间放在同一行。（可嵌套）
namespace apple { namespace functions {
    void print(const char* text)
    {
        std::cout << text << std::endl;
    }
 }}
 
 
{
    apple::print("hello");
}
```

### 线程 threads

```C++
#include <iostream>
#include <thread>
#include <chrono>

static bool s_Finished = false;

void doWork()
{
    while (!s_Finished) {
        std::cout << "Working !!!\n" << std::endl;
        // std::this_thread 当前线程
        std::cout << "thread id = " << std::this_thread::get_id() << std::endl;
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }   
}

int main()
{
    std::cout << "thread id = " << std::this_thread::get_id() << std::endl;
    std::thread worker(doWork);
    std::cin.get();
    s_Finished = true;

    worker.join();   
}
```

### 计时 timing

**Chrono** 库，平台无关

**instrumentation**，可以使用插码来实际修改源代码，以包含某种分析工具，比如：

```C++
#include <iostream>
#include <thread>
#include <chrono>

static bool s_Finished = false;

struct Timer
{
    std::chrono::time_point<std::chrono::steady_clock> start, end;
    std::chrono::duration<float> duration;
    Timer()
    {
        start = std::chrono::high_resolution_clock::now();
    }
    ~Timer()
    {
        end = std::chrono::high_resolution_clock::now();
        duration = end - start;
        std::cout << "Timer took " << duration.count() << " s" << std::endl;
    }
};

void f()
{
    Timer timer;
    for (int i = 0; i < 100; i++)
    {
        std::cout << "hello \n";
    }
}

int main()
{
    f();
    std::cout << "Finished !!!\n" << std::endl;
        
}
```

### 多维数组 multidimensional arrays

```C++
void f11()
{
    // 50 * 4 -> 200 个字节
    int* a = new int[50];
    delete[] a;
    
    // a pointer to a pointer 一个指向 int 指针的指针
    // 分配了 50 个指针 * 4 -> 200 字节（区别于上述一维数组，现在存储空间存了200个字节的指针）
    // 我们有一个包含 50 个数组的内存位置的数组
    // 我们存储的实际数组其实还没有被分配
    int** a2d = new int* [50];
    for (int i = 0; i < 50; i++)
    {
        a2d[i] = new int[50];
    }

    int*** a3d = new int** [50];
    for (int i = 0; i < 50; i++)
    {
        a3d[i] = new int*[50];
        for (int j = 0; j < 50; j++)
        {
            a3d[i][j] = new int[50];
            /*
            int** ptr = a3d[i];
            prt[j] = new int[50];
            */
        }
    }
    /*
    删除，需要遍历，然后 delete 所有的数组（没有 delete[][]！）
    */
    for (int i = 0; i < 50; i++)
    {
        delete[] a2d[i];
    }
    delete[] a2d;
    
    for (int i = 0; i < 50; i++)
    {
        delete[] a3d[i];
        for (int j = 0; j < 50; j++)
        {
            delete[] a3d[i][j];
        }
    }
    delete[] a3d;
}

// 上述会产生碎片，会导致 cache miss
// 速度比 一维数组 慢！
/*
优化内存访问
如果你能将要访问的内存存储在一起
那么你在定位数据时，会有更多的 cache hits（缓存命中）以及更少的 cache miss
*/

// 一维数组实现，可以像二维数组一样访问，这是连续的内存，它更快
void f12()
{
    int* array = new int[50 * 50];
    for (int i = 0; i < 50; i++)
    {
        for (int j = 0; j < 50; j++)
        {
            // 每次i + 1， 就向前跳50个元素
            // 相当于你的数组网格向下跳一行
            array[i + j * 50] = 1;
        }
    }
}
```

### 排序 sorting

标准库 algorithm sort 可以为你进行排序，或者为你提供它的任何类型的迭代器执行排序。nlogN

```C++
#include <vector>
#include <algorithm>

void f2()
{
    std::vector<int> values = { 1,3,5,7,9,2 };
    // 不提供任何谓词，对于整数默认按照生序排序
    std::sort(values.begin(), values.end());
    
}
void f3()
{
    std::vector<int> values = { 1,3,5,7,9,2 };
    std::sort(values.begin(), values.end(), [](int a, int b) {
        // 如果 a 小于 b，它会排到列表的前面
        return a < b;
    });
    for (int i = 0; i < values.size(); i++)
    {
        std::cout << values[i] << std::endl;
    }
}
```

### 类型双关 Type Punning

> Type punning refers to the practice of accessing an object of one type through a pointer or reference of another type. This can be done by using C-style casts or by using union types in C++.
>
> In C++, type punning can be used for a variety of purposes, such as converting one type to another, reinterpreting the contents of a memory buffer, or accessing an object as a different type for performance reasons.

用来在  c++ 中绕过类型系统

把拥有的这段内存，当作不同类型的内存来对待。

```C++
void f4()
{
    int a = 50;
    double value = a; // 隐式类型转换 = *(double*)&a;
  	// C++会将整数a转换为双精度浮点数，但是由于整数类型和浮点类型的内部表示方式不同，因此可能会导致精度丢失。 
    double value1 = (double)a; // 显示类型转换
  	// double value1 = static_cast<double>(a);
    std::cout << value << std::endl;
}

void f5()
{
    int a = 50;
    double value = a; // 隐式类型转换 = *(double*)&a;
    value = 0.0; // 可能会导致 issue
    std::cout << value << std::endl;
}

struct Entity
{
    int x, y;
}

void f6()
{
    // 将 Entity 结构体看成是一个 int 数组
    // 不用通过 e.x, e.y 就可以提取出这些整数
    Entity e = {5, 8};
    // e 包含了这个int 数组的开始地址，也就是包含了指向 int 的指针
    int* position = (int*)&e;
    std::cout << position[0] << "," << position[1] << std::endl;
    
    // +4 移动4个字节
    int y = *(int*)((char*)&e + 4);
    std::cout << y << std::endl;
}

struct Entity
{
    int x, y;
    int* GetPosition()
    {
    return &x;
    }
}
```

### 联合体 Union

可以像结构体或类一样使用它，也可以给它添加静态函数或普通函数/方法等，但只能有一个成员。

当你想给同一个变量取两个不同的名字时，它真的很有用。

通常 union 是匿名使用的，但是匿名 union 不能含有成元函数。

union通常和类型双关一起使用。如果要把它转换成不同的变量类型，可以很容地做到。

```C++
void f6()
{
    struct Test
    {
        union
        {
            // 两种不同的方法来处理相同的内存 
            float a;
            int b;
        };
    };
    Test t;
    t.a = 2.0f;
    std::cout << t.a << "---" << t.b << std::endl;
}
struct Vector2
{
    float x, y;
};

struct Vector4
{
    float x, y, z, w;
    Vector2& GetA()
    {
        // 把 x 的内存地址转换成 Vector2，然后解引用
        return *(Vector2*)&x;
    }
};

void PrintVector2(const Vector2& vector)
{
    std::cout << vector.x << " = x, y = " << vector.y << std::endl;
}
struct Vector2
{
    float x, y;
};

struct Vector4
{
    union
    {
        struct
        {
            float x, y, z, w;
        };
        struct
        {
            // a {x,y} b {z,w}
            Vector2 a, b;
        };
    };
    
    /*
    Vector2& GetA()
    {
        return *(Vector2*)&x;
    }
    */
};

void PrintVector2(const Vector2& vector)
{
    std::cout << vector.x << " = x, y = " << vector.y << std::endl;
}

void f7()
{
    Vector4 v = { 1.0f, 20.0f, 3.0f, 40.0f };
    PrintVector2(v.a);
    PrintVector2(v.b);
    // 这里修改了 z ，对应到打印里这个变量对应 b.x，因为它占用了相同的内存
    v.z = 500.f;
    std::cout << "-----------------------" << std::endl;
    PrintVector2(v.a);
    PrintVector2(v.b);
    
    /**
    1 = x, y = 20
    3 = x, y = 40
-----------------------
    1 = x, y = 20
    500 = x, y = 40
    */
}
```

在C++标准中，联合体（union）的大小至少要能够容纳其中最大的成员，这通常是因为不同的成员可能占用的内存空间大小是不同的，因此需要保证联合体大小足够大以容纳最大的成员。

但是，联合体的实际内存分配大小通常是编译器依据其内部实现而定的，并不是成员内存大小之和。由于联合体的成员共享同一块内存空间，因此编译器需要确保所有成员都能够放在同一块内存中，可能会采用一些内存对齐、填充等技术来达到这个目的。

因此，对于一个联合体，其实际内存分配大小通常会大于等于其最大成员的大小，而不是等于所有成员内存大小之和。实际分配大小还可能受到编译器和平台的影响，因此最好使用`sizeof`运算符来获取联合体的实际大小。

### 虚析构函数 virtual destructors

虚函数 + 析构函数

对于处理多态

```C++
class Base
{
public:
    Base() { std::cout << "Base Constructed\n"; }
    // 标记为虚函数（这意味着这类可能被扩展为子类，可能还有一个析构函数也需要被调用）
    virtual ~Base() { std::cout << "Base Destructed\n"; }
};

class  Derived : public Base
{
public:
    Derived() { m_Array = new int[5]; std::cout << "Derived Constructed\n"; }
    // 析构函数没有调用，会有内存泄漏
    ~Derived() { delete[] m_Array; std::cout << "Derived Destructed\n"; }
private:
    int* m_Array;
};


void f8()
{
    Base* base = new Base();
    delete base;

    Derived* d = new Derived();
    delete d;

    std::cout << "========================" << std::endl;
    Base* poly = new Derived();
    delete poly;
    /*
    * memory leak
    Base Constructed
    Base Destructed
    Base Constructed
    Derived Constructed
    Derived Destructed
    Base Destructed
    ========================
    Base Constructed
    Derived Constructed
    Base Destructed
    */
}
```

> 只要你允许一个类拥有子类你必须声明你的析构函数是虚函数，否则没人能安全的扩展这个类

### 类型转换 casting

Cast

必须在 c++ 可用的类型系统中进行的类型转换！！！

```C++

{
    int a = 5;
    //这种隐式的转换，很容易做到，没有数据都是之类的事情
    double b = a;

    double c = 5.25;
    // 这种 c 风格的类型转换
    int d = (int)(c);

    double e = a + c; // 10.25
    double f = (int)(a + c); // 10 截断

    /**
    c++ 风格的类型转换（对阅读代码和编写代码有帮助）
    增加了编译时检查 -- 鲁棒性
    减少在尝试强制转换时，可能会意外犯的错误
    static_cast 静态类型检查
    reinterpret_cast 类型双关（把这段内存重新解释成别的东西）
    dynamic_cast 可以看到转换是否成功它与运行时类型信息 RTTI（runtime type information）紧密联系
    const_cast 移除或者添加变量的 const 限定
    */
    double g = static_cast<int>(a) + c; // 10.23

    double h = static_cast<AClass>(a) + c; // 编译器会提示错误❌
}
class Base
{
public:
    Base() { std::cout << "Base Constructed\n"; }
    virtual ~Base() { std::cout << "Base Destructed\n"; }
};

class  Derived : public Base
{
public:
    Derived() { m_Array = new int[5]; std::cout << "Derived Constructed\n"; }
    ~Derived() { delete[] m_Array; std::cout << "Derived Destructed\n"; }
private:
    int* m_Array;
};

class AnotherClass : public Base
{
public:
    AnotherClass() { std::cout << "AnotherClass Constructed\n"; }
    ~AnotherClass() { std::cout << "AnotherClass Destructed\n"; }
};

void f10()
{
    Derived* d = new Derived();

    Base* b = d;

    AnotherClass* ac = dynamic_cast<AnotherClass*>(b);
    if (ac)
    {
        std::cout << "ac " << std::endl;
    }
    else
    {
        std::cout << "ac empty" << std::endl;
    }

    Derived* dc = dynamic_cast<Derived*>(b);
    if (dc)
    {
        std::cout << "dc " << std::endl;
    }
    else
    {
        std::cout << "dc empty" << std::endl;
    }
}
```

### 条件与操作断点 conditional an action breakpoints

调试

Conditions

​	条件

actions

​	Continue execution

断点会拖慢程序



### C++ 安全及 cherno 观点

降低崩溃、内存泄漏、非法访问等问题，这往往是人为错误

智能指针和自动内存管理系统可以降低人犯错的概率

原始指针和智能指针的讨论。

智能指针围绕原始指针做了额外的辅助代码，以便自动化所有事情，但本质上只是删除和释放内存。

### 预编译头文件 Precompiled header

在你想要编译 main 文件之前，预处理器必须把所有这些文件复制到，比如 vector 文件，这就有上十万行代码了，它需要解析，并以某种形式标记并编译。

如果main 引入了 vector，那么 vector 必须复制并粘贴到  main 文件中，然后是整个代码，所有的代码每次都需要被解析和编译。

如果你要改变我们正在编译的main 文件，main 文件就是一个 cpp 文件，就是我们现在正在做的那个修改，整个文件都要重新编译。

如果你的项目中有许多的 cpp 文件，很多地方都会 #include <vector> ，显然它们会被单独包含在每个文件中，因为这就是编译器的工作方式。每个翻译单元都是单独编译的，然后再进行链接，把所有的东西连接在一起。

所以那个 vector 类或者 vector 文件必须被复制并粘贴到你的 c++ 文件中，从头开始重新解析并重新编译。这意味着你的编译事件将比潜在的要长的多。

不仅如此，考虑到你的项目中有多个 c++ 文件，它们都包含了 vector 你不得不一遍又一遍解析同样的代码。对你实际要编译的每个翻译单元，每个cpp文件，每一次都是如此。

所以你可以用一个叫 预编译头文件的东西来代替。它的作用是接收一堆你告诉它要接收的头文件。基本上是一堆代码，它只编译一次。它以二进制格式存储，这对编译器来说比单纯的文本处理要快得多。

这样你不需要解析整个vector文件，每次它只需要看预编译的头文件，他已经是非常快速且容易使用的，对编译器来说很容易使用的二进制格式。每次你需要 vector 的时候都要用到这些二进制格式，因为你已经在你当前编译的源文件中包含了预编译的头文件。 -- 编译提速！

不要把项目中经常修改变动的文件，放到 PCH 中，因为变动后整个预编译头文件都需要重新编译，速度又变回慢了。。。

PCH 真正有用的是外部依赖（😊预编译头文件可能比实际代码多很多倍～～）

- C++ 库
- 标准模板库
- Windows API
- ...

Pch 被包含在每个你写的 cpp 文件中。导致一个问题，如果你只单独看 CPP 文件，你并不知道她需要什么依赖。在模块化和代码重用方面，这可能非常困难！

```undefined
project -> properties -> C/C++ -> preprocessor -> Preprocess to a file
pch.h

pch.cpp

pch.cpp -> properties -> C/C++ -> precompiled headers -> precompiled header -> create(/Yc)

project -> properties -> C/C++ -> precompiled headers -> precompiled header -> use(/Yu)
project -> properties -> C/C++ -> precompiled headers -> precompiled header file -> pch.h

Main.cpp -> properties -> C/C++ -> precompiled headers -> precompiled header -> use(/Yu)
// 构建时长
tools -> options -> projects and solutions -> vc++ project settings -> build timing 设置为 yes
```

GCC 编译器

```undefined
time g++ -std=c++11 Main.cpp
// pch.gc.h 114M
```

在你的预编译头文件中放入什么，是一个可探讨的问题 😜

### dynamic_cast 动态类型转换

更像是一个函数，在运行时计算。

举个例子，一个 Entity 类，玩家Player类和敌人enemy类都是继承自 Entity类，如果把一个  Entity 指针转换成玩儿家Player类该怎么做？

dynamic_cast 常用来做验证，如果把 Entity 转换成 Player玩家失败，会返回一个  NULL 指针

```C++
class Entity
{
public:
    virtual void PrintName() {}

}

class Player : public Entity
{

}

class Enemy : public Entity
{

}

int main()
{
    Player* player = new Player();
    Entity* acutallyPlayer = new Player(); // 隐式转换
    Entity* acutallyEnemy = new Enemy(); // 隐式转换
    
    // dynamic_cast 它会告诉我们它需要一个多态类类型
    // 我们需要一个虚函数表
    // runtime type information
    Player* p = dynamic_cast<Player*>(acutallyPlayer); // 成功
    
    Player* p1 = dynamic_cast<Player*>(acutallyEnemy); // NULL
    
    /*
    actuallEnemy is Player --> c#
    actuallEnemy instanceof Player --> java
    */
}
```

Rtti 增加了开销，因为类型需要存储更多关于自己的信息。

dynamic_cast 也需要时间，因为我们需要检查类型信息是否匹配。

```undefined
// 可以关闭 RTTI
virtual studio --> c/c++ -> language -> enable runtime type information

// 'dynamic cast' used on polymorphic type entity with / GR-
```

### 测试基准 Benchmarking

```C++
#include "pch.h"

class MyTimer
{
public:
    MyTimer()
    {
        m_StartTimePoint = std::chrono::high_resolution_clock::now();
    }
    ~MyTimer()
    {
        Stop();
    }
    void Stop()
    {
        auto endTimePoint = std::chrono::high_resolution_clock::now();
        auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimePoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimePoint).time_since_epoch().count();
        auto duration = end - start;
        double ms = duration * 0.001;
        std::cout << "Timer took " << duration << " us    " << ms << " ms" << std::endl;
    }
private:
    std::chrono::time_point<std::chrono::steady_clock> m_StartTimePoint;
};

int main()
{

    int value = 0;
    {
        MyTimer t;
        for (int i = 0; i < 1000000; i++)
        {
            value += 2;
        }
    }

    std::cout << value << std::endl;
    

    __debugbreak();
}
```

比如上述代码，在 debug 模式，你可以看到汇编代码执行 add 指令。但在 release 模式下，编译器看了这段代码，并意识到，这并不需要在运行时计算，于是进行了优化，直接在编译时就计算出来了。只需要打印 2百万就可以了。我们没有做任何形式的增量计算。那么这里计时器的结果将完全是无用功！！！因为 timer 啥也没测量，我们基本上是在测量打印需要多长时间。。。。事实上甚至都不是，因为那个计时范围不包括打印函数😢 你要确保自己并不是在测量什么都没有发生的事情，因为编译器实际上会非常积极地改变你的代码！！！

```C++
void fn2()
{
    struct Vector2
    {
        float x, y;
    };

    std::cout << "Make shared \n";
    {
        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
        MyTimer t;
        for (int i = 0; i < sharedPtrs.size(); i++)
        {
            sharedPtrs[i] = std::make_shared<Vector2>();
        }
    }
    
    std::cout << "New shared \n";
    {
        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
        MyTimer t;
        for (int i = 0; i < sharedPtrs.size(); i++)
        {
            sharedPtrs[i] = std::shared_ptr<Vector2>(new Vector2());
        }
    }

    std::cout << "Make unique \n";
    {
        std::array<std::unique_ptr<Vector2>, 1000> uniquePtrs;
        MyTimer t;
        for (int i = 0; i < uniquePtrs.size(); i++)
        {
            uniquePtrs[i] = std::make_unique<Vector2>();
        }
    }
}
```

### 结构化绑定 structured bindings c++17

更好的处理多返回值，特别是如何处理元组 tuple 和 对组 pairs 以及返回诸如此类的东西。

```undefined
// 切换 c++ 版本

project --> properties --> C/C++ --> Language --> Standard --> C++17
```

### 如何处理 OPTIONAL 数据 std::optional c++17

```C++
std::optional<std::string> ReadFileAsString(const std::string& filePath)
{
    /*
    std::filesystem::path absolutePath = std::filesystem::absolute(filePath);
    std::cout << "Absolute path: " << absolutePath << std::endl;
    */
    std::filesystem::path file_path(filePath);
    if (!std::filesystem::exists(file_path))
    {
        std::cout << "File dose not exist \n";
        return {};
    }

    // Check the file permissions
    std::filesystem::file_status status = std::filesystem::status(file_path);
    if ((status.permissions() & std::filesystem::perms::owner_read) == std::filesystem::perms::none)
    {
        std::cerr << "No permission to read file" << std::endl;
        return {};
    }

    std::ifstream infile(file_path);
    if (!infile)
    {
        std::cout << "Error opening file \n";
        return {};
    }
    std::string reslut;
    // read file
    std::string line;
    while (std::getline(infile, line)) { // read each line of the file
        reslut += line;
        std::cout << line << std::endl; // output the line
    }
    infile.close();
    return reslut;
}

int main()
{
    // C:\\Users\\fmsli\\dev\\Project1\\Project1\\src\\Test.txt
    // absolute path
    std::optional<std::string> data = ReadFileAsString("C:\\Users\fmsli\dev\Project1\Project1\src\Test.txt");
    //if (data.has_value())
    if (data)
    {
        std::cout << "File read successfully! \n";
    }
    else
    {
        std::cout << "File read error! \n";
    }
    //__debugbreak();
}
```

> 这个例子用了打开文件，注意 virtual studio 中，打开文件的路径！！！
>
> 这里我用了绝对路径才访问到。。。

### 单一变量存放多种类型的数据 c++17 multiple types of data in a single variable

```C++
#include <variant>


void fn6()
{
    std::variant<std::string, int> data;
    data = "liuxing";
    
    data = 24;
    
    if (auto v = std::get_if<std::string>(&data))
    {
        std::cout << std::get<std::string>(data) << "name \n";
    }
    else if (auto v = std::get_if<int>(&data))
    {
        std::cout << "age = " << std::get<int>(data) << std::endl;
    }

}
```

Variant 是一个类型安全的 “union”

当我们修改联合体的某个成员时，其他成员的值也被改变了，因为它们共享同一块内存空间。



std::variant与联合体不同，它使用的内存空间大小只和存储的成员类型最大的一种成员大小相同，而不是成员内存大小之和。

这是因为std::variant内部实现使用了一个类型擦除技术，将不同类型的成员进行类型擦除后，存储到一个固定大小的内存空间中，保证不同成员之间的地址不重叠。因此，std::variant的大小只需要足够存储最大的成员即可。

在C++17之前，std::variant的实现是使用了类似于联合体的技术，需要保证所有成员能够放在同一块内存中，因此实际内存分配大小可能会大于等于最大成员的大小。但是，从C++17开始，标准库提供了std::aligned_union模板来保证内存对齐和填充，因此实际内存分配大小不再需要大于等于最大成员的大小。

总之，std::variant的实际内存分配大小是由其最大成员的大小和内存对齐方式所决定的，不是成员内存大小之和。

### 存储任意类型的数据 c++17 store any data

鸡肋。。。

```C++
// void*

// std::any

#include <any>

void fn7()
{
    //std::any data = std::make_any();
    std::any data;
    data = 2;
    // 这是一个 const char 类型
    data = "liuxing";
    data = std::string("liu");
    
    // 取值的时候你必须知道它是哪个类型，然后把它转换成那个类型，你可以使用 std::any_cast
    // 如果类型不对，会抛出类型转换异常的错误
    // 会产生复制
    std::string string1 = std::any_cast<std::string>(data);
    
    // 不产生复制，通过引用返回
    std::string& string2 = std::any_cast<std::string&>(data);
    
}
```

Any 动态分配内存

小空间 -- union

超过了 32 个字节

大空间 -- void*

简单重写一个 new 操作符，来看一下 Any 的动态分配内存

```C++
void* operator new(size_t size)
{
    return malloc(size);
}

struct CustomClass
{
    std::string s0, s1;
}

int main()
{
    std::any data;
    // Big_Storage, 会在这里调用 new
    data = CustomClass();
}
```



### 让 C++ 运行的更快 c++ run faster

多线程

数据流化

```C++
#include <future>

/*
当某些资源不能被同时访问，你必须 锁定资源 做你想做的事情，比如：修改它，然后解锁它
这样另一个线程才能访问它。
方法是使用：mutex/ spinlock ...
*/
static std::mutex s_MeshesMutex;

// meshes 是一个指针拷贝
// filepath 是一个拷贝，因为你可能会访问已经被删除的内存
static void LoadMesh(std::vector<Ref<Mesh>>* meshes, std::string filepath)
{
    auto mesh = Mesh::Load(filepath);
    // 锁定资源
    std::lock_guard<std::mutex> lock(s_MeshesMutex);
    meshes->push_back(mesh);
}


void EditorLayer::loadMeshes()
{
    std::ifstream stream("src/Models.txt");
    std::string line;
    std::vector<std::string> meshFilepaths;
    while (std::getline(stream, line)
    {
        meshFilepaths.push_back(line);
    }
#define ASYNC 1
#if ASYNC
    for (const auto& file : meshFilepaths)
    {
        m_Meshes.push_back(Mesh::Load(file));
    }
#else
    for (const auto& file : meshFilepaths)
    {
        m_Futures.push_back(std::async(std::launch::async, LoadMesh, &m_Meshes,file));
    }
#endif    
}

void func()
{
    // 这主要是为了持有 future 避免for循环结束，释放
    std::vetcor<std::future<void>> m_Futures;
}
```

### 让字符串更快 Make your Strings faster

```C++
static uint32_t s_AllocCount = 0;

void* operator new(size_t size)
{
    s_AllocCount++;
    std::cout << "Allocating = " << size << "bytes \n";
    return malloc(size);
}

void PrintName(const std::string& name)
{
    std::cout << name << std::endl;
}

void PrintNameView(std::string_view name)
{
    std::cout << name << std::endl;
}

void fn8()
{
#if 0
    std::string name = "liu xing";
    std::string fname = name.substr(0, 3);
    std::string lname = name.substr(4, 7);
    PrintName(lname);
    /*
    Allocating = 16bytes
    Allocating = 16bytes
    Allocating = 16bytes
    xing
    3 allocations
    */
#elif 0
    std::string name = "liu xing";
    std::string_view firstName(name.c_str(), 3);
    std::string_view lastName(name.c_str() + 4, 4);
    PrintNameView(lastName);
    /*
    Allocating = 16bytes
    xing
    1 allocations
    */
#else
    const char* name = "liu xing";
    std::string_view firstName(name, 3);
    std::string_view lastName(name + 4, 4);
    PrintNameView(lastName);
    /*
    xing
    0 allocations
    */
#endif

    std::cout << s_AllocCount << " allocations \n";

        
}

int main()
{
    fn8();
    //__debugbreak();
}
```

string_view -- c++17

本质上只是一个指向现有内存的指针，换句话说，就是一个 const char 指针，指向其他人拥有的现有字符串，再加上一个大小 size

没有内存分配，按值传递字符串视图是非常轻量级的

### 可视化基准测试 Visual Benchmarking 

> chrome://tracing/
>
> json

```C++
#pragma once
#include <string>
#include <chrono>
#include <algorithm>
#include <fstream>

#include <thread>
#include <mutex>

#define COMBINE_HELPER(X,Y) X##Y
#define COMBINE(X,Y) COMBINE_HELPER(X,Y)

/*
__FUNCTION__
__FUNCSIG__
*/

#define PROFILING 1
#ifdef PROFILING
    #define PROFILE_SCOPE(name) InstrumentationTimer COMBINE(timer,__LINE__)(name)
    #define PROFILE_FUNCTION()  PROFILE_SCOPE(__FUNCTION__)
#else
    #define PROFILE_SCOPE(name)
#endif

struct ProfileResult
{
    std::string Name;
    long long Start, End;
    
    // linux: unsigned long int (depend on compile setting 64bit or 32bit)
    uint32_t ThreadID;
    //std::thread::id ThreadID;
};

struct InstrumentationSession
{
    std::string Name;
};

class Instrumentor
{
private:
    InstrumentationSession* m_CurrentSession;
    std::ofstream m_OutputStream;
    int m_ProfileCount;
    std::mutex m_lock;
public:
    Instrumentor()
        : m_CurrentSession(nullptr), m_ProfileCount(0)
    {
    }

    void BeginSession(const std::string& name, const std::string& filepath = "results.json")
    {
        m_OutputStream.open(filepath);
        WriteHeader();
        m_CurrentSession = new InstrumentationSession{ name };
    }

    void EndSession()
    {
        WriteFooter();
        m_OutputStream.close();
        delete m_CurrentSession;
        m_CurrentSession = nullptr;
        m_ProfileCount = 0;
    }

    void WriteProfile(const ProfileResult& result)
    {
        std::lock_guard<std::mutex> lock(m_lock);

        if (m_ProfileCount++ > 0)
            m_OutputStream << ",";

        std::string name = result.Name;
        std::replace(name.begin(), name.end(), '"', '\'');

        m_OutputStream << "{";
        m_OutputStream << "\"cat\":\"function\",";
        m_OutputStream << "\"dur\":" << (result.End - result.Start) << ',';
        m_OutputStream << "\"name\":\"" << name << "\",";
        m_OutputStream << "\"ph\":\"X\",";
        m_OutputStream << "\"pid\":0,";
        m_OutputStream << "\"tid\":" << result.ThreadID << ",";
        m_OutputStream << "\"ts\":" << result.Start;
        m_OutputStream << "}";

        m_OutputStream.flush();
    }

    void WriteHeader()
    {
        m_OutputStream << "{\"otherData\": {},\"traceEvents\":[";
        m_OutputStream.flush();
    }

    void WriteFooter()
    {
        m_OutputStream << "]}";
        m_OutputStream.flush();
    }

    static Instrumentor& Get()
    {
        static Instrumentor instance;
        return instance;
    }
};

class InstrumentationTimer
{
public:
    InstrumentationTimer(const char* name)
        : m_Name(name), m_Stopped(false)
    {
        m_StartTimepoint = std::chrono::high_resolution_clock::now();
    }

    ~InstrumentationTimer()
    {
        if (!m_Stopped)
            Stop();
    }

    void Stop()
    {
        auto endTimepoint = std::chrono::high_resolution_clock::now();

        long long start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).time_since_epoch().count();
        long long end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

        uint32_t threadID = std::hash<std::thread::id>{}(std::this_thread::get_id());
        Instrumentor::Get().WriteProfile({ m_Name, start, end, threadID });

        m_Stopped = true;
    }
private:
    const char* m_Name;
    std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
    bool m_Stopped;
};


namespace Benchmark
{
        void fn1()
        {
                PROFILE_FUNCTION();
                int value = 0;
                {
                        PROFILE_SCOPE("for loop");
                        for (int i = 0; i < 1000000; i++)
                        {
                                value += 2;
                        }
                }

                std::cout << value << std::endl;
        }

        void fn2()
        {
                PROFILE_FUNCTION();
                struct Vector2
                {
                        float x, y;
                };

                std::cout << "Make shared \n";
                {
                        PROFILE_SCOPE("make_shared");
                        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
                        for (int i = 0; i < sharedPtrs.size(); i++)
                        {
                                sharedPtrs[i] = std::make_shared<Vector2>();
                        }
                }

                std::cout << "New shared \n";
                {
                        PROFILE_SCOPE("new_shared");
                        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
                        for (int i = 0; i < sharedPtrs.size(); i++)
                        {
                                sharedPtrs[i] = std::shared_ptr<Vector2>(new Vector2());
                        }
                }

                std::cout << "Make unique \n";
                {
                        PROFILE_SCOPE("make_unique");
                        std::array<std::unique_ptr<Vector2>, 1000> uniquePtrs;
                        for (int i = 0; i < uniquePtrs.size(); i++)
                        {
                                uniquePtrs[i] = std::make_unique<Vector2>();
                        }
                }
        }
        void fn3()
        {
                PROFILE_FUNCTION();

                std::thread a([]() { fn1(); });
                a.join();
        }
}
#include "Instrumentor.h"
#include <iostream>
#include <array>
#include <memory>

namespace Benchmark
{
    void fn1()
    {
        PROFILE_FUNCTION();
        int value = 0;
        {
                PROFILE_SCOPE("for loop");
                for (int i = 0; i < 1000000; i++)
                {
                        value += 2;
                }
        }

        std::cout << value << std::endl;
    }

    void fn2()
    {
        PROFILE_FUNCTION();
        struct Vector2
        {
                float x, y;
        };

        std::cout << "Make shared \n";
        {
                PROFILE_SCOPE("make_shared");
                std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
                for (int i = 0; i < sharedPtrs.size(); i++)
                {
                        sharedPtrs[i] = std::make_shared<Vector2>();
                }
        }

        std::cout << "New shared \n";
        {
                PROFILE_SCOPE("new_shared");
                std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
                for (int i = 0; i < sharedPtrs.size(); i++)
                {
                        sharedPtrs[i] = std::shared_ptr<Vector2>(new Vector2());
                }
        }

        std::cout << "Make unique \n";
        {
            PROFILE_SCOPE("make_unique");
            std::array<std::unique_ptr<Vector2>, 1000> uniquePtrs;
            for (int i = 0; i < uniquePtrs.size(); i++)
            {
                    uniquePtrs[i] = std::make_unique<Vector2>();
            }
        }
    }
    void fn3()
    {
        PROFILE_FUNCTION();

        std::thread a([]() { fn1(); });
        a.join();
    }
}
int main()
{
    Instrumentor::Get().BeginSession("Profile");
    Benchmark::fn1();
    Benchmark::fn2();
    Benchmark::fn3();
    Instrumentor::Get().EndSession();
    return 0;
}
```

### 单例模式 Singleton

当我们想要拥有应用于某种完全数据集的功能，且我们只是想要重复使用时，单例是非常有用的。

单例的行为就像命名空间，单例累可以像命名空间一样工作。

C++ 中的单例只是一种组织一堆全局变量和静态函数的方式。

```C++
// 没有构造函数
class Random
{
public:
    // 删除拷贝构造函数
    Random(const Random&) = delete;
    // 删除赋值操作符（不可复制赋值操作符）
    Random& operator=(const Random&) = delete;
    /*
    // 上述删除了复制方法，以下将不被允许❌
    Random instance = Random::Get();
    
    // 必须写成引用
    Random& instance = Random::Get();
    */
    static Random& Get()
    {
        static Random instance;
        return instance;
    }
    static float Float()
    {
        return Get().IFloat();
    }
private:
    float IFloat()
    {
        return m_RandomGenerator;
    }
    Random() {}
    float m_RandomGenerator = 0.5f;
};

int main()
{
    // Random::Get().Float();
    Random::Float();
}
```



### 小字符串优化 small string optimization

15个字符 -- 小字符串

```C++
basic_string(_In_z const _Elem* const _Ptr) : xxx
{
    // xxx
    _Container_procy_ptr<_Alty> _Proxy(_Alproxy, _Mypair._Myval2);
}
```

### 跟踪内存分配 track memory allocations the easy way

自己的 `operator new / operator delete`

```C++
#include <iostream>
#include <memory>

static uint32_t s_AllocCount = 0;

struct AllocationMetrics
{
    uint32_t TotalAllocated = 0;
    uint32_t TotalFreed = 0;
    
    uint32_t CurrentUsage() { return TotalAllocated - TotalFreed; };
};

static AllocationMetrics s_AllocationMetrics;

void* operator new(size_t size)
{
    s_AllocCount++;
    s_AllocationMetrics.TotalAllocated += size;
    std::cout << "Allocating = " << size << "bytes \n";
    return malloc(size);
}

void operator delete(void* memory, size_t size)
{
    s_AllocationMetrics.TotalFreed += size;
    free(memory);
}

static void PrintMemoryUsage()
{
    std::cout << "Memory usage:" << s_AllocationMetrics.CurrentUsage() << " bytes\n";
}

struct Object
{
    int x,y,z;
};


void fn9()
{
    PrintMemoryUsage();
    {
        Object* obj = new Object;
        PrintMemoryUsage();
    }
    {
        std::unique_ptr<Object> obj = std::make_unique<Object>();    
        PrintMemoryUsage();
    }
    PrintMemoryUsage();
}

int main()
{
    fn9();
}
```

### 左值与右值 lvalues and rvalues

左值时某种存储支持的变量，右值时临时变量。

左值引用仅仅接受左值，除非使用 const

右值引用仅仅接受右值

Expression must be a modifiable lvalue

Located value

```C++
{
    // i -> 左值/10 -> 右值
    int i = 10;
    // 同上
    int temp = 10;
    // temp -> 左值 / a -> const 左值引用可以同时接受左值和右值
    const int& a = temp;
}

void PrintName1(std::string& name)
{
    std::cout << "lValue: " << name << std::endl;
}
// 重载
void PrintName1(const std::string&& name)
    {
        std::cout << "rValue: " << name << std::endl; // ok
    }

void PrintName2(const std::string& name)
{
    std::cout << name << std::endl;
}

void fn10()
{
    std::string firstName = "liu";
    std::string lastName = "xing";    
    
    std::string fullName = firstName + lastName;
    
    PrintName1(fullName);
    PrintName1(firstName + lastName); // no 报错
    
    PrintName2(firstName + lastName); // ok
    
    /*
    检测左值or右值
    写一个非常亮的左值引用，因为左值只能接受左值，否则编译器报错
    
    写一个函数，只接受临时对象（需要使用一个叫做右值引用 &&）
    
    */
}
```

### 持续集成（CI）continuous integration

构建自动化和测试

jenkins

jenkinsfile

```undefined
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Building..."'
                sh 'chmod +x scripts/Linux-Build.sh'
                sh 'scripts/Linux-Build.sh'
                archiveArtifacts artifacts: 'bin/Debug/*', fingerprint: true
            }
        }
        stage('Test') {
            steps {
                sh 'echo "Running..."'
                sh 'chmod +x scripts/Linux-Run.sh'
                sh 'scripts/Linux-Run.sh'
            }
        }
    }
}
```

### 静态分析 static analysis

pvs-studio

```undefined
EXTENSIONS -- PVS-Studio -- Check -- Current Project/...
```

### 参数计算顺序  argument evaluation order

```C++
void Function(int a, int b, int c)
{

}

/*
 undeifine behavior 未定义行为
 完全依赖于 c++ 编译器将代码转换成机器码的实际表现
*/
{
    int a = 2;
    Function(GetResult(), ++a, a--);
}

/*
c++17
The postfix-expression is sequenced before each expression in the expression list
and andy default argument. The initialization of a parameter, including every associated
value computation and side effect, is indeterminately sequenced with respect to that
of any other parameter.
后缀表达式必须在别的表达式之前被计算，对于这些参数，它们必须一个接一个地被计算
但他们被计算的顺序仍然是不确定的。
*/
```

### 移动语义 Move Semantics

很多情况下我们不需要或者不想把一个对象从一个地方复制到另一个地方，但又不得不复制，因为这是唯一可以复制的地方。

例如：

1. 如果我把一个对象传递给一个函数，那么它要获得那个对象的所有权，就需要拷贝。
   1. 在当前堆栈帧中构造一个一次性对象，不管它在哪里，然后将它复制到我正在调用的函数中
2. 当我想从函数返回一个对象时也是一样的，需要在函数中创建那个对象，然后返回它（ps.返回值优化)

移动对象，而不是复制，性能会更高。

```C++
class String
{
private:
    char* m_Buffer;
    unsigned int m_Size;
    
public:
    String() = default;
    String(const char* string)
    {
        printf("create!\n");
        m_Size = strlen(string);
        // 注意：空终止字符
        m_Buffer = new char[m_Size+1];
        // memcpy(m_Buffer, string, m_Size+1);
        memcpy(m_Buffer, string, m_Size);
        m_Buffer[m_Size] = 0;
    }
    
    // move 构造函数
    String(String&& other)
    {
        /*
         接管了那个旧字符串，而不是通过复制所有数据和分配新的内存
         来进行深度复制。
         以下实际上做了一个浅拷贝
        */
        printf("move!\n");
        m_Buffer = other.m_Buffer;
        m_Size = other.m_Size;

        other.m_Size = 0;
        other.m_Buffer = nullptr;
    }
    
    String(const String& other)
        : m_Size(other.m_Size)
    {
        printf("copy!\n");
        m_Buffer = new char[m_Size+1];
         memcpy(m_Buffer, other.m_Buffer, m_Size+1);
    }
    
    ~String()
    {
        printf("delete!\n");
        delete[] m_Buffer;
    }
    
    void Print()
    {
        for (uint32_t i = 0; i < m_Size; i++)
        {
            printf("%c", m_Buffer[i]);
        }
        printf("\n");
    }
    
    friend std::ostream& operator<<(std::ostream& stream, const String& string);
};

class Entity
{
public:
    // 对传进来的字符串，复制到 m_Name (分配空间)
    Entity(const String& name)
        : m_Name(name)
    {
    }
    
    // move 构造函数
    /*
    Entity(String&& name)
        : m_Name((String&&)name)
    {
    }
    */
    Entity(String&& name)
        : m_Name((std::move)name)
    {
    }
    
    void PrintName()
    {
        m_Name.Print();
    }
private:
    String m_Name;    
};

void fn11()
{
    // 这里创建字符串，然后传递到 Entity 构造函数
    // 构造函数把字符串复制到 m_Name
    // 为什么不能把字符串移动到这儿？为什么不能直接放到 m_Name 分配的内存里
    Entity entity("liuxing");
    entity.PrintName();

}
```

### std::move 与移动赋值操作符

移动赋值操作符是你想要包含在类中的东西，当你包含一个移动构造函数时，因为它当然是想要将一个对象移动到现有的变量中。

C++ 三法则：

- 如果需要析构函数，一定需要拷贝构造函数和拷贝赋值操作符

C++ 五法则（为了支持移动语义，又增加了）：

- 移动构造函数 和 移动赋值运算符

```C++
{
    /*
     // 将字符串移动到 dest，同上，但是使用 std::move 更加优雅
     // std::move 是你想要将一个对象转换为临时对象时要做的
    String string = "Hello";
    
    // 移动构造函数
    // String dest((String&&)string); == String dest((std::move)string);
    
    String ap = "apple";
    String dest;
    // 移动赋值运算符
    dest = std::move(ap);
    */
}

// 接上一个例子🌰

{
    /*
    通常在赋值操作符中，你还需要确保这个对象，也就是当前对象，不等于另一个对象。
    如果是同一个对象，那确实没必要移动
    dest = std::move(dest); // ❌
    */
    String& operator=(String&& other)
    {
        if (this != &other)
        {
            printf("= \n");
            
            delete[] m_Buffer;
            
            m_Size = other.m_Size;
            m_Buffer = other.m_Buffer;
            
            other.m_Size = 0;
            other.m_Buffer = nullptr;
        }
        return *this;
    }
}
```



### Array

Fixed size stack allocated array data structure in c++

```C++
template<typename T, size_t S>
class Array
{
    T m_Data[S];
public:
    constexpr int Size() const { return S; }
    
    // for the potential other data types
    T& operator[](size_t index) 
    {
        if (!(index < S))
        {
            // msvc
            __debugbreak();
        }
        return m_Data[index];
    }
    
    const T& operator[](size_t index) const { return m_Data[index]; }  
    
    
    T* Data() { return m_Data;}
    const T* Data() const { return m_Data;}    
};


void fn12()
{
    int size = 5;
    Array<int 5> data;
    
    // 断言
    static_assert(data.Size() < 10, "Size is too large!");
    
    const auto& arrayRef = data;
    
    memset(data.Data(), 0, data.Size() * sizeof(int));
    // memset(&data[0], 0, data.Size() * sizeof(int));
    
    for (int i = 0; i< data.Size(); i++)
    {
        data[i] = 2;
        // arrayRef[i] = 2; // ❌
        
        
        std::cout << data[i] << std::endl;
        
        std::cout << arrayRef[i] << std::endl; // ok
    }
}
```

### Vector

```C++
#pragma once

/*
//1
//2
The 1 2 is we change the way  allocate and deallocate memory
TODO
shrinking
emplace or push into middle of the vector
replace or remove element form the middle of vectors
iterator
*/

template<typename T>
class Vector
{
public:
        Vector()
        {
                ReAlloc(2);

        }
        void PushBack(const T& value)
        {
                if (m_Size >= m_Capacity)
                {
                        // growing this by 1.5 times every time
                        ReAlloc(m_Capacity + m_Capacity / 2);
                }
                m_Data[m_Size] = value;
                m_Size++;
        }

        void PushBack(T&& value)
        {
                if (m_Size >= m_Capacity)
                {
                        // growing this by 1.5 times every time
                        ReAlloc(m_Capacity + m_Capacity / 2);
                }
                m_Data[m_Size] = std::move(value);
                m_Size++;
        }

        template<typename... Args>
        T& EmplaceBack(Args&&... args)
        {
                if (m_Size >= m_Capacity)
                {
                        ReAlloc(m_Capacity + m_Capacity / 2);
                }
                // constructing a temporary here which is then moving
                // m_Data[m_Size] = T(std::forward<Args>(args)...);
                // new for constructing objects
                // associate that with heap allocations
                // grab the memory address of this element
                new(&m_Data[m_Size]) T(std::forward<Args>(args)...);
                m_Size++;
                return m_Data[m_Size];
        }

        void PopBack()
        {
                if (m_Size > 0)
                {
                        m_Size--;
                        // manual call destructor
                        m_Data[m_Size].~T();
                }
        }

        void Clear()
        {
                for (size_t i = 0; i < m_Size; i++)
                {
                        // manual call destructor
                        m_Data[i].~T();
                }
                m_Size = 0;
        }

        // const version
        const T& operator[](size_t index) const
        {
                // assert overflow
                return m_Data[index];
        }
        // none const version
        T& operator[](size_t index)
        {
                // assert overflow
                return m_Data[index];
        }

        size_t Size() const { return m_Size; }

        ~Vector()
        {
                //1
                // delete[] m_Data;
                Clear();
                ::operator delete(m_Data, m_Capacity * sizeof(T));
        }
private:
        /*
        void ReAlloc(size_t newCapacity)
        {
                // 0. overflow
                if (newCapacity < m_Size)
                {
                        m_Size = newCapacity;
                }
                // 1. allocate a new block of memeory
                // shouldn't be any constructors being called here at all
                T* newBlock = new T[newCapacity];
                // 2. copy/move old element to new block
                for (size_t i = 0; i < m_Size; i++)
                        newBlock[i] = std::move(m_Data[i]);
                // 3. delete
                //2
                delete[] m_Data;

                m_Data = newBlock;
                m_Capacity = newCapacity;
        }
        */
        void ReAlloc(size_t newCapacity)
        {
                // 0. overflow
                if (newCapacity < m_Size)
                {
                        m_Size = newCapacity;
                }
                // 1. allocate a new block of memeory
                // malloc enough memory to fill up this new capacity
                T* newBlock = (T*)::operator new(newCapacity * sizeof(T));
                // 2. copy/move old element to new block
                for (size_t i = 0; i < m_Size; i++)
                        newBlock[i] = std::move(m_Data[i]);
                // 3. delete
                for (size_t i = 0; i < m_Size; i++)
                {
                        m_Data[i].~T();
                }
                ::operator delete(m_Data, m_Capacity * sizeof(T));

                m_Data = newBlock;
                m_Capacity = newCapacity;
        }
        
private:
        T* m_Data = nullptr;
        size_t m_Size = 0;
        // an initial size or an initial allocation
        // to prevent having to reallocate
        size_t m_Capacity = 0;
};
#include <iostream>
#include "Vector.h"


struct Vector3
{
        float x = 0.0f;
        float y = 0.0f;
        float z = 0.0f;
        int* m_MemoryBlock = nullptr;

        Vector3()
        {
                m_MemoryBlock = new int[5];
        }
        Vector3(float scalar)
                : x(scalar), y(scalar), z(scalar)
        {
                m_MemoryBlock = new int[5];
        }
        Vector3(float x, float y, float z)
                : x(x), y(y), z(z)
        {
                m_MemoryBlock = new int[5];
        }
        Vector3(const Vector3& other) = delete;
        /*
        Vector3(const Vector3& other)
                : x(other.x), y(other.y), z(other.z)
        {
                std::cout << "copyed! \n";
        }
        */
        Vector3(Vector3&& other)
                : x(other.x), y(other.y), z(other.z)
        {
                m_MemoryBlock = other.m_MemoryBlock;
                other.m_MemoryBlock = nullptr;
                std::cout << "copyed! \n";
        }

        Vector3& operator=(const Vector3& other) = delete;
        /*
        Vector3& operator=(const Vector3& other)
        {
                if (this != &other)
                {
                        std::cout << "Copy \n";
                        x = other.x;
                        y = other.y;
                        z = other.z;
                }

                return *this;
        }
        */

        Vector3& operator=(Vector3&& other)
        {
                if (this != &other)
                {
                        std::cout << "move \n";
                        m_MemoryBlock = other.m_MemoryBlock;
                        other.m_MemoryBlock = nullptr;
                        x = other.x;
                        y = other.y;
                        z = other.z;
                }

                return *this;
        }

        ~Vector3()
        {
                std::cout << "destoryed! \n";
                delete[] m_MemoryBlock;
        }
};


template<typename T>
void PrintVector(const Vector<T>& vector)
{
        for (size_t i = 0; i < vector.Size(); i++)
        {
                std::cout << vector[i] << std::endl;
        }
}

template<>
void PrintVector(const Vector<Vector3>& vector)
{
        for (size_t i = 0; i < vector.Size(); i++)
        {
                std::cout << vector[i].x << "," << vector[i].y << "," << vector[i].z << std::endl;
        }
}

int main()
{
        /*
        Vector<std::string> vector;
        vector.PushBack("liuxing");
        vector.PushBack("yangzi");
        vector.PushBack("xiaopang");

        PrintVector(vector);
        */

        /*
        Vector<Vector3> vector;
        vector.PushBack(Vector3(1.0));
        vector.PushBack(Vector3(1.0,2.0,3.0));
        vector.PushBack(Vector3());
        PrintVector(vector);
        */
        {
                Vector<Vector3> vector;
                vector.EmplaceBack(1.0f);
                vector.EmplaceBack(2.0f, 3.0f, 4.0f);
                vector.EmplaceBack(1.0f, 3.0f, 4.0f);
                vector.EmplaceBack();
                PrintVector(vector);
                vector.PopBack();
                vector.PopBack();
                PrintVector(vector);
                vector.EmplaceBack(5.0f, 2.0f, 0.0f);
                vector.EmplaceBack(1.0f, 7.0f, 9.0f);
                PrintVector(vector);

                vector.Clear();
                PrintVector(vector);
                vector.EmplaceBack(5.0f, 2.0f, 0.0f);
                vector.EmplaceBack(1.0f, 7.0f, 9.0f);
                PrintVector(vector);

                PrintVector(vector);
        }

        //std::cin.get();
}
```