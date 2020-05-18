---
title: OC 中的方法“重写”小结（一）
date: 2015-04-29 18:01:51
categories:
- OC
tags:
- OC
---
## 写在前面的话

在学习 OC 的过程中，接触到了重写某些方法的知识点，而且了解到在实际的开发中，为了方便，经常会自定义某些方法，也就是说会重写某些方法。比如，在 OC 的**继承**中，**子类**可以**重写**其**父类**的方法，还有我们可以重写**构造方法**等。所以，我总结了一下学习过程中重写方法的知识点汇总成一篇文章，希望对读者有所帮助。
<!--more-->

##  子类重写父类的方法

就从子类重写父类的方法开始写起吧，这又不得不提一下面向对象的另一特性——**继承**（这里是**单继承**）。当两个类拥有相同属性和方法的时候，就可以将相同的东西抽取到一个父类中，当 A 类完全拥有 B 类中的部分属性和方法时，可以考虑让 B 类继承 A 类（有时候并不是符合语法的东西就可以大用特用，要考虑逻辑，这时候就要考虑继承与组合了，这里不是重点掠过）。子类会继承父类的所有实例变量和方法，但是父类中的某些方法远不能达到自身的需求的时候，子类有权利重写父类的方法。

编程这东西如果只是讲来讲去，总觉得空口无凭啊，上代码：

    #import <Foundation/Foundation.h>
    // 人这个类的声明
    @interface Person : NSObject
    
    @property int age;
    
    - (void)run;
    @end
    // 人这个类的实现
    @implementation Person
    
    - (void)run
    {
        NSLog(@"run run run");
    }
    @end
    // 学生类的声明
    @interface Student : Person
    
    @property int num;
    
    - (void)run;
    @end
    
    @implementation Student
    // 子类里面可以拥有与父类一样的方法，子类重写（重新实现）父类的方法
    // 另外，完全可以在父类声明方法，在子类实现
    - (void)run
    {
        NSLog(@"student run run run");
    }
    @end
    int main()
    {
        Person *p = [[Person alloc] init];
    
        Student *s = [[Student alloc] init];
    
        [s run];        
    
        return 0;
    }


从代码我们可以看到，学生类继承了人这个类（学生当然是人），然后学生类中重写了父类的 run 方法。这与对象访问方法的机制有关系，因为对象调用方法是先从自己类的方法列表中找有没有 run 这个方法，如果有就直接调用了，就不再继续往下找了。毕竟，想想这也是满符合人的思维的嘛！

如果，这个学生不仅要调用自己的 run 方法还要调用父类的 run 方法，那就要用到 **super** 关键字了。子类重写父类的方法时，想保留父类的方法即子类既有自己的实现，又有从父类继承下来的实现，相当于对父类的方法做了扩展。其中有一点大家要搞明白，就是 super 如果处在对象方法中调用的是对象方法，如果处在类方法中调用的是父类的类方法。

## 重写构造方法

### 重写初始化方法

构造方法用来初始化对象的方法，很明显它是一个对象方法。我们之所以要重写构造方法，是为了使创建出来的对象，其成员变量一开始就有我们希望的赋值。本着有简单到复杂的原则，先来看一下构造函数的基本使用：


    #import <Foundation/Foundation.h>
    
    @interface Person : NSObject
    
    @property int age;
    
    @property int heihgt;
    
    @property NSString *name; 
    
    - (void)print;
    
    @end
    
    @implementation Person
    // 重写init 方法
    - (id)init
    {
        // 一定要调用回super的init方法，
        // 初始化父类中声明的一些成员变量和其他属性
        // if条件判断只有初始化成功,才有必要进行接下来的初始化
        if (self = [super init])
        {
            _age = 10;
        }
    
        // 返回一个初始化完毕的对象
        return self;
    }
    
    - (void)print
    {
        NSLog(@"Age is %d", _age);
    }
    
    @end
    
    @interface Student : Person
    
    @property int num;
    
    - (void)print;
    
    @end
    
    @implementation Student
    // 重写init 方法
    - (id)init
    {
        if (self = [super init])
        {
            _num = 110;
        }
        return self;
    }
    - (void)print
    {
        NSLog(@"Student number is %d", _num);
        [super print];
    }
    @end
    
    int main()
    {
        
        Person *p = [[Person alloc] init];
    
        // 此时p对象创建出来初始化的age=10
        [p print];
    
        Student *s = [[Student alloc] init];
    
        // 每个s对象创建出来初始化的num=110,age=10
        [s print];
        
        return 0;
    }

先来解释一下，开发中一般不用 *new* 方法创建对象，因为它写死了分配空间（ alloc ）和初始化（ init ）这两个步骤，开发中我们可能要用其他的方法初始化，就像上面我们一开始给成员变量赋了值。其次，代码中的注释也提到了，一定要调用回父类（ super ）的 初始化方法（ init ），初始化父类中声明的一些成员变量和其他属性。if 判断中的条件意思是只有初始化成功才有必要进行接下来的初始化操作。而且我们的初始化方法最终返回一个初始化完毕的对象。

### 自定义构造方法

    #import <Foundation/Foundation.h>
    @interface Person : NSObject
    
    @property int age;
    @property int height;
    @property NSString *name;
    
    -(id)initWithAge:(int)age andHeight:(int)height andName:(NSString *)name;
    
    @end
    
    @implementation Person
    
    // 自定义构造方法
    -(id)initWithAge:(int)age andHeight:(int)height andName:(NSString *)name
    {
        if (self = [super init])
        {
            _age = age;
            _height = height;
            _name = name;
        }
    
        return self;
    }
    
    @end
    @interface Student : Person
    
    @property int num;
    
    -(id)initWithAge:(int)age andHeight:(int)height andName:(NSString *)name andNum:(int)num;
    - (void)out;
    
    @end
    @implementation Student
    
    -(id)initWithAge:(int)age andHeight:(int)height andName:(NSString *)name andNum:(int)num
    {
        // 将age/height/name传递到父类方法中去初始化
        
        if (self = [super initWithAge:age andHeight:height andName:name])
        {
            _num = num;
        }   
    
        return self;
    
    }
    
    - (void)out
    {
        NSLog(@"The Student's age is %d, height is %d, name is %@, number is %d", _age, _height, _name, _num);
    }
    @end
    
    int main()
    {
        Student *s = [[Student alloc] initWithAge:20 andHeight:166 andName:@"jack" andNum:120];
        
        [s out];
    
        return 0;
    }

这里我要解释几个点：

- 自定义构造方法一定是对象方法，一定以短横线（ - ）开头
- 返回值一般是id类型，毕竟你不想一下子写死它吧！
- 方法名以 init 开头，这是规范的写法，要不然写的乱七八糟，会被骂的吆！
- 尽量做到父类的成员变量在父类初始化，子类的在子类

## 写在最后的话
总结全文：首先是提到了，继承中子类重写父类的方法，还要注意其中的关键字 super ；另外总结了构造方法的初始化以及自定义构造函数，最后，希望我的博文可以给看到文章最后的您带来些许的帮助，谢谢！在接下来的博文中，我将进一步总结如何重写 **dealloc** 方法和 **description** 方法，敬请期待！

## 参考文献

- [面向对象程序设计](http://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1)
- [07-自定义构造方法和description方法](http://www.cnblogs.com/mjios/archive/2013/04/19/3031412.html#label1)
- [Understanding Object Oriented Programming](http://www.csis.pace.edu/~bergin/patterns/ppoop.html)
