## C ++ 学习笔记

> 感谢 https://www.youtube.com/@TheCherno 的视频教学！！！

[toc]



### 编译 compiler

### 链接 linker

### 变量 variables

不同变量类型之间唯一区别是，这个变量会占用多少内存。（依赖编译器）

### 函数 functions

### 头文件 header files

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

### 调试 Debug in Visual Studio

Visual Studio

- Breakpoint

-  Autos 变量

-  Locals 局部变量

-  watch 观察变量

-  memory 内存视图（Debug -> windows -> memory -> memory1）

-  以十六进制格式表示的实际值

-  CC 数字实际上是十六进制数，意味着它是未初始化的栈内存

### 条件与分支 conditions and branches

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

```
// 不使用 vs 的虚拟目录
Show all files

// 新建 folder
src folder

// 新增文件
add new item

// project --> properties
Configuration: all configurations
platform: all platforms
General:
output directory -- $(SolutionDir)bin\$(Platform)\$(Configuration)\
Intermediate directory  -- $(SolutionDir)bin\intermediates\$(Platform)\$(Configuration)\
```

### 循环 loops

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

### 控制语句 control flow

```undefined
continue

break

return
```

### 指针 Pointers

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

### 引用 references

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

### 类 classes

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

### 结构体 structs

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

```c++
class MyClass {
public:
  static void staticFunction() {
    // 不能访问非静态成员变量 x
    // 只能访问静态成员变量 y
    y++;
  }

private:
  int x;
  static int y;
};

int MyClass::y = 0; // 类静态成员变量需要在类外部进行定义和初始化

int main() {
  MyClass::staticFunction();
  return 0;
}

```



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

### 枚举Enums

Enumration

```C++
enum Example {
    A,
    B,
    C
}
```

### 构造函数 constructors

构造函数是一种特殊类型的方法，这是一种每次你构造一个对象时都会调用的方法。

必须手动初始化所有基本类型

```C++
class Log {
public:
    // 删除默认的构造函数
    Log() = delete;

}
```

### 析构函数 destructors

卸载变量等，并清理你使用过的内存

很少见手动调用析构函数的！！！

### 继承 inheritance

### 虚函数 virtual functions

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

### 纯虚函数 interfaces in c++(pure virtual functions)

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

### 可见性 visibility

- Private
- Protected
- public

Friend 是 C++ 关键字，它可以让类或者函数成为类的朋友（友元）

### 数组 arrays

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

### 字符串 strings

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
// 当你传递一个这样的字符串，而且是只读的情况下，确保通过常量引用传递它
// 因为字符串拷贝一份到堆上并不是一个很快的操作
void PrintStirng(const std::string& string) {
    std::cout << string << std::endl;
}
```

通过常量引用来传递参数，特别是那些只读的东西，没必要copy分配内存

### 字符串字面量 string literals

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
*a = 2; // 指针指向的内容不可变，但指向可变，编译报错❌

// 2 const 在星号之后
int* const b = new int;
*a = 2; // 可以改变指针指向的内容，但是指针指向不可变


// 3 在参数列表之后写上 const（只在类中有效）
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

### C++ 的成员初始化列表 member initializer lists(constructor initializer list)

初始化成员变量

按照成员变量声明时的顺序，否则有些编译器可能会报错。

### 三元操作符 ternary operators

Condition ? A : B;

可以嵌套

### 创建并初始化 C++ 对象 how to create/instantiate objects in c++

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

### 隐式转换(implicit conversion)与 explicit 关键字

```C++
{
  
  	// explicit key word
    explicit Entity(int sorce)
        : m_Name("Unknow"), m_Sorce(0) {

    }
}

{
		Entity a("liuxing");
    Entity b(22);

    // Entity c = String("liuxing");
  	//Entity d = 22;
  	// no suitable constructor exists to convert from "int" to "Entity"
    Entity d = (Entity)22; 
}
```

### 运算符及重载 operators and operator overloading

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



### 对象生存周期（栈作用域生存期）object lifetime

以下是问题代码：

```C++
int* CreateArray()
{
    int array[50];
    return array;
}

int main()
{
    int* a = CreateArray();
}
```

常见的错误：创建一个基于栈的变量，然后尝试返回指向它的指针。

```C++
// 直接在堆上分配
int main()
{
    int* a = new int[50];
}

// 赋值给一个在栈作用域之外存在的变量
void CreateArray(int* array)
{
    // file our array
}

int main()
{
    int array[50];
    int* a = CreateArray(array);
    
    
}
```

**作用域指针**，它基本上是一个类，它是一个指针的包装器，在构造时用堆分配指针然后在析构时删除指针。

```C++
class ScopedPtr
{
private:
    Entity0* m_Ptr;
public:
    ScopedPtr(Entity0* ptr)
        : m_Ptr(ptr)
    {
    }

    ~ScopedPtr()
    {
        delete m_Ptr;
    }
};
```

### 智能指针 smart pointers

本质上是原始指针的包装

在很多情况下使用智能指针，你调用了 new，不需要手动调用 delete

#### unique_ptr

唯一

作用域指针，是超出作用域时，它会被销毁，然后调用 delete

```C++
    {
        // std::unique_ptr<EntityZero> entity(new EntityZero());
        // meke_unique 出于异常安全建议使用该方法
        // 最终不会得到一个没有引用的悬空指针，从而造成内存泄漏
        std::unique_ptr<EntityZero> entity = std::make_unique<EntityZero>();
        // std::unique_ptr<EntityZero> e2 = entity; // 这是不被允许的
        // unique_ptr(const unique_ptr&)            = delete;
        // unique_ptr& operator=(const unique_ptr&) = delete;
        std::cout << entity->GetX() << std::endl;
    }
```

#### shared_ptr

实现的方式实际上取决于编译器和你在编译器中使用的标准库。

引用计数，可以跟踪你的指针有多少个引用。

```C++
std::shared_ptr<EntityZero> e0;
{
    // 增加引用计数 + 分配 EntityZero  内存
    std::shared_ptr<EntityZero> e3 = std::make_shared<EntityZero>();
    std::cout << e3->GetX() << std::endl;
    // 增加引用计数
    e0 = e3;
    // weak_ptr 不会增加引用计数 + 分配 EntityZero  内存
    std::weak_ptr<EntityZero> e5 = std::make_shared<EntityZero>();
    
}
```

shared_ptr 需要分配另一块内存，叫做控制块，用来存储引用计数。

#### weak_ptr

### 复制与拷贝构造函数 copying and copy constructors

对于只读的，或者修改已经存在的对象，尽量避免复制，因为复制是增加开销的。

当你使用赋值操作符，将一个变量设置为另一个变量时，你总是在复制值。除了引用，因为引用只是在改变指针的指向，并没有复制。

在指针的情况下，你在复制指针，也就是内存地址，内存地址的数字，就是数字而已。而不是指针指向的实际内存。

```C++
struct Vector
{
public 
    float x, y;
};

int main()
{
    vector* a = new Vector();
    vector* b = a;
    b->x = 2;
}
class String
{
private:
    char* m_Buffer;
    unsigned int m_Size;
    
public:
    String(const char* string)
    {
        m_Size = strlen(string);
        // 注意：空终止字符
        m_Buffer = new char[m_Size+1];
        // memcpy(m_Buffer, string, m_Size+1);
        memcpy(m_Buffer, string, m_Size);
        m_Buffer[m_Size] = 0;
    }
    
    String(const String& other)
        : m_Size(other.m_Size)
    {
        m_Buffer = new char[m_Size+1];
         memcpy(m_Buffer, other.m_Buffer, m_Size+1);
    }
    /**
    // 不可拷贝
    String(const Stirng& other) = delete;
    
    */
    /**
    
    String(const String& other)
        : m_Buffer(other.m_Buffer), m_Size(other.m_Size)
    {
        
    }
    */
    
    /**
    String(const Stirng& other)
    {
        memcpy(this, &other, sizeof(String));
    }
    */
    
    ~String()
    {
        delete[] m_Buffer;
    }
    friend std::ostream& operator<<(std::ostream& stream, const String& string);
};

std::ostream& operator<<(std::ostream& stream, const String& string)
{
    // friend
    stream << string.m_Buffer;
    return stream;
}

void PrintString(const String& string)
{
    std::cout << string << std::endl;
}

int main()
{
    /**
    以下代码存在问题：
    内存中有两个 string ---- 有相同的 char* 指针
    因为他们直接进行了复制（浅拷贝）
        复制 char*
        复制 int
    程序打印结束的时候，析构函数会被调用，
    然后执行 delete[] m_Buffer 两次 
    程序试图两次释放同一个内存块！！！
    */
    String string = "liuxing";
    String second = string;
    std::cout << string << std::endl;
    std::cout << second << std::endl;
    
    /**
    深拷贝
    拷贝构造函数 -- 是一个构造函数
    当你复制第二个字符串时，他会被调用。
    当你把一个字符串赋值给一个对象时，这个对象也是一个字符串，
    当你试图创建一个新的变量并给它分配另一个变量时，
    它和你正在创建的变量有相同的类型。
    你复制这个变量，也就是所谓的拷贝构造函数。
    
    c++ 在默认情况喜爱会为你提供一个拷贝构造函数
    */
}
```



### 箭头操作符 arrow operator

```C++
{
		Entity e;
    e.GetName();

    Entity* ptr = &e;
    // 这里指针不能用上边的 点号调用类方法
    // ptr.GetName();
    
    // 逆向指针
    (*ptr).GetName();
    // 引用
    Entity& entity = *ptr;
    entity.GetName();
    
    // 箭头操作符号
    ptr->GetName();
}

class ScopedPtr
{
private:
    Entity0* m_Ptr;
public:
    ScopedPtr(Entity0* ptr)
        : m_Ptr(ptr)
    {
    }

    ~ScopedPtr()
    {
        delete m_Ptr;
    }
    // Entity0* GetPtr() { return m_Ptr; }
    // 重载箭头运算符
    Entity0* operator->()
    {
        return m_Ptr;
    }
};

int main()
{
    ScopedPtr entity = new Entity0();
    // entity.GetPtr()->GetName();
    entity->GetName();
    
    
}
```

#### 偏移量（使用箭头运算符，来获取内存中某个值的偏移量）

```C++
    // get x memory address
    // ((Vector3*)nullptr)->x; 
    int offset = (int)&((Vector3*)0)->z; // x, y, z
    std::cout << offset << std::endl;    // 0, 4, 8
```

### 动态数组 dynamic arrays

标准模板库

（模板可以处理你提供的底层数据类型）

Vector -- array list

> 标准库不是以速度为第一优先级的，所以，很多团队会有比标准库更快的实现。

我是否应该，把指向堆分配的类对象的指针，存储在我的 vector 中。或者我应该存储栈分配，一条线上（连续的内存，不是碎片）分配的 类或者结构体。-- 视情况而定！！！

存储对象比存储指针在技术上更优！

```C++
    std::vector<Vector2> vector2s;
    vector2s.push_back({ 1,3 });
    vector2s.push_back({ 5,7 });

    for (int i = 0; i < vector2s.size(); i++)
    {
        std::cout << vector2s[i] << std::endl;
    }

    // ⚠️这里的 & 可以避免将每个 vector2 复制到这个for范围循环中
    for (auto& v : vector2s)
    {
        std::cout << v << std::endl;
    }
    
    // 会将数组大小设回 0
    vector2s.clear();
    // 或者单独移除某个 vector，比如移除第2个元素
    vector2s.erase(vector2s.begin() + 1);
```

### std::vector 使用优化

> 优化的前提是了解你的环境，了解事情是如何运作的

如果 vector 的容量不够大，不能容纳你想要的新元素，vector 需要分配新的内存，至少足够容纳这些想要加入的新元素。当前的 vector 的内容，从内存中的旧位置复制到内存中的新位置，然后删除旧位置的内存。

- 避免经常重新分配大小
- 避免不必要的复制对象

```C++
struct Vector2 {
    float x, y;
    Vector2(float x, float y)
        : x(x), y(y) {}

    // copy init
    Vector2(const Vector2& other)
        : x(other.x), y(other.y)
    {
        std::cout << "copied !!" << std::endl;
    }
}    
int main()
{    
    std::vector<Vector2> vector2s;
    // 分配3个空间
    vector2s.reserve(3);
    
    // 我想在实际的vector中构造，--> 使用 emplace_back 而不是 push_back
    // 不是传递我们已经构建的 vector 对象，而只是传递了构造函数的参数列表
    vector2s.emplace_back(1,3);
    
    
    // vector2s.push_back({ 5,7 });
    vector2s.push_back({ 9,11 });

    for (int i = 0; i < vector2s.size(); i++)
    {
        std::cout << vector2s[i] << std::endl;
    }
    /*
    上述产生了6次 copy
    1 对象从main函数栈上拷贝到vector中
    优化：在适当的位置构造vector2， 在 vector 分配的内存中
    2 vector 默认分配 1（capacity） ，随着push_back 添加元素，会从内存中的旧位置复制到内存中的新位置
    优化：直接分配足够大的内存
    */
}
```

### 使用静态库（静态链接）using libraries

推荐源码构建；链接二进制更快：）

Virtual studio 可以添加另一个项目，该项目包含你的依赖库的源代码，然后将其编译为静态或动态库。

链接二进制文件，会更快更容易。但是如果项目足够重要，时间也足够，肯定会选择编译他，因为它有助于调试。甚至想稍微修改一下。

库通常包含两部分：

- Includes
  - 头文件
- Library
  - 预先构建的二进制文件
    - 动态库（比如 dll 是一种运行时动态链接库）
    - 静态库

「动or静」态库的主要区别是，库文件是否被编译到 exe 文件中或链接到 exe 文件中。

以 GLFW 库为例：（库的版本要匹配，否则编译不通过）

```
1 创建 dependency/GLFW 文件目录
2 将include文件和lib 文件放到 1 的目录下
```



```Plain
Project
Properties
  C/  C++   --> General
  Configuration    Platform
    Additional include Directories （$(SolutionDir)Dependencies\GLWF\include）
    ps. Inherit from parent or project defaults 没有勾选！！！
    
  C/  C++   --> linker --> general --> Additional library directories（静态库路径）
  (这里包含依赖库文件的根目录)  
  C/  C++   --> linker --> input --> Additional Dependencies
  
// can use <GLFW/glfw3.h>
// "" first find relative path
#include "GLFW/glfw3.h"

/**
双引号会优先检查相对路径（从相对路径查找文件），如果没找到任何相对于这个文件的东西
它就会去找编译器，检查编译器的 include 路径

如果这个源文件在 visual studio 中 ==> 用双引号
如果是一个完全的外部依赖，或者外部库，不在virtual studio中和我的solution一起编译 ==> 尖括号
*/
```

头文件提供声明，告诉我们那些函数是可用的，库文件我们提供了定义，这样我们就可以链接到那些函数，并在 C++ 中调用函数时执行正确的代码。

> ⚠️我用 mac 系统，vmware fusion 13.0.1，安装 win11 x64 版本，virtual studio 2022 个人版。按照上述操作，编译❌
>
> ```
> Build started...
> 1>------ Build started: Project: LibDemo, Configuration: Debug x64 ------
> 1>LINK : warning LNK4098: defaultlib 'MSVCRT' conflicts with use of other libs; use /NODEFAULTLIB:library
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_TranslateMessage referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_TranslateMessage
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_DispatchMessageW referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_DispatchMessageW
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_PeekMessageW referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_PeekMessageW
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_RegisterDeviceNotificationW referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_UnregisterDeviceNotification referenced in function _glfwPlatformTerminate
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_CreateWindowExW referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_CreateWindowExW
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_DestroyWindow referenced in function _glfwPlatformTerminate
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_DestroyWindow
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_ShowWindow referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_ShowWindow
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_ToUnicode referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_MapVirtualKeyW referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_MapVirtualKeyW
> 1>glfw3.lib(win32_init.obj) : error LNK2019: unresolved external symbol __imp_SystemParametersInfoW referenced in function _glfwPlatformInit
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_SystemParametersInfoW
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_CreateDCW referenced in function _glfwPlatformGetGammaRamp
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_DeleteDC referenced in function _glfwPlatformGetGammaRamp
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_GetDeviceCaps referenced in function _glfwGetMonitorContentScaleWin32
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_GetDeviceGammaRamp referenced in function _glfwPlatformGetGammaRamp
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_SetDeviceGammaRamp referenced in function _glfwPlatformSetGammaRamp
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_GetDC referenced in function _glfwGetMonitorContentScaleWin32
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_GetDC
> 1>glfw3.lib(wgl_context.obj) : error LNK2001: unresolved external symbol __imp_GetDC
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_ReleaseDC referenced in function _glfwGetMonitorContentScaleWin32
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_ReleaseDC
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_ChangeDisplaySettingsExW referenced in function _glfwPlatformGetVideoModes
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_EnumDisplaySettingsW referenced in function _glfwPlatformGetVideoMode
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_EnumDisplaySettingsExW referenced in function _glfwPlatformGetMonitorPos
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_EnumDisplayDevicesW referenced in function _glfwPollMonitorsWin32
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_GetMonitorInfoW referenced in function _glfwPlatformGetMonitorWorkarea
> 1>glfw3.lib(win32_window.obj) : error LNK2001: unresolved external symbol __imp_GetMonitorInfoW
> 1>glfw3.lib(win32_monitor.obj) : error LNK2019: unresolved external symbol __imp_EnumDisplayMonitors referenced in function _glfwPollMonitorsWin32
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_CreateBitmap referenced in function createIcon
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_CreateRectRgn referenced in function updateFramebufferTransparency
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DeleteObject referenced in function createIcon
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_CreateDIBSection referenced in function createIcon
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_TrackMouseEvent referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetMessageTime referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SendMessageW referenced in function _glfwPlatformSetWindowIcon
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_PostMessageW referenced in function _glfwPlatformPostEmptyEvent
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_WaitMessage referenced in function _glfwPlatformWaitEvents
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DefWindowProcW referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_UnregisterClassW referenced in function _glfwUnregisterWindowClassWin32
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_RegisterClassExW referenced in function _glfwRegisterWindowClassWin32
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetLayeredWindowAttributes referenced in function _glfwPlatformGetWindowOpacity
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetLayeredWindowAttributes referenced in function _glfwPlatformSetWindowOpacity
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_FlashWindow referenced in function _glfwPlatformRequestWindowAttention
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_MoveWindow referenced in function _glfwPlatformSetWindowAspectRatio
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetWindowPos referenced in function _glfwPlatformMaximizeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetWindowPlacement referenced in function createNativeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetWindowPlacement referenced in function createNativeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_IsWindowVisible referenced in function _glfwPlatformMaximizeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_IsIconic referenced in function _glfwPlatformWindowIconified
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_BringWindowToTop referenced in function _glfwPlatformCreateWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_IsZoomed referenced in function _glfwPlatformWindowMaximized
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_OpenClipboard referenced in function _glfwPlatformGetClipboardString
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_CloseClipboard referenced in function _glfwPlatformGetClipboardString
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetClipboardData referenced in function _glfwPlatformSetClipboardString
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetClipboardData referenced in function _glfwPlatformGetClipboardString
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_EmptyClipboard referenced in function _glfwPlatformSetClipboardString
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetFocus referenced in function _glfwPlatformCreateWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetActiveWindow referenced in function _glfwPlatformPollEvents
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetKeyState referenced in function _glfwPlatformPollEvents
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetCapture referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_ReleaseCapture referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_MsgWaitForMultipleObjects referenced in function _glfwPlatformWaitEventsTimeout
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetSystemMetrics referenced in function _glfwPlatformMaximizeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetForegroundWindow referenced in function _glfwPlatformCreateWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetPropW referenced in function createNativeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetPropW referenced in function _glfwPlatformPollEvents
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_RemovePropW referenced in function _glfwPlatformDestroyWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetWindowTextW referenced in function _glfwPlatformSetWindowTitle
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetClientRect referenced in function _glfwPlatformGetFramebufferSize
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetWindowRect referenced in function _glfwPlatformSetWindowAspectRatio
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_AdjustWindowRectEx referenced in function _glfwPlatformGetWindowFrameSize
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetCursorPos referenced in function _glfwPlatformPollEvents
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetCursor referenced in function _glfwPlatformSetCursor
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetCursorPos referenced in function _glfwPlatformGetCursorPos
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_ClientToScreen referenced in function _glfwPlatformGetWindowPos
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_ScreenToClient referenced in function _glfwPlatformGetCursorPos
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_WindowFromPoint referenced in function cursorInContentArea
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_ClipCursor referenced in function updateClipRect
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetRect referenced in function _glfwPlatformGetWindowFrameSize
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_OffsetRect referenced in function _glfwPlatformMaximizeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_PtInRect referenced in function cursorInContentArea
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetWindowLongW referenced in function _glfwPlatformGetWindowOpacity
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_SetWindowLongW referenced in function _glfwPlatformMaximizeWindow
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetClassLongPtrW referenced in function _glfwPlatformSetWindowIcon
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_LoadCursorW referenced in function _glfwPlatformSetCursor
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DestroyIcon referenced in function _glfwPlatformDestroyCursor
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_LoadImageW referenced in function _glfwPlatformCreateStandardCursor
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_CreateIconIndirect referenced in function createIcon
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_MonitorFromWindow referenced in function _glfwPlatformGetWindowContentScale
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_GetRawInputData referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_RegisterRawInputDevices referenced in function _glfwPlatformSetRawMouseMotion
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DragQueryFileW referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DragQueryPoint referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DragFinish referenced in function windowProc
> 1>glfw3.lib(win32_window.obj) : error LNK2019: unresolved external symbol __imp_DragAcceptFiles referenced in function createNativeWindow
> 1>glfw3.lib(win32_joystick.obj) : error LNK2019: unresolved external symbol __imp_GetRawInputDeviceInfoA referenced in function deviceCallback
> 1>glfw3.lib(win32_joystick.obj) : error LNK2019: unresolved external symbol __imp_GetRawInputDeviceList referenced in function deviceCallback
> 1>glfw3.lib(wgl_context.obj) : error LNK2019: unresolved external symbol __imp_ChoosePixelFormat referenced in function _glfwInitWGL
> 1>glfw3.lib(wgl_context.obj) : error LNK2019: unresolved external symbol __imp_DescribePixelFormat referenced in function _glfwCreateContextWGL
> 1>glfw3.lib(wgl_context.obj) : error LNK2019: unresolved external symbol __imp_SetPixelFormat referenced in function _glfwCreateContextWGL
> 1>glfw3.lib(wgl_context.obj) : error LNK2019: unresolved external symbol __imp_SwapBuffers referenced in function swapBuffersWGL
> 1>C:\Users\fmsli\dev\LibDemo\bin\x64\Debug\LibDemo.exe : fatal error LNK1120: 97 unresolved externals
> 1>Done building project "LibDemo.vcxproj" -- FAILED.
> ========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========
> ========== Build started at 4:42 PM and took 01.420 seconds ==========
> 
> ```
>
> 用window 电脑是可以的。应该是虚拟机的问题。
>
> 删了 重新搞了一遍又可以了。。。奇怪



### 使用动态库 using dynamic libraries

动态链接

发生在运行时

一类是，“静态的” 动态库版本，应用程序现场需要这个动态链接库，已经知道里边有什么函数，可以使用什么。

另一类是，任意加载这个动态库，甚至不知道里面有什么，但想取出一些东西，或者我想用它做很多事情。

还是以 GLFW 为例

```undefined
glfw3.dll
glfw3dll.lib 
// glfw3dll.lib  是一堆指向 dll 文件的指针。这样我们就不用在运行时去检索所有东西的位置

// 同时编译这两个文件是非常重要的，他们是直接相关的
//（否则可能会得到不匹配的函数和错误类型的内存地址，函数指针不会正常工作）
C/  C++   --> linker --> input --> Additional Dependencies（glfw3dll.lib 动态库文件名）
```

运行会遇到错误

```
❌由于找不到 glfw3.dll，无法继续执行代码。重新安装程序可能会解决此问题
```



```Plain
Dependencies\GLFW\lib-vc2022\glfw3.dll : fatal error LNK1107: invalid or corrupt file: cannot read at 0x2F8

// 简单的做法是：把 dll 库和 exe 放在同级目录（自动搜索路径）
// 确保在一个可访问的地方有 dll 文件，可以在整个应用程序中，设置库搜索位置
```

> 为什么我可以不需要 declspec dllimport？
>
> 
>
> 
>
> ---
>
> 
>
> [总结一下__declspec(dllimport)的作用](https://blog.csdn.net/clever101/article/details/5421782) 作者：朱金灿
>
> 是时候总结一下`__declspec(dllimport)`的作用了。可能有人会问：`__declspec(dllimport)`和`__declspec(dllexport)`是一对的，在动态链接库中`__declspec(dllexport)`管导出，`__declspec(dllimport)`管导出，就像一个国家一样，有出口也有进口，有什么难理解的呢？这是一种很自然的思路，开始我也是这样理解。
>
> 但是在两年前的一个项目中，我发现不用`__declspec(dllimport)`似乎也可以。比如现在我新建一个使用共享MFC DLL的规则DLL工程：DllDlg。然后我新建两个文件：DllApi.h和DllApi.cpp。DllApi.h作为接口文 件，DllApi.cpp作为实现文件。
>
> 接着在DllApi.h声明一个函数：
>
> ```cpp
> __declspec(dllexport) void HelloWorld();  
> ```
>
> 在DllApi.cpp写这个函数的实现：
>
> ```cpp
> void HelloWorld()  
> {  
>     AfxMessageBox(_T("HelloWorld"));  
> }  
> ```
>
> 这样外部的应用程序或dll就能调用HelloWorld函数。这里要特别提醒的是：有些网友说要把DllApi.h中的`__declspec(dllexport) void HelloWorld()`;改为`__declspec(dllimport) void HelloWorld();`才能提供给外部调用，实际上这并不需要，这个我已经测试过。从那时我就产生一个疑问：照这样，像类似下面的：
>
> ```cpp
> #ifdef _EXPORTING  
> #define API_DECLSPEC    __declspec(dllexport)  
> #else  
> #define API_DECLSPEC    __declspec(dllimport)  
> #endif 
> ```
>
> 是不是就只剩下一种作用：让外部调用者看得更自然些，知道哪些接口是自己工程需要导入的？`__declspec(dllimport)`是不是一点实际作用都没有呢？这个疑问一直盘旋在我的脑海。直到最近，我在CSDN论坛上发了一个帖子：
>  [`__declspec(dllimport) `的作用到底在哪里呢？](http://topic.csdn.net/u/20100322/00/17389242-a3f7-46d1-992b-ae4c4e2976bb.html)
>
> 总结了各位大虾的发言，特得出如下结论：
>
> 1. 在导入动态链接库中的全局变量方面起作用：
>     使用类似
>
> ```cpp
> #ifdef _EXPORTING  
> #define API_DECLSPEC    __declspec(dllexport)  
> #else  
> #define API_DECLSPEC    __declspec(dllimport)  
> #endif 
> ```
>
> 可以更好地导出dll中的全局变量，比如按照的宏，可以在dll中这样导出全局变量：
>
> ```undefined
> API_DECLSPEC CBtt g_Btt;  
> ```
>
> 然后在调用程序这样导入：
>
> ```undefined
> API_DECLSPEC CBtt g_Btt;  
> ```
>
> 当然也可以使用extern关键字，比如在dll中这样导出全局变量：
>
> ```undefined
> CBtt g_Btt;  
> ```
>
> 然后在调用程序这样导入：
>
> ```cpp
> extern CBtt g_Btt;  
> ```
>
> 但据说使用`__declspec(dllimport)`更有效。
>
> 1. __declspec(dllimport)的作用主要体现在导出类的静态成员方面，
>     比如在动态链接库中定义这样一个导出类：
>
> ```cpp
> class __declspec(dllexport) CBtt  
> {  
> public:  
>     CBtt(void);  
>     ~CBtt(void);  
> public:  
>     CString m_str;  
>     static int GetValue()  
>     {  
>         return m_nValue;  
>     }  
> private:  
>     static int m_nValue;  
> }; 
> ```
>
> 照上面这样声明，外部虽然可以使用CBtt类，但不能使用CBtt类的GetValue函数，一使用就会出现无法解析的外部符号 "`public: static int CBtt::m_nValue`" `(?m_nValue@CBtt@@2HA)`。只有如下声明才能使用CBtt类的GetValue函数：
>
> ```cpp
> #ifdef _EXPORTING  
> #define API_DECLSPEC    __declspec(dllexport)  
> #else  
> #define API_DECLSPEC    __declspec(dllimport)  
> #endif  
> class API_DECLSPEC CBtt  
> {  
> public:  
>     CBtt(void);  
>     ~CBtt(void);  
> public:  
>     CString m_str;  
>     static int GetValue()  
>     {  
>         return m_nValue;  
>     }  
> private:  
>     static int m_nValue;  
> };  
> ```
>
> 1. 使用隐式使用dll时，不加`__declspec(dllimport)`完全可以，使用上没什么区别，只是在生成的二进制代码上稍微有点效率损失。
>
> a、 不加`__declspec(dllimport)`时，在使用dll中的函数时，编译器并不能区别这是个普通函数，还是从其它dll里导入的函数
> b、有 `__declspec(dllimport)`时，编译器知道这是要从外部dll导入的函数，从而在生成的exe的输入表里留有该项，以便在运行 exe，PE载入器加载exe时对输入地址表IAT进行填写
>
> 参考文献：
>
> 1. [__declspec(dllimport) 到底有什么用？](http://blog.csdn.net/Repeaterbin/archive/2009/06/15/4269666.aspx)





### 创建与使用库 making and working with libraries

```undefined
new project
c++ empty project --> Game

project 右键 add new project --> Engine

All Configurations/ All Platforms
Game properities  General Configuration Type --> Application
Enigine properities  General Configuration Type --> Static Library
// 相对路径引用
#include "../../Engine/src/Engine.h"

// 绝对路径，特别是使用编译器的包含路径
/*
Game properties C/C++ General Additional include Directories
*/
#include "Engine.h"

/*
// #include "Engine.h"
namespace engine
{
        PrintMessage();
}
*/

int main()
{
        engine::PrintMessage();
        return 0;
}
Virtual studio (vs) 中
点击 Game 点击 add 然后是 reference
我们可以在项目的解决方案中，选择 Engine
现在会把 Engine.lib 链接到我们的可执行文件中，就像我们已经把它添加到连接器输入一样。
```



### 如何处理多返回值 Deal with multiple return values

#### 输入参数

```C++
// 输入参数（引用类型）
static void int ParseShader(const std::string& vertexSource, const std::string& fragmentSource)
{
    // ...
}

// 输入参数（指针类型）
static void int ParseShader(const std::string* outVertexSource, const std::string* outFragmentSource)
{
    // ...
    // null 判断
    if (outVertexSource)
    {
        // ..
        *outVertexSource = xxx;
    }
    if (outFragmentSource)
    {
        *outFragmentSource = xxx;
    }
}

void func
{
    // ...
    // 声明两个字符串，然后像这样传递进来。
    std::stirng vs, fs;
    // 引用
    ParseShader(vs, fs);
    
    // 指针
    ParseShader(&vs, &fs);
}
```

#### 返回一个数组

```C++
static std::string* ParseShader()
{
    // ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();
        
    // 新分配内存
    return new std::string[] {vs, fs};
}


void func
{
    // ...
    std::string* sources = ParseShader();
    
}

// -------

#include <vector>
// 存储在堆上
static std::vector<std::string> ParseShader()
{
    // ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();
    
    std::vector<std::string> results;
    results[0] = vs;
    results[1] = fs;
    return results;
}


void func
{
    // ...
    std::vector sources = ParseShader();
    
}


#include <array>
// 存储在栈上
static std::array<std::string, 2> ParseShader()
{
    // ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();
    
    std::array<std::string, 2> results;
    results[0] = vs;
    results[1] = fs;
    return results;
}


void func
{
    // ...
    std::array sources = ParseShader();
    
}
```

Tuple 元组

```C++
//#include <utility>
#include <functional>

static std::tuple<std::string, std::string> ParseShader()
{
    // ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();
    
    
    return std::make_pair(vs,fs);
}


void func
{
    // ...
    auto sources = ParseShader();
    std::string vs = std::get<0>(sources);
    std::string fs = std::get<1>(sources);
    
}
```

Pair

```C++
#include <functional>

static std::pair<std::string, std::string> ParseShader()
{
    // ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();
    
    
    return std::make_pair(vs,fs);
}


void func
{
    // ...
    auto sources = ParseShader();
    std::string vs = sources.first;
    std::string fs = sources.second;
    
}
```

#### 返回一个结构体

阅读性更好！

```C++
struct ShaderProgramSource
{
    std::string VertexSource;
    std::string FragmentSource;    
}

static ShaderProgramSource ParseShader()
{
    // ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();
    
    
    return {vs,fs};
}


void func
{
    // ...
    auto sources = ParseShader();
    std::string vs = sources.VertexSource;
    std::string fs = sources.FragmentSource;
    
}
```

### 模板 template

编译器为你写代码！！！基于你给他的规则，基于函数或类的使用，或类似的东西

```C++
/**
这不是一个真的函数
只有当我们实际调用它的时候，这些函数才被真的创建
当我们调用这个函数的时候，基于传递的参数，这个函数才被创建出来
并作为源代码被编译。
*/

template<typename T>
void printMsg(T msg)
{
    std::cout << msg << std::endl;
}
template<typename T, int N>
class Array
{
private:
    T m_Array[N];
public:
    int GetSize() const { return N; }    
};

int main()
{
    Array<int, 5> array;
    std::cout << array.GetSize() << std::endl;

}
```

日志系统，可以使用模板。当模板搞得越来越复杂，可能没人知道干什么。。。

### 栈和堆 stack vs heap

```C++
#include <iostream>

struct Vector3
{
    float x, y, z;
    
    Vector3()
        : x(0), y(0), z(0)
    {
    
    }
}

int main()
{
    // 栈，一行 cpu 指令，分配 快
    // 作用域结束，所有在站内分配的东西，都会被弹出，被释放，被回收
    // 不需要将栈指针反向移动然后返回栈指针地址，
    // 只需要将栈指针，指向开始
    int value = 5;
    int array[5];
    Vector3 vector;
    
    
    // 堆，分配 慢
    // new，malloc函数（memory allocate）
    // 这样做通常会调用底层操作系统或平台的特定函数
    // 这将在堆上为你分配内存（得到一定数量的物理 ram）
    // 程序维护一个叫做 free list 空闲列表的东西
    // 跟踪哪些内存块是空闲的
    int* hvalue = new int;
    *hvalue = 5;
    
    int* harray = new int[5];
    
    Vector3* hvector = new Vector();
    
    delete hvalue;
    delete[] harray;
    delete hvector;
}
```

### 宏 macros

预处理器

文本替换

```C++
#if PR_DEBUG == 1
#define LOG(x) std::cout << x << std::endl
#elif defined(PR_RELEASE)
#define LOG(x)
#endif

// 多行的宏
#define MAIN int main() \
{\
    LOG("HELLO");\
    std::cin.get();\
}

MAIN
project -> properties -> C/C++ -> Preprocessor Definitions
```

### Auto 关键字

C++ 自己推导类型

- 有了 auto 还需要写类型吗？当然！！！
- 可否到处使用 auto？可读性变差！！！

编程风格

硬币的两面：

客户端不需要做任何的改动，我甚至不知道 API 已经改变了。但是因为我不知道 API 改变了，它可能会破坏依赖于特定类型的代码！

```C++
#include <vector>

int main()
{
    std::vector<std::string> strings;
    strings.push_back("Apple");
    strings.push_back("Orange");
    
    // for (std::vector<std::string>::interator it = strings.begin(); it != strings.end(); it++)
    // 使用 auto，类型长度变短了
    for (auto it = strings.begin(); it != strings.end(); it++)    
    {
        std::cout << *it <<std::endl;
    }    

}
#include <unordered_map>

class Device {};

class DeviceManager
{
private:
    std::unordered_map<std::string, std::vector<Device*>> m_Devices;
public:
    const std::unordered_map<std::string, std::vector<Device*>>& GetDevices() const
    {
        return m_Devices;
    }        
};

int main()
{
    DeviceManager dm;
    const std::unordered_map<std::string, std::vector<Device*>>& devices = dm.GetDevices();
}

int main()
{
    using DeviceMap = const std::unordered_map<std::string, std::vector<Device*>>;
    DeviceManager dm;
    DeviceMap& devices = dm.GetDevices();
}

int main()
{
    typedef std::unordered_map<std::string, std::vector<Device*>> DeviceMap;
    DeviceManager dm;
    const DeviceMap& devices = dm.GetDevices();
}

int main()
{
    DeviceManager dm;
    const auto& devices = dm.GetDevices();
}
```

### 静态数组 static arrays

> whose lifetime is tied to the scope in which it is declared. It is created on the stack, and its elements are initialized when the program starts.

```C++
#include <array>

int main()
{
    std::array<int, 5> data;
    // size 模板的参数
    // template<class _Ty, size_t _Size> class array {}
    data.size();
    data[0] = 100;
    
    // std::array 有边界检查（可选）_ITERATOR_DEBUG_LEVEL
    
    
    int data1[5];
    data1[0] = 200;
}
```

### 函数指针 function pointers

讲一个函数赋值给一个变量的方法。

对 函数指针，使用 auto typedef 或者 using

```C++
void HelloWorld()
{
    std::cout << "hello world" << std::endl;
}

int main()
{
    void(*f1)() = HelloWorld;
    
    f1();
    
    
    auto f2 = HelloWorld;
    
    
    typedef void(*HelloWorldFunction)();
    //typedef void(*f)() HelloWorldFunction;
    HelloWorldFunction f3 = HelloWorld;
    f3();
    
}
#include <iostream>

void HelloWorld(int a)
{
    std::cout << "hello world value = " << a << std::endl;
}

int main()
{
    typedef void(*HelloWorldFunction)(int);
    //typedef void(*f)() HelloWorldFunction;
    HelloWorldFunction f3 = HelloWorld;
    f3(100);
    
}
#include <vector>

void PrintValue(int value)
{
    std::cout << "value = " << value << std::endl;    
}

void ForEach(const std::vector<int>& values, void(*func)(int))
{
    for (int value : values)
        func(value);
}

int main()
{
    std::vector<int> values = {1,5,4,2,3};
    ForEach(values, PrintValue);
}
#include <vector>

void ForEach(const std::vector<int>& values, void(*func)(int))
{
    for (int value : values)
        func(value);
}

int main()
{
    std::vector<int> values = {1,5,4,2,3};
    // lambda
    // [] 捕获方式 (参数列表)
    ForEach(values, [](int value){ std::cout << "value = " << value << std::endl; });
}
```



### lambda

只要你有一个函数指针，你都可以使用 lambda。我们会设置函数指针指向函数的任何地方，我们都可以将它设置为 lambda。

```C++
#include <vector>

void ForEach(const std::vector<int>& values, void(*func)(int))
{
    for (int value : values)
        func(value);
}

int main()
{
    std::vector<int> values = {1,5,4,2,3};
    // lambda
    // [] 捕获方式 (参数列表)
    // [a, &b] / [this] / [&] / [=] / []
    ForEach(values, [](int value){ std::cout << "value = " << value << std::endl; });
}
#include <vector>
#include <functional>

void ForEach(const std::vector<int>& values, const std::function<void(int)>& func)
{
    for (int value : values)
        func(value);
}

int main()
{
    std::vector<int> values = {1,5,4,2,3};
    // lambda
    // [] 捕获方式 (参数列表)
    // [a, &b] / [this] / [&] / [=] / []
    ForEach(values, [=](int value){ std::cout << "value = " << value << std::endl; });
}
```



