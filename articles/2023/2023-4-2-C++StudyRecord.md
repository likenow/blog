## C ++ 学习笔记

> 感谢 https://www.youtube.com/@TheCherno 的视频教学！！！

### 编译

### 链接

### 变量

不同变量类型之间唯一区别是，这个变量会占用多少内存。（依赖编译器）

### 函数

### 头文件

```C++
#pragma once

// avoid redefine
#ifdef _LOG_H
#define _LOG_H



#endif
// 尖括号只用于编译器包含路径，引号可以做一切。但我通常只用它在相对路径。
#include< > //引用的是编译器的类库路径里面的头文件。
#include"" //引用的是你程序目录的相对路径中的头文件。

#include< > //编译程序会先到标准函数库中调用文件。
#include"" //编译程序会先从当前目录中调用文件。

#include< > //指示预处理程序到预定义的缺省路径下寻找文件。
#include"" //指示预处理程序先到当前目录下寻找文件，再到预定义的缺省路径下寻找文件。 
```

### 调试

Visual Studio

- Breakpoint

-  Autos 变量

-  Locals 局部变量

-  watch 观察变量

-  memory 内存视图（Debug -> windows -> memory -> memory1）

-  以十六进制格式表示的实际值

-  CC 数字实际上是十六进制数，意味着它是未初始化的栈内存

### 条件与分支

查看汇编，确保您处于调试模式并确保已经尽可能多的关闭了优化。

```C++
if (condition) {
    // do something 
} else if (condition) {

}

// 上述实际上是如下：
if (condition) {
    // do something 
} else {
    if (condition) {
    
    }
}
```

### Visual Studio

配置

```undefined
Show all files


src folder

Configuration: all configurations
platform: all platforms
General:
output directory -- $(SolutionDir)\bin\$(Platform)\$(Configuration)\
Intermediate directory  -- $(SolutionDir)\bin\intermediates\$(Platform)\$(Configuration)\
```

### 循环

```C++
bool conition = true;
for ( ; condition ; ) {

}

for ( ; ; ) {

}

while (condition) {


}

do {

} while(condition);
```

### 控制语句

```undefined
continue

break

return
```

### 指针

指针只是一个地址，它是一个保存内存地址的整数。

```C++
int main() {
    int var = 8;
    void* ptr = &var;
    int *ptr1 = &var;
    *ptr1 = 9;
    void* ptrZero = nullptr; // NULL
}
```

### 引用

语法糖～～

```C++
#include <iostream>

// 引用是语法糖
void plus(int& value) {
        value++;
}

void plusp(int* value) {
        // 加括号是因为，我们不是给指针递增，而是给指针指向的内容递增
        (*value)++;
}

void Log(const char* message) {
        std::cout << message << std::endl;
}

int main() {
        
        for (int i = 0; i < 5; i++) {
                Log("hello world");
        }
        int a = 8;
        plus(a);
        std::cout << a << std::endl;

        plusp(&a);
        std::cout << a << std::endl;

        std::cin.get();
        return 0;
}
```

### 类

类只是对数据和功能组合在一起的一种方法。

Private 默认

```Java
class Player {
public:
        int x, y;
        int speed;

        void Move(int xa, int ya) {
                x += xa * speed;
                y += ya * speed;
        }
};
```

通过类实例引用静态变量是没有意义的，静态方法也是一样，无法访问类的实例。

### 结构体

和类的区别，可见度（默认public），与C保持向后兼容性。

```C++
struct Player {
private:
        int x, y;
        int speed;

        void Move(int xa, int ya) {
                x += xa * speed;
                y += ya * speed;
        }
};
#define struct class
```

用法的区别，没有正确答案，取决于编程风格。

Plain old data (POD) ，（一堆变量）尽可能的使用 struct -- 数据结构

附加很多功能时，class

不会在继承中使用 struct

语义

### Static

- 在结构体或类内部使用static 关键字
  - 
- 在结构体或类外部使用static 关键字
  - 他只会在声明的 c++ 文件中被“看到”
  - 如果你不需要变量是全局变量，你就需要尽可能多的使用静态变量
  - 如果没有设定为 static ，链接器会跨编译单元进行链接

External linkage or external linking

- extern，在外部翻译单元中寻找

### Local Static

- 生命周期（在内存中存多久）
- 作用域（访问变量的范围）

### 枚举

Enumration

```C++
enum Example {
    A,
    B,
    C
}
```

### 构造函数

构造函数是一种特殊类型的方法，这是一种每次你构造一个对象时都会调用的方法。

必须手动初始化所有基本类型

```C++
class Log {
public:
    // 删除默认的构造函数
    Log() = delete;

}
```

### 析构函数

卸载变量等，并清理你使用过的内存

很少见手动调用析构函数的！！！

### 继承

### 虚函数

Dynamic Dispatch 动态联编

它通常通过 v 表（虚函数表--包含基类中所有虚函数的映射）来实现编译。

虚函数表--包含基类中所有虚函数的映射，这样我们可以在它运行时，将他们映射到正确的复写（override）函数

```C++
class Player {
public:
        virtual std::string GetName() { return "Player"; }
};

class PlayerPro : public Player {
private:
        std::string m_Name;

public:
        const char* name;

        PlayerPro(const std::string& name)
                : m_Name(name) {}

        std::string GetName() override { return m_Name; }

};

void printName(Player* p) {
        std::cout << p->GetName() << std::endl;
}

int main() {
    Player* p = new Player();
    printName(p); // Player

    PlayerPro* pp = new PlayerPro("liuxing");
    printName(pp); // liuxing
    
    std::cin.get();
}
```

### 纯虚函数

类似于java 或 C# 中的抽象方法或接口

纯虚函数允许我们在基类中定义一个没有实现的函数，然后强制子类去实现该函数。

```C++
#include <iostream>
#include <string>

class Printable {
public:
        virtual std::string GetClassName() = 0;
};

class MyEntity : public Printable {
public:
        std::string GetClassName() override {
                return "MyEntity";
        };
};

class A : public MyEntity, Printable {
public:
        std::string GetClassName() override { return "A"; }
};

void Print(Printable* obj) {
        std::cout << obj->GetClassName() << std::endl;
}
```

### 可见性

- Private
- Protected
- public

Friend 是 C++ 关键字，它可以让类或者函数成为类的朋友（友元）

### 数组

安全检查，确保没有越界

使用 new 关键字来分配内存，它将一直存在，直到你把它删除。

如果你有一个函数返回一个数组，你必须使用一个 new 来分配它，除非你传入一个数组的地址参数。

间接寻址 memory indirection

```C++
/*比如对象e 包含另一个地址，是数组的实际内存地址，
这意味着我们访问数组时，要在代码周围跳来跳去，首先找到 e，然后找到数组，然后拿到数据。
应该在栈上创建数组来避免这种情况，以内像这样Z爱内存中跳跃肯定会影响性能。
*/

class Entity {
public:
    int* example = new int[5];
    
    Entity() {
        for (int i = 0; i< 5; i++)
            example[i] = 3;
    }
};

int main() {
    Entity e;
    
    std:cin.get();

}
```

内存碎片 memory fragmentation

缓存丢失 cache miss

```
c++ 11 std::array
#include <array>

class Entity {
public:
    std::array<int, 5> test;
    
    Entity() {
        for (int i = 0; i< test.size(); i++)
            test[i] = 3;
    }
};

int main() {
    Entity e;
    
    std:cin.get();

}
```

你永远不应该在数组内存中，访问数组的大小。dangerous！

```undefined
size 字节
count 数量
```

自己维护数组的大小

```C++
class Entity {
public:
    static constexpr int testSize = 5; 
    int* test[testSize];
    
    Entity() {
        for (int i = 0; i< testSize; i++)
            test[i] = 3;
    }
};

int main() {
    Entity e;
    
    std:cin.get();

}
```

### 字符串

char 字符，一个字节，单引号

char* 字符串，双引号

空终止字符 `0`

```Plain
/*
它是一个 char 数组和一些函数，用来操作他们（char 数组）
*/
std::string
c++ 标准库 string

BasicString -- 模板类
string 基本上是 basicString 类的模板版本

template specialization
```

当你把类（对象）传递给一个函数时，你实际上是在复制这类（对象）

```C++
void PrintStirng(std::string string) {
    std::cout << string << std::endl;
}


// vs
// 当你传递一个这样的字符串，而且是只读的情况下，确保通过常量应用传递它
// 因为字符串拷贝一份到堆上并不是一个很快的操作
void PrintStirng(const std::string& string) {
    std::cout << string << std::endl;
}
```

通过常量引用来传递参数，特别是那些只读的东西，没必要copy分配内存

### 字符串字面量

永远保存在内存的只读区域。

```C++
"" // const char[]

strlen() // 遇到 0 计算即结束了

// 以下可能会导致未定义行为，应禁止使用！！！
char* name = "liucing";
name[2] = 'a';

// 应该如下：
const char* name = "liuxing";

// 宽字符串
const wchar_t* name2 = L"liuxing";

const char16_t* name3 = u"liuxing";
const char32_t* name4 = U"liuxing"; // 4字节字符


// 拼接
std::string name5 = "liuxing"s + "hello";


// R 忽略转义字符
const char* example = R"(Line1
line2
line3)";
```

### CONST

不变承诺

```C++
// 1 const 在星号之前
const int* a = new int; // 等价于 int const* a = new int;
*a = 2; // 指针指向的内容不可变，但指向可变

// 2 const 在星号之后
int* const b = new int;
*a = 2; // 可以改变指针指向的内容，但是指针指向不可变


// 3 在参数里表之后写上 const（只在类中有效）
class Entity {
private:
    int m_X, m_Y;
    int* m_W, *m_H;
public:
    int GetX() const {
        return m_X;
    }
    
    void SetX(int x) {
        m_X = x;
    }
    
    int GetY() const {
        return m_Y;
    }
    void SetY(int y) {
        m_Y = y;
    }
    
    // 返回一个不能修改的指针，指针内容也不能修改，这个方法承诺不修改实际的Entity 类
    const int* const GetW() const {
        return m_W;
    }
};

// 4 mutable 允许函数是常量方法，但可以修改变量。
class Entity {
private:
    int m_X;
    mutable int var;
public:
    int GetX() const {
        var = 2; // var 可在此方法中修改
        return m_X;
    }
    
}
```

### Mutable

- 与 const 一起使用

```C++
class Entity {
private:
    std::string m_Name;
    mutable int m_DebugCount = 0;
public:
    const std::string& GetName() const {
        m_DebugCount++;
        return m_Name; 
    }    
}

int main() {

    const Entity e;
    e.GetName();
    
    std::cin.get();
}
```

- 与 Lambda 一起使用

```C++
    int x = 8;
    // [&x] 通过引用发送这个变量
    // [x] 直接传值传递
    // [=] 对所有的变量进行传值传递
    // [&] 对所有的变量进行引用传递
    /*
    auto f = [?]() {

        std::cout << "Hello" << std::endl;
    };
    */
    
    auto f = [=]() mutable {
        x++;
        std::cout << "inner =" << x << std::endl;
    };

    auto f1 = [&]() mutable {
        x++;
        std::cout << "inner 1=" << x << std::endl;
    };


    f();
    std::cout << "out=" << x << std::endl;
    f1();
    std::cout << "out1=" << x << std::endl;
```

### C++ 的成员初始化列表

初始化成员变量

按照成员变量声明时的顺序，否则有些编译器可能会报错。

### 三元操作符

Condition ? A : B;

可以嵌套

### 创建并初始化 C++ 对象

对象在堆上or栈上，它们有不同的功能差异。

```C++
    Entity entity; // 实际调用了默认构造函数
    std::cout << entity.GetName() << std::endl;

    Entity* ety;
    {
        Entity entity1("wuyong"); //Entity entity1 = Entity("liuxing");
        ety = &entity1;
        std::cout << entity1.GetName() << std::endl;
    }
    
    {
        Entity* entity1 = new Entity("luxun"); //Entity entity1 = Entity("liuxing");
        ety = entity1;
        std::cout << (*entity1).GetName() << std::endl;
        // std::cout << entity1->GetName() << std::endl;
    }


    std::cin.get();
    delete ety;
```

堆上创建，需要手动释放。

推荐创建在栈上。

### New 关键字

New 是一个操作符，这意味着你可以重载这个操作符，并改变它的行为。

New 在堆上分配（足够大的）内存。

空闲列表。

```C++
int* b = new int; // 一个单一的四字节，在堆上分配
int* c = new int[50]; // 连续的 50 个四字节大小

int* d = (int*)malloc(sizeof(int));

Entity* e = new Entity();
Entity* e1 = (Entity*)malloc(sizeof(Entity)); // 与 e 的区别在于，不会调用构造函数

// new()
Entity* e2 = new(e) Entity();

delete b;
delete c;
delete[] d;

delete e;
delete e1;
```

### 隐式转换与 explicit 关键字

```C++
    Entity a("liuxing");
    Entity b(22);

    // Entity c = String("liuxing");
    Entity d = (Entity)22; //Entity d = 22;
```

### 运算符及重载

重载本质是给运算符重载赋予新的含义，或者添加参数，或者创建。

允许在程序中定义或更改运算符的行为。

运算符就是函数！！！

```C++
struct Vector2 {
    float x, y;
    Vector2(float x, float y)
        : x(x), y(y) {}

    /*
    Vector2 Add(const Vector2& other) const
    {
        return Vector2(x + other.x, y + other.y);
    }

    Vector2 operator+(const Vector2& other) const
    {
        return Add(other);
    }
    */
    
    Vector2 Add(const Vector2& other) const
    {
        return *this + other;
    }

    Vector2 operator+(const Vector2& other) const
    {
        return Vector2(x + other.x, y + other.y);
    }
    /*
    Vector2 Add(const Vector2& other) const
    {
        return operator+(other);
    }

    Vector2 operator+(const Vector2& other) const
    {
        return Vector2(x + other.x, y + other.y);
    }
    */
    Vector2 Muti(const Vector2& other) const
    {
        return Vector2(x * other.x, y * other.y);
    }
    Vector2 operator*(const Vector2& other) const
    {
        return Muti(other);
    }

    bool operator==(const Vector2& other) const
    {
        // Warning 浮点数这么直接比较是不安全的
        return x == other.x && y == other.y;
    }

    bool operator!=(const Vector2& other) const
    {
        // Warning 浮点数这么直接比较是不安全的
        // return x == other.x && y == other.y;
        return !(*this == other);
    }

};
```

### This 关键字

访问成员函数

This 是一个指向当前对象实例的指针，该方法属于这个对象实例。

```C++
// 通过常量引用来传递参数，特别是那些只读的东西，没必要copy分配内存
void PrintEntity0(const Entity0& e);

class Entity0
{
public:
    int x, y;
    /*
    Enity(int x, int y)
    {
        Entity* const & const e = this;
        e->x  = x;
    }
    */
    Entity0(int x, int y)
    {
        (*this).x = x;
        (*this).y = y;

        Entity0& e = *this;

        PrintEntity0(e);

        // delete this; // pro!!! 非常专业的操作⚠️
    }

    int GetX() const
    {
        const Entity0& e = *this;
        return e.x;
    }
};

void PrintEntity0(const Entity0& e)
{

}
```













---



### 用 c++ 编写桌面程序的最佳方法

Dear ImGui

imGUI 如果你有非常大的数据集，如果他们在每一帧都被渲染刷新，你可能会遇到一些问题。

即时模式 immediate mode

每次都会重新绘制所有的元素，绝大多数游戏是这类模式。

保留模式 retain mode

当需要改变时，执行的是改变的处理，绝大多数应用程序是用的这种模式。

