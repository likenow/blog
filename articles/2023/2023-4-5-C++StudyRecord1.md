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

**对自己的库使用 using namespace，在足够小可控的****作用域****下使用。**

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

### 线程

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

### 计时

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

### 多维数组

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

### 排序

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

### 类型双关

用来在  c++ 中绕过类型系统

把拥有的这段内存，当作不同类型的内存来对待。

```C++
void f4()
{
    int a = 50;
    double value = a; // 隐式类型转换 = *(double*)&a;
    double value1 = (double)a; // 显示类型转换
    std::cout << value << std::endl;
}

void f5()
{
    int a = 50;
    double value = a; // 隐式类型转换 = *(double*)&a;
    value = 0.0; // 可能会导致 crash
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

### 联合体

Union

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

### 虚析构函数

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

### 类型转换

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

### 条件与操作断点

调试

Conditions

​	条件

actions

​	Continue execution

断点会拖慢程序

