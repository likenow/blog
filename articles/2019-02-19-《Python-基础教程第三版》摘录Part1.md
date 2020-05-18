---
title: 《Python 基础教程第三版》摘录Part1
date: 2019-02-19 21:20:53
tags:
- Python
categories:
- Python
---

## 前言

前些日子读了**[《Unix编程艺术》](https://dnduuhn.com/2019/01/15/%E3%80%8AUnix%E7%BC%96%E7%A8%8B%E8%89%BA%E6%9C%AF%E3%80%8B%E6%91%98%E5%BD%95Part1/)**然后就坚定了学习Python的信念。今天早上车还看了一片[2018 Python官方年度报告](https://mp.weixin.qq.com/s/wmUPE3mzmMWTnTpzbJgrVw)。希望可以尽快把这门语言熟练起来（flag）。

<!--more-->

## 1. 基础知识

### 1.4 变量。变量没有默认值

### 1.5 语句。语句和表达式

### 1.6 获取用户输入。

### 1.7 函数。

​	内置函数

​	函数调用

### 1.8 模块。

#### 1.8.1 cmath 和 复数

负数的平方根为虚数，而由实部和虚部组成的数为复数。Python 没有专门表示虚数的类型，而将虚数视为实部为0的复数。

#### 1.8.2 `__future__`

### 1.9 保存并执行程序

海龟绘图法

#### 1.9.1 从命令提示符运行Python脚本

#### 1.9.2 让Python脚本像普通程序一样

```python
# !/usr/bin/env python
```

UNIX 提供了一种标准方式：让脚本的第一行以字符序列`#!`开始，并在他后面指定用于对脚本进行解释的程序的绝对路径。

### 1.10 字符串

字符串就是由字符组成的序列。

#### 1.10.1 单引号字符串以及对引号转义

#### 1.10.2 拼接字符串

#### 1.10.3 字符串表示 str 和 repr

str 将值转化为用户能够看懂的字符串

repr 通常会获得值的合法Python 表达式表示

#### 1.10.4 长字符串、原始字符串和字节

- 长字符串
  - 使用三引号
- 原始字符串
  - 前缀`r`
- 字节
  - Unicode 码点是使用整数定义的。
  - ASCII字符是使用对应的数及二进制编码定义的。
  - 变长编码
  - UTF-8（Unicode Transformation Format 8 bits）
  - encode
  - decode
  - bytes
  - str
  - 指定编码 `# -*- coding: encoding name -*-`
  - bytearray

### 1.11 本章函数

| 函数                              | 描述                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| abs(number)                       | 返回指定数的绝对值                                           |
| bytes(string, encoding[, errors]) | 对指定的字符串进行编码，并以指定的方式处理错误               |
| cmath.sqrt(number)                | 返回平方根，可用于负数                                       |
| float(object)                     | 将字符串或数字转换为浮点数                                   |
| help([object])                    | 提供交互是帮助                                               |
| input(prompt)                     | 以字符串的方式获取用户输入                                   |
| int(object)                       | 将字符串或数转换为整数                                       |
| math.ceil(number)                 | 以浮点数的方式返回向上圆整的结果                             |
| math.floor(number)                | 以浮点数的方式返回向下圆整的结果                             |
| math.sqrt(number)                 | 返回平方根，不能用于负数                                     |
| pow(x, y[, z])                    | 返回x的y次方，对z求模的结果                                  |
| print(object, ...)                | 将提供的实参打印出来，并用空格分隔                           |
| repr(number)                      | 返回指定值的字符串表示                                       |
| round(number[, ndigits])          | 四舍五入为指定的精度，正好为5时舍入到偶数                    |
| str(object)                       | 将指定的值转化为字符串。用于转换bytes时，可指定编码和错误处理方式 |

## 2 列表和元组

数据结构是以某种方式组合起来的数据元素集合。

### 2.1 序列 sequence

列表，可修改

元组，不可修改

### 2.2 通用的序列操作

#### 2.2.1 索引

访问单个元素

#### 2.2.2 切片

访问特定方位内的元素

`numbers[x:y:z]` ，xyz可为正数或负数；第一个索引x是包含的第一
个元素的编号，但第二个索引y是切片后余下的第一个元素的编号；第三个索引z是步长；

#### 2.2.3 序列相加

一般而言，不能拼接不同类型的序列。

#### 2.2.4 乘法

```python
[0]*10 => [0,0,0,0,0,0,0,0,0,0]
[None]*10 => [None,None,None,None，None,None,None,None,None,None]
```

#### 2.2.5 成员资格

`in` 指定的字符（or子字符串）是否包含在字符串中

- 满足时返回 true 
- 不满足时返回  false

`len`

 `min`

 `max`

### 2.3 列表

#### 2.3.1 函数 list

可将任何序列作为list的参数

##### 修改列表：给元素赋值

##### 删除元素

`del`语句

##### 给切片赋值

#### 2.3.3 列表方法

##### append，将一个对象附加到列表末尾

##### clear，清空列表内容

##### copy，常规复制只是将另一个名称关联到列表

##### count，计算指定元素在列表中出现的次数

##### extend，同时将多个值附加到列表末尾

##### index，查找指定值第一次出现的索引

##### insert，用于将一个对象插入列表 ps.可以使用切片赋值来达到同样的效果

##### pop，从列表中删除一个元素，并返回这个元素

##### remove，用于删除第一个为指定值的元素。修改列表，无法返回值

##### reverse，反转列表元素。修改列表，无法返回值

##### sort，列表排序。修改列表，无法返回值。

​	另一种方式是使用 函数 sorted

##### 高级排序

sort 接受两个可选参数：key和reverse





### 2.4 元组：不可修改的序列

`tuple` 将一个序列作为参数，并将其转化为元组。

```
tuple([1,2,3]) => (1,2,3)
1,2,3 => (1,2,3)
```

 

| 函数          | 描述                                           |
| ------------- | ---------------------------------------------- |
| len(seq)      | 返回序列的长度                                 |
| list(seq)     | 将序列转换为列表                               |
| max(args)     | 返回序列或一组参数中的最大值                   |
| min(args)     | 返回序列或一组参数中的最小值                   |
| reversed(seq) | 让你能够反向迭代序列                           |
| sorted(seq)   | 返回一个有序列表，其中包含指定序列中的所有元素 |
| tuple(seq)    | 将序列转化为元组                               |

## 3 使用字符串

### 3.1 字符串是不可变的

### 3.2 设置字符串的格式

```python
# %s 是转换说明符
format = "Hello, %S, it's %s"
values = ('w', 'x')
format % values
# ------
from math import pi
"{name} is approximately {value}.".format(value=pi, name="pai")
# ------
from math import e
f"Euler's constant is roughly {e}" => "Euler's constant is roughly {e}".format(e=e)


```



### 3.4 字符串方法

#### 3.4.1 center

通过在字符串两边添加填充字符，让字符串居中

#### 3.4.2 find

查找子串，如果找到就返回子串的第一个字符的索引（**非布尔值**），否则返回 -1

#### 3.4.3 join

用于合并序列的元素（所合并的元素都必须是字符串）

#### 3.4.4 lower

方法 `lower` 返回字符串的小写版本

`title` 词首大写

String 中的函数`capwords`，使用split根据sep拆分s，将每项的首字母大写，再以空格为分隔符，将他们合并起来。

`ascii(obj)` 创建指定对象的 `ascii` 表示



#### 3.4.5 replace

#### 3.4.6 split

字符串拆分为序列

#### 3.4.7 strip

将字符串开头和末尾的空白删除，并返回删除后的结果

#### 3.4.8 translate

单字符替换，能够同时替换多个字符，因此效率比replace 高。

转换表

`maketrans`

```python
# 可选的第三个参数指定，要将那些字母删除
table = str.maketrans('cs','kz','')
```

#### 3.4.9 判断字符串是否满足特定的条件

很多字符串方法都以`is`打头，比如 `isspace` `isdigit` `isupper`



## 4 当索引行不通时

**映射**，字典是 Python 中唯一的内置映射类型，其中的值不按顺序排列，而是存储在键下。

### 4.1 字典的用途

### 4.2 创建和使用字典

#### 4.2.1 函数 dict

从键值对，映射或关键字参数创建字典

```python
items = [('name', 'Gumby'), ('age', 27)]
d = dict(items)
# {'age': 42, 'name': 'Gumby'}
```

还可通过**关键字实参**来调用这个函数。

#### 4.2.2 基本的字典操作

- len(d) 返回字典d包含的项数
- d[k] 返回与键k相关联的值
- d[k] = v 将值v关联到键k
- del d[k] 删除键为k的项
- k in d检查字典d是否包含键为k的项



- 键的类型，字典中的键可以是任何不可变的类型
- 自动添加，即便字典中没有的键，也可以给他赋值，这将在字典中创建一个新项
- 成员资格，k in d 查找的是键而不是值；v in l（l 列表）查找的是值而不是索引。

#### 4.2.3 将字符串格式设置功能用于字典

```python
phonebook = {'Beth': '9102', 'Alice': '2341', 'Cecil': '3258'}
"Cecil's phone number is {Cecil}.".format_map(phonebook) 
# "Cecil's phone number is 3258."
```

#### 4.2.4 字典方法

1. clear，删除所有的字典项
2. copy，浅复制，值本身是原件而非副本
   - 浅复制，如果修改副本的值，原件也将发生变化，因为原件指向的也是被修改的值
   - 深复制，`deepcopy`
3. fromkeys
   - 创建一个新字典，其中包含指定的键，且每个键对应的值都是none
   - `dict.fromkeys(['name', 'age'])
     {'age': None, 'name': None}`
4. get，使用get来访问不存在的键时，没有引发异常，而是返回 None，可以指定默认值：
   - `d.get('name', 'N/A')`
5. items，返回一个包含所有字典项的列表，其中每个元素都为`(key,value)` 的形式，字典项在列表中的顺序不确定
   - 字典视图
6. keys，返回一个字典视图，其中包含指定字典中的键
7. pop，获取与指定键相关联的值，并将该键值对从字典中删除
8. popitem，随机的弹出一个字典项
9. setdefault，在字典不包含指定的键时，在字典中添加指定的键值对。

```python
d = {}
d.setdefault('name', 'N/A')
# 'N/A'
```

10. update，使用一个字典中的项来更新另一个字典

```python
d = {
'title': 'Python Web Site',
'url': 'http://www.python.org', 
'changed': 'Mar 14 22:09:15 MET 2016'
}
x = {'title': 'Python Language Website'}
d.update(x)
d
{'url': 'http://www.python.org', 'changed':
'Mar 14 22:09:15 MET 2016', 'title': 'Python Language Website'}
```

对于，通过参数提供的字典，将其项添加到当前字典中。如果当前字典包含键相同的项，就替换它。

11. values，返回一个由字典中的值组成的字典视图。可能包含重复的值。

## 5. 条件循环及其他语句

#### 5.1.1 打印多个参数

```python
# 自定义分隔符
print("I", "wish", "to", "register", "a", "complaint", sep="_")
I_wish_to_register_a_complaint
```

#### 5.1.2 导入时重命名

从模块导入时，通常使用:

```python
import somemodule
# or
from somemodule import somefunction
# or
from somemoodule import somefunction, anotherfunction, yetanotherfunction
# or
from somemodule import *
```



```python
# 导入整个模块并给他指定别名
import math as foobar
foobar.sqrt(4)
# 2
```



```python
# 导入特定函数并指定别名
from math import sqrt as foobar
foobar(4)
# 2
```



### 5.2 赋值

#### 5.2.1 序列解包(或可迭代对象解包)

```python
x, y, z = 1, 2, 3
print(x, y, z)
# 1 2 3
x, y = y, x
print(x, y, z)
# 2 1 3
```



```python
a, b, *rest = [1, 2, 3, 4]
rest
# [3, 4]
```

#### 5.2.2 链式赋值

用于将多个变量关联到同一个值

```python
x = y = somefunc()
# 等价于
y = somefunc()
x = y
```

#### 5.2.3 增强赋值

```python
x = 2
x += 1
x *= 2
x
# 6
```

```python
fnord = 'foo'
fnord += 'bar'
fnord *= 2
fnord 
# 'foobarfoobar'
```



### 5.3 代码块：缩进

代码块是一组语句，可在满足条件时执行，可多次执行，等等

代码块是通过缩进代码来创建的，

```pseudocode
this is a line
this is another line:
this is another block continuing the same block the last line of this block
phew, there we escaped the inner block
```



### 5.4 条件和条件语句

George Boole 在真值方面做出巨大贡献

用作布尔表达式时，

```python
False
None
0
""
()
[]
{}
```

都将被解释器视为假

#### 5.4.2 有条件的执行和 if 语句

#### 5.4.3 else 子句

```python
name = input('What is your name?') 
if name.endswith('Gumby'):
    print('Hello, Mr. Gumby') 
else:
    print('Hello, stranger')
```

#### 5.4.4 elif 子句

```python
num = int(input('Enter a number: ')) 
if num > 0:
    print('The number is positive') 
elif num < 0:
    print('The number is negative') 
else:
    print('The number is zero')
```

#### 5.4.5 代码块嵌套

#### 5.4.6 更复杂的条件

##### 5.4.6.1 比较运算符

| 表达式      | 描述               |
| ----------- | ------------------ |
| x is y      | x y 是同一个对象   |
| x is not  y | x y 是不同的对象   |
| x in y      | x是容器 y 的成员   |
| x not in  y | x不是容器 y 的成员 |

与 赋值一样，Python 支持链式比较 `0 < age < 100`

- 相等运算符，用两个（`==`表示）
- 相同运算符，`is`
- `in` 成员资格运算符
- 字符串和序列的比较
  - 字符串是根据字符的字母排列顺序(码点)进行比较的

##### 5.4.6.2 布尔运算符

```python
and
or
not
```

**短路逻辑**（或者延迟求值）

#### 5.4.7 断言 assert

```python
age = -1
assert 0 < age < 100, 'The age must be realistic' 
Traceback (most recent call last):
    File "<stdin>", line 1, in ? 
AssertionError: The age must be realistic
```

### 5.5 循环

#### 5.5.1 while

#### 5.5.2 for

```python
words = ['this', 'is', 'an', 'ex', 'parrot'] 
for word in words:
    print(word)
# or
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
for number in numbers:
    print(number)
```

Range 函数，包含起始位置，但不包含结束位置。

#### 5.5.3 迭代字典

```python
for key, value in d.items():
    print(key, 'corresponds to', value)
```

#### 5.5.4 一些迭代工具

##### 5.5.4.1 并行迭代

```python
names = ['anne', 'beth', 'george', 'damon'] 
ages = [12, 45, 32, 102]
for i in range(len(names)):
    print(names[i], 'is', ages[i], 'years old')
```

内置函数**zip**，它将两个序列缝合起来，并返回一个由元组组成的序列。

```python
list(zip(names, ages))
# [('anne', 12), ('beth', 45), ('george', 32), ('damon', 102)] 
# “缝合”后，可在循环中将元组解包。
for name, age in zip(names, ages): 
    print(name, 'is', age, 'years old') 
```

函数 zip 可缝合任意数量的序列。需要指出的是，当序列的长度不同时，函数zip将在最短的序列用完后停止”缝合“。

##### 5.5.4.2 迭代时获取索引

```python
for string in strings:
    if 'xxx' in string:
		index = strings.index(string) # 在字符串列表中查找字符串 
        strings[index] = '[censored]'
```



```python
index = 0
for string in strings:
    if 'xxx' in string:
        strings[index] = '[censored]'
	index += 1
```

`enumerate`

```python
for index, string in enumerate(strings): 
    if 'xxx' in string:
         strings[index] = '[censored]'
```

##### 5.5.4.3 反向迭代和排序后再迭代

`sorted`，返回一个列表

`reversed，`，返回一个可迭代对象

#### 5.5.5 跳出循环

##### break，结束（跳出）当前循环

##### continue，结束（跳出）当前迭代，进入下一次迭代

##### while True/break 成例

```python
while True:
	word = input('Please enter a word: ') 
    if not word: break
	# 使用这个单词做些事情:
	print('The word was ', word)
```

#### 5.5.6 循环中的else子句

```python
from math import sqrt
for n in range(99, 81, -1):
	root = sqrt(n)
	if root == int(root):
		print(n)
		break 
 else:
    print("Didn't find it!")
```

else 子句仅在没有调用break时才执行。

非 else 子句实现如下：

```python
broke_out = False 
for x in seq:
	do_something(x) 
    if condition(x):
		broke_out = True
		break 
    do_something_else(x)
if not broke_out:
	print("I didn't break out!")
```

### 5.6 简单推导

列表推导是一种从其他列表创建列表的方式，类似于数学中的集合推导

```python
[(x, y) for x in range(3) for y in range(3)]
# 一个3x3的矩阵
```



```python
result = []
for x in range(3):
	for y in range(3) 
		result.append((x, y))
```



```python
girls = ['alice', 'bernice', 'clarice'] 
boys = ['chris', 'arnold', 'bob']
letterGirls = {}
for girl in girls:
	letterGirls.setdefault(girl[0], []).append(girl) 
print([b+'+'+g for b in boys for g in letterGirls[b[0]]])
```

### 5.7 三人行

#### pass

```python
if name == 'Ralph Auldus Melish': 
    print('Welcome!')
elif name == 'Enid': 
    # 还未完成......
	pass
elif name == 'Bill Gates':
	print('Access Denied')
```



#### del

```python
x = 1
del x
x
Traceback (most recent call last):
File "<pyshell#255>", line 1, in ? x
NameError: name 'x' is not defined
```



事实上，在Python中，根本没有办法删除值，而且你也不需要这样做，因为对你不在需要的值，Python解释器会立即将其删除。

#### exec

主要用于动态的创建代码字符串。

```python
from math import sqrt
scope = {}
# scope 作为 exec 函数的第二个参数，命名空间(否则代码将污染你的命名空间，即修改你的变量)
exec('sqrt = 1', scope)
sqrt(4)
# 2.0
scope['sqrt'] 1
```

**eval**是类似于 exec 的内置函数。

exec 执行一系列的Python 语句，而 eval 计算用字符串表示的Python表达式的值，并返回结果。

```python
eval(input("Enter an arithmetic expression: ")) Enter an arithmetic expression: 6 + 18 * 2
# 42
```

## 6 抽象

```python
fibs = [0, 1]
num = int(input('How many fibonacci numbers do you want?'))
for in in Range(num-2):
    fibs.append(fibs[-2]+fibs[-1])
print(fibs)

```

### 6.2 抽象和结构

### 6.3 自定义函数

判断某个对象是否可调用，可使用内置函数 `callable`

使用`def`定义函数：

```python
def hello(name):
	return 'Hello, ' + name + '!'
```

```python
def fibs(num):
    result = [0, 1]
    for i in range(num-2):
    	result.append(result[-2]+result[-1])
    return result
```

#### 6.3.1 给函数编写文档

**文档字符串**：

```python
def square(x):
    'Calculate the square of the number x.'
    return x*x

# __doc__是函数的一个属性
squate.__doc__
# 'Calculate the square of the number x.'
```

特殊的内置函数 help 很有用

```python
help(square)
Help on function square in module __main__:
square(x)
Calculates the square of the number x.
```

#### 6.3.2 其实并不是函数的函数

没有返回值的函数

### 6.4 参数魔法

#### 6.4.1 形参和实参

#### 6.4.2 能修改参数吗？

在函数内部，给参数赋值，对外部没有任何影响。

参数存储在**局部作用域**内



将同一个列表赋给两个变量时，这两个变量将同时指向这个列表。所以，给变量赋值会修改变量关联的列表。

要避免这样的结果，需要创建列表的**副本**。对序列执行切片操作时，返回的切片都是副本。因此，你创建覆盖整个列表的切片，得到的将是列表的副本。

##### 6.4.2.1 为何要修改参数

```python
MyNames = {}
init(MyNames)
store(MyNames, 'Magnus Lie Hetland') 14 
lookup(MyNames, 'middle', 'Lie')
# ['Magnus Lie Hetland']
```

##### 6.4.2.1 如果参数是不可变的

在有些语言(如C++、Pascal和Ada)中，经常需要给参数赋值并让这种修改影响函数外部的变
量。在 Python 中，没法直接这样做，只能修改参数对象本身。如果参数是不可变的，那就没办法了。

#### 6.4.3 关键字参数和默认值

前面使用的都是未知参数，因为他们的位置至关重要—事实上比名称还要重要。

#### 6.4.4 收集参数

```python
def print_params(*params): 
    print(params)
```



```python
def in_the_middle(x, *y, z):
    print(x, y, z)
in_the_middle(1, 2, 3, 4, 5, z=7)
1 (2, 3, 4, 5) 7
in_the_middle(1, 2, 3, 4, 5, 7) 
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: in_the_middle() missing 1 required keyword-only argument: 'z'
```

要收集关键字参数，可使用两个星号：

```python
def print_param_3(**param):
	print(param)
    
print_param_3(x=1, y=2, z=3)
# {'z': 3, 'x': 1, 'y': 2}
```



```python
def print_params_4(x, y, z=3, *pospar, **keypar): 
    print(x, y, z)
	print(pospar) 
    print(keypar)
    
print_params_4(1, 2, 3, 5, 6, 7, foo=1, bar=2) 
# 123
# (5, 6, 7)
# {'foo': 1, 'bar': 2}
print_params_4(1, 2) 
# 123
# ()
# {}
```

#### 6.4.5 分配参数

通过在调用函数时使用运算符`*`实现

```python
def add(x, y): 
    return x + y
param = (1, 2)
add(*param)
# 3
```



### 6.5 作用域

除全局作用域外，每个函数调用都将创建一个命名空间（作用域）。

`vars` ，

`globals` 返回一个包含全局变量的字典。

`locals` 返回一个包含局部变量的字典。

**遮盖**，如果有一个局部变量或参数与你要访问的全局变量同名，就无法直接访问全局变量，因为它被局部变量遮
住了。

**重新关联**全局变量：

```python
x = 1
def change_global():
	global x 
    x=x+1
change_global() 
x
# 2
```

**作用域嵌套**

Python 函数可以嵌套：

```python
def foo():
	def bar():
		print("Hello, world!") 
    bar()
```

突出的作用是：使用一个函数创建另一个函数。

```python
def multiplier(factor):
    def mutiplyByFactor(number):
        return factor * number
    return mutiplyByFactor
```

外面的函数返回里面的函数。

像 mutiplyByFactor 这样存储其所在作用域的函数称为**闭包**。

通过关键字`nonlocal`让你能给外部作用域（非全局作用域）内的变量赋值。

### 6.6 递归

最大递归深度

无穷递归

递归函数通常包含下面两部分：

- 基线条件：满足这种条件时，函数将一直返回一个值。
- 递归条件：包含一个或多个调用，这些问题旨在解决**问题的一部分**。

通过将问题分解为较小的部分，可避免递归没完没了，因为问题终将被分解成基线条件可以解决的最小问题。

#### 6.6.1 两个经典案例：阶乘和幂

##### 6.6.1.1 阶乘

```python
def factorial(n): 
    result = n
	for i in range(1, n): 
        result *= i
	return result
```

如果用递归的思想来解这道题呢？

- 1 的阶乘是 1
- 对于大于 1 的数字 n，其阶乘为 n-1 的阶乘再乘以 n

```python
def factorial(n):
	if n == 1:
        return 1
    else:
        return n * factorial(n-1)
```



##### 6.6.1.2 幂

```python
def power(x, n):
    result = 1
    for i in range(n):
    	result *= x
    return result
```

- 对于任何数字 x，`power(x, 0)` 都为 1
- n > 0 时，`power(x, n)` 为 `power(x, n-1)`与x的乘积

```python
def power(x, n):
    if n==0:
        return 1
    elif n > 0:
        return power(x, n-1) * x
```

#### 6.6.2 另一个经典案例：二分查找



- 如果上限和下限相同，就说明他们都指向数字所在的位置，因此将这个数字返回。
- 否则，找出区间的中间位置（上限和下限的平均值），在确定数字在左半部分还是右半部分。然后再继续在数字所在的那部分中查找。

```python
def search(sequence, number, lower = 0, upper = None):
    if upper == None: upper = len(sequence) - 1
    if lower == upper:
        assert number == sequence[upper]
        return upper
    else:
        middle = (lower+upper) // 2
        if number > sequence[middle]:
            return search(sequence, number, middle+1, upper)
        else:
            return search(sequence, number, lower, middle)     
```

**函数式编程**

Python 提供了有助于函数式编程的函数：

`map(func, seq[, seq, ...])`，对于序列中的所有元素执行函数

 `filter(func, seq)`，返回一个列表，其中包含对其执行函数时结果为真的所有元素

 `reduce(func, seq[, initial])`，等价于func(func(func(seq0, seq2), ...)

`Lambda` 表达式

新版 Python 使用列表推到来替换，`map` `filter` 

```python
list(map(str, range(10))) # 与[str(i) for i in range(10)]等价

# ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
```

## 7 再谈抽象

自定义对象

### 7.1 对象魔法

使用对象的好处：

- 多态，对不同类型的对象，执行相同的操作。
- 封装，对外部隐藏有关对象工作原理的细节。
- 继承，可基于通用类创建出专用类。

#### 7.1.1 多态（polymorphism）

#### 7.1.2 多态和方法

与对象属性相关联的函数称为**方法**。

```python
from random import choice
x = choice(['Hello, world!', [1, 2, 'e', 'e', 4]])
x.count('e')
# 2
```

从上述结果看，x包含的应该是列表。但关键在于你无需执行相关的检查，只要X有一个名为count的方法，它将单个字符作为参数并返回一个整数就行。

**多态形式多样**

```python
1 + 2
# 3
'Fish' + 'license' 
# 'Fishlicense'
```

`+`运算符既可用于数，也可用于字符串。

事实上，要破坏多态，唯一的办法是使用诸如`type`、`issubclass`等函数显式地执行类型检查，但你应尽可能避免以这种方式破坏多态。引入本章后面将讨论的**抽象基类**和模块abc后，函数`issubclass`本身也是多态的了!



#### 7.1.3 封装

封装指的是向外部隐藏不必要的细节。

封装不同于多态。多态让你无需知道对象所属的类(对象的类型)就能调用其方法，而封装让你无需知道对象的构造就能使用它。

如果尝试创建多个`OpenObject`对象，将出现问题，因为它们共用同一个变量。 

```python
o1 = OpenObject()
o2 = OpenObject()
o1.set_name('Robin Hood')
o2.get_name()
# 'Robin Hood' 
```

基本上，希望对象是抽象的：当调用方法时，无需操心其他事情，如避免干扰全局变量。实现方式是，将方法作为对象的一个属性。属性是归属于对象的变量，就像方法一样。实际上，方法差不多就是与函数相关联的属性。

如果你使用属性而非全局变量重新编写前面的类，并将其重命名为`ClosedObject`，就可像下面这样使用它: 

```python
c = ClosedObject()
c.set_name('Sir Lancelot')
c.get_name()
# 'Sir Lancelot' 
```



#### 7.1.4 继承

### 7.2 类

#### 7.2.1 类到底是什么

本书前面反复提到了**类**，并将其用作**类型**的同义词。从很多方面来说，这正是类的定义——
**一种对象**。**每个对象都属于特定的类，并被称为该类的实例**。

ps. Python 2，中类和类型差别不那么明显。Python 3 中已经不区分类和类型了。

子类

超类

#### 7.2.2 创建自定义类

```python
__metaclass__ = type # python 2
class Person:
    def set_name(self, name):
        self.name = name
    def get_name(self):
        return self.name
    def greet(self):
        print("Hello, world! I'm {}.".format(self.name))
```

`self` 指向对象本身。

```python
foo = Person()
bar = Person()
foo.set_name('Luke Skywalker')
bar.set_name('Anakin Skywalker')
foo.greet()
# Hello, world! I'm Luke Skywalker.
bar.greet()
# Hello, world! I'm Anakin Skywalker.
```

对foo调用set_name和greet时，foo都会作为第一个参数自动传递给它们。我将这个参数命名为self，这非常贴切。实际上，可以随便给这个参数命名，但鉴于它总是指向对象本身，因此习惯上将其命名为self。

#### 7.2.3 属性、函数和方法

实际上，方法和函数的区别表现在前一节提到的参数self上。方法(更准确地说是关联的方法)将其第一个参数关联到它所属的实例，因此无需提供这个参数。无疑可以将属性关联到一个普通函数，但这样就没有特殊的self参数了。

#### 7.2.4 再谈隐藏

Python没有为私有属性提供直接的支持，而是要求程序员知道在什么情况下从外部修改属性是安全的。

通过，`__`打头的属性，强烈告知他人不要访问“私有属性”

然而， 幕后的处理手法并不标准:在类定义中，对所有以两个下划线打头的名称都进行转换，即在开头加上一个下划线和类名。 

```python
Secretive._Secretiveinaccessible 
# <unbound method Secretive.inaccessible> 
```

只要知道这种幕后处理手法，就能从类外访问私有方法，然而不应这样做。 

```python
s._Secretive__inaccessible() 
# Bet you can't see me
```

总之，你无法禁止别人访问对象的私有方法和属性，但这种名称修改方式发出了强烈的信号， 让他们不要这样做。 

如果你不希望名称被修改，又想发出不要从外部修改属性或方法的信号，可用一个下划线打头。这虽然只是一种约定，但也有些作用。例如，`from module import *`不会导入以一个下划线打头的名称。 

#### 7.2.5 类的命名空间

类定义其实就是要执行的代码段。

每个实例都可访问这个类作用域内的变量，就像方法一样。

#### 7.2.6 指定超类

要指定超类，可在class语句中的类名后加上超类名，并将其用圆括号括起。 

#### 7.2.7 深入讨论继承

如果你有一个类，并想知道它的基类，可访问其特殊属性`__bases__`

```python
SPAMFilter.__bases__
# (<class main.Filter at 0x171e40>,) 
Filter.__bases__
# (<class 'object'>,) 
```

同样，要确定对象是否是特定类的实例，可使用isinstance

```python
s = SPAMFilter()
isinstance(s, SPAMFilter) 
# True
isinstance(s, Filter)
# True
isinstance(s, str) 
# False
```

> 使用isinstance通常不是良好的做法，依赖多态在任何情况下都是更好的选择。一个重要
> 的例外情况是使用抽象基类和模块abc时。

如果你要获悉对象属于哪个类，可使用属性`__class__ `

```python
s.__class__
# <class main.SPAMFilter at 0x1707c0>
# 新式类还可以使用 type(s)
```

#### 7.2.8 多个超类

**多重继承**，是一个功能强大的工具。然而，除非万不得已，否则应避免使用多重继
承，因为在有些情况下，它可能带来意外的“并发症”。

使用多重继承时，有一点务必注意:如果多个超类以不同的方式实现了同一个方法(即有多个同名方法)，必须在class语句中小心排列这些超类，因为位于前面的类的方法将覆盖位于后面的类的方法。因此，在前面的示例中，如果Calculator类包含方法talk，那么这个方法将覆盖Talker类的方法talk(导致它不可访问)。

#### 7.2.9 接口和内省

接口这一概念与多态相关。处理多态对象时，你只关心其接口(协议)——对外暴露的方法和属性。

```python
# 检查所需的方法是否存在
hasattr(tc, 'talk') 
# True
hasattr(tc, 'fnord') 
# False
```



```python
# 检查talk属性是否可调用
callable(getattr(tc, 'talk', None)) 
# True
callable(getattr(tc, 'fnord', None)) 
# False
```



要查看对象中存储的所有值，可检查其`__dict__`属性。

#### 7.2.10 抽象基类

Python 几乎都只依赖于鸭子类型，即假设所有对象都能完成其工作，同时偶尔使用`hasattr`来检查所需的方法是否存在。

一般而言，抽象类是不能(至少是**不应该**)实例化的类，其职责是定义子类应实现的一组抽象方法。

```python
from abc import ABC, abstractmethod
class Talker(ABC): 
    @abstractmethod 
    def talk(self):
		pass
```

形如`@this`的东西被称为装饰器，这里的要点是你使用`@abstractmethod`来将方法标记为抽象的——在子类中必须实现的方法。 

```python
Talker()
# Traceback (most recent call last):
#. File "<stdin>", line 1, in <module>
# TypeError: Can't instantiate abstract class Talker with abstract methods talk

```

将Herring注册为Talker (而不从Herring和Talker派生出子类)，这样所有的Herring对象都将被视为Talker对象。

```python
Talker.register(Herring) 
# <class '__main__.Herring'>
isinstance(h, Talker) 
# True
issubclass(Herring, Talker) 
# True
```

然而，这种做法存在一个缺点，就是直接从抽象类派生提供的保障没有了。

```python
class calm:
    pass

Talker.register(calm)
# <class '__main__.Clam'>

issubclass(Clam, Talker) 
# True
c = Clam()

isinstance(c, Talker)
# True
c.talk()
# Traceback (most recent call last):
# File "<stdin>", line 1, in <module>
# AttributeError: 'Clam' object has no attribute 'talk'
```

换而言之，应将isinstance返回True视为一种意图表达。在这里，Clam有成为Talker的意图。本着鸭子类型的精神，我们相信它能承担Talker的职责，但可悲的是它失败了。

### 7.3 关于面向对象设计的一些思考

- 将相关的东西放在一起。如果一个函数操作一个全局变量，最好将它们作为一个类的属性和方法。 
- 不要让对象之间过于亲密。方法应只关心其所属实例的属性，对于其他实例的状态，让它们自己去管理就好了。 
- 慎用继承，尤其是多重继承。继承有时很有用，但在有些情况下可能带来不必要的复杂性。要正确地使用多重继承很难，要排除其中的bug更难。 
- 保持简单。让方法短小紧凑。一般而言，应确保大多数方法都能在30秒内读完并理解。对于其余的方法，尽可能将其篇幅控制在一页或一屏内。 

确定需要哪些类以及这些类应该包含哪些方法时，尝试：

- 将有关问题的描述记录下来，并给所有的名词、动词和形容词加上标记。
- 在名词中找出可能的类
- 在动词中找出可能的方法
- 在形容词中找出可能的属性
- 将找出的方法和属性分配给各类

有了面向对象模型的草图，进一步确定类之间的关系以及他们的职责。

为进一步改进模型，可像下面这样做。
 (1) 记录(或设想)一系列用例，即使用程序的场景，并尽力确保这些用例涵盖了所有的功能。

 (2) 透彻而仔细地考虑每个场景，确保模型包含了所需的一切。如果有遗漏，就加上;如果 

有不太对的地方，就修改。不断地重复这个过程，直到对模型满意为止。 



## 8 异常

### 8.1 异常是什么

Python 中使用**异常对象**来表示异常状态，并在遇到错误时**引发**异常。异常对象未被处理(或捕获)时，程序将终止并显示一条错误消息(**traceback**)。

### 8.2 让事情沿你指定的轨道出错

#### 8.2.1 raise 语句

要引发异常，可使用raise语句，并将一个类(必须是Exception的子类)或实例作为参数。将类作为参数时，将自动创建一个实例。下面的示例使用的是内置异常类Exception:

```python
# 引发通用异常
raise Exception
# Traceback (most recent call last): File "<stdin>", line 1, in ?
# Exception

# 错误消息:hyperdrive overload
raise Exception('hyperdrive overload') 
# Traceback (most recent call last):
# File "<stdin>", line 1, in ? 
# Exception: hyperdrive overload
```



| 类名              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| Exception         | 几乎所有的异常类都是从它派生而来的                           |
| AttributeError    | 引用属性或给它赋值失败时引发                                 |
| OSError           | 操作系统不能执行指定的任务(如打开文件)时引发，有多个子类     |
| IndexError        | 使用序列中不存在的索引时引发，为LookupError的子类            |
| KeyError          | 使用映射中不存在的键时引发，为LookupError的子类              |
| NameError         | 找不到名称(变量)时引发                                       |
| SyntaxError       | 代码不正确时引发                                             |
| TypeError         | 将内置操作或函数用于类型不正确的对象时引发                   |
| ValueError        | 将内置操作或函数用于这样的对象时引发:其类型正确但包含的值不合适 |
| ZeroDivisionError | 在除法或求模运算的第二个参数为零时引发                       |



#### 8.2.2 自定义的异常类

```python
class SomeCustomException(Exception): 
    # 可以添加方法
    pass
```

### 8.3 捕获异常

```python
# try except
try:
	x = int(input('Enter the first number: ')) 
    y = int(input('Enter the second number: ')) 
    print(x / y)
except ZeroDivisionError:
	print("The second number can't be zero!")
```

#### 8.3.1 不用提供参数

捕获异常后，如果要重新引发它(即继续向上传播)，可调用raise且不提供任何参数。

```python
class MuffledCalculator: 
    muffled = False
	def calc(self, expr): 
        try:
			return eval(expr) 
        except ZeroDivisionError: 
            if self.muffled:
				print('Division by zero is illegal') 
            else:
                # 不带参数的raise
				raise
```

有时你可能想引发别的异常。在这种情况下，导致进入except子句的异常将被作为异常上下文存储起来，并出现在最终的错误消息中，如下所示:

```python
try:
    1/0
except ZeroDivisionError:
    raise ValueError
    
    
# Traceback (most recent call last):
# File "<stdin>", line 2, in <module> 
# ZeroDivisionError: division by zero    
```

你可使用`raise ... from ...`语句来提供自己的异常上下文，也可使用`None`来禁用上下文。

```python
try:
    1/0
except ZeroDivisionError:
    raise ValueError from None
    
# Traceback (most recent call last):
# File "<stdin>", line 4, in <module> 
# ValueError    
```

#### 8.3.2 多个except 子句

```python
# try except
try:
	x = int(input('Enter the first number: ')) 
    y = int(input('Enter the second number: ')) 
    print(x / y)
except ZeroDivisionError:
	print("The second number can't be zero!")
except TypeError:
    print("That wasn't a number, was it?")
```

#### 8.3.3 一箭双雕

如果要使用一个except子句捕获多种异常，可在一个元组中指定这些异常

```python
try:
	x = int(input('Enter the first number: ')) 
    y = int(input('Enter the second number: ')) 
    print(x / y)
except (ZeroDivisionError, TypeError, NameError):
	print("Your numbers were bogus ...")
```

#### 8.3.4 捕获对象

```python
try:
	x = int(input('Enter the first number: ')) 
    y = int(input('Enter the second number: ')) 
    print(x / y)
except (ZeroDivisionError, TypeError, NameError) as e:
	print(e)
```

#### 8.3.5 一网打尽

```python
try:
	x = int(input('Enter the first number: ')) 
    y = int(input('Enter the second number: ')) 
    print(x / y)
except:
	print("Something wrong happend ...")
```

像这样捕获所有的异常很危险，因为这不仅会隐藏你有心理准备的错误，还会隐藏你没有考虑过的错误。更好的做法：

```python
try:
	x = int(input('Enter the first number: ')) 
    y = int(input('Enter the second number: ')) 
    print(x / y)
except Exception as e
	print(e)
```

#### 8.3.6 万事大吉

在有些情况下，在没有出现异常时执行一个代码块很有用

```python
try:
	print('A simple task')
except:
	print('What? Something went wrong?')
else:
	print('Ah ... It went as planned.')
```

#### 8.3.7 finally

最后，还有finally子句，可用于在发生异常时执行清理工作。

finally子句非常适合用于确保文件或网络套接字等得以关闭

```python
try: 
    1/0
except NameError: 
    print("Unknown variable")
else:
	print("That went well!")
finally:
	print("Cleaning up.")
```

### 8.4 异常和函数

异常和函数有着天然的联系。

如果不处理函数中引发的异常，它将向上传播到调用函数的地方。

​	如果在那里也未得到处理，异常将继续传播，直至到达主程序(全局作用域)。

​		如果主程序中也没有异常处理程序，程序将终止并显示栈跟踪消息。

### 8.5 异常之禅

有时候，可使用条件语句来达成异常处理实现的目标，但这样编写出来的代码可能不那么自然，可读性也没那么高。另一方面，有些任务使用if/else完成时看似很自然，但实际上使用try/except来完成要好得多。

检查对象是否包含特定的属性时，try/except也很有用。

### 8.6 不那么异常的情况

如果你想发出警告，指出情况偏离了正轨，可以使用模块 warnings 中的函数 warn。

```python
from warnings import warn
warn("I've got a bad feeling about this.")
# __main__:1: UserWarning: I've got a bad feeling about this.
```

警告只显示一次。可使用模块warnings中的函数filterwarnings来抑制你发出的警告(或特定类型的警告)，并指定要采取的措施，如"error"或"ignore"。

```python
filterwarnings("error")
warn("This function is really old...", DeprecationWarning) 
# Traceback (most recent call last):
# File "<stdin>", line 1, in <module>
# DeprecationWarning: This function is really old...
filterwarnings("ignore", category=DeprecationWarning)
warn("Another deprecation warning.", DeprecationWarning)
warn("Something else.")
# Traceback (most recent call last):
# File "<stdin>", line 1, in <module> 
# UserWarning: Something else.
```

## 9 魔法方法、特性和迭代器

### 9.1 如果你使用的不是Python3

如果你的程序无需与旧版Python兼容，建议将所有类都定义为**新式类**。

在Python 3中没有旧式类，因此无需显式地继承object或将`__metaclass__`设置为type。所有的类都将隐式地继承object。如果没有指定超类，将直接继承它，否则将间接地继承它。

### 9.2 构造函数

**构造函数**不同于普通方法的地方在于，将在对象创建后自动调用它们。

```python
class FooBar:
	def __init__(self, value = 42):
		self.somevar = value
f = FooBar()
f.somevar 
# 42
```

Python提供了魔法方法`__del__`，也称作**析构函数**。这个方法在对象被销毁前被调用，但鉴于你无法知道准确的调用时间，建议尽可能不要使用`__del__`。

#### 9.2.1 重写普通方法和特殊的构造函数

与重写普通方法相比，**重写构造函数**时更有可能遇到一个特别的问题:重写构造函数时，**必须调用超类(继承的类)的构造函数，否则可能无法正确地初始化对象**。

#### 9.2.2 调用未关联的超类构造函数

```python
class Bird:
	def __init__(self):
		self.hungry = True 
    def eat(self):
        if self.hungry: 
            print('Aaaah ...') 
            self.hungry = False
        else:
        	print('No, thanks!')

class SongBird(Bird): 
    def __init__(self):
		Bird.__init__(self)
		self.sound = 'Squawk!' 
    def sing(self):
		print(self.sound)
```

对实例调用方法时，方法的参数self将自动关联到实例。如果你通过类调用方法，就没有实例与其相关联。在这种情况下，你可随便设置参数self，这样的方法称为**未关联的**。

通过将这个未关联方法的self参数设置为当前实例，将使用超类的构造函数来初始化SongBird对象。这意味着将设置其属性hungry。

#### 9.2.3 使用函数super

super函数只适用于新式类

调用这个函数时，将当前类和当前实例作为参数。对其返回的对象调用方法时，调用的将是超类的方法。

```python
# Python 3 中调用super时，可不提供任何参数
class Bird:
	def __init__(self):
		self.hungry = True 
    def eat(self):
        if self.hungry: 
            print('Aaaah ...') 
            self.hungry = False
        else:
        	print('No, thanks!')

class SongBird(Bird): 
    def __init__(self):
		super().__init__()
		self.sound = 'Squawk!' 
    def sing(self):
		print(self.sound)
```

使用函数super比调用超类的未关联构造函数(或其他方法)要好得多。

函数super返回的到底是什么呢？实际上他返回的是一个super对象，该对象将负责为你执行方法解析，当你访问它的属性时，它将在所有的超类中查找，直到找到指定的属性或引发AttributeError异常。

### 9.3 元素访问

> Python 中，协议通常指的是规范行为的规则，有点类似于**接口**。协议指定应实现哪些方法以及这些方法应做什么。

#### 9.3.1 基本的序列和映射协议

序列和映射基本上是元素的集合，要实现他们的基本行为（协议），不可变对象需要实现两个方法，而可变对象需要实现4个。

- `__len__(self):`，这个方法应返回集合包含的项数，对序列来说为元素个数，对映射来说为键值对数。
- `__getitem__(self, key):`，这个方法返回与指定键相关联的值。
- `__setitem__(self, key):`，这个方法应以与键相关联的方式存储，以便以后能够使用`__getitem__(self, key):`来获取。仅当对象可变时才需要实现这个方法。
- `__delitem__(self, key):`，这个方法在对对象使用`__del__`语句时被调用，应删除与key相关联的值。仅当对象可变时才需要实现这个方法。

额外的要求：

- 对于序列，如果键为负整数，应从末尾往前数。即 `x[-n]`应该与`x[len(x)-n]`等效
- 如果键的类型不合适，可能引发TypeError异常
- 对于序列，如果索引的类型是正确的，但不在允许的范围内，应该引发 `IndexError`异常

#### 9.3.2 从list、dict 和 str 派生

在标准库中，模块collections提供了抽象和具体的基类，但你也可以继承内置类型。因此，如果要实现一种行为类似于内置列表的序列类型，可直接继承list。

### 9.5 特性

#### 9.5.1 函数 property

```python
class Rectangle:
    def __init__ (self):
        self.width = 0
        self.height = 0
    def set_size(self, size):
    	self.width, self.height = size 
    def get_size(self):
    	return self.width, self.height
    # 存取方法作为参数，获取方法在前，设置方法在后
	size = property(get_size, set_size)
    
# 调用
r = Rectangle()
r.width = 10
r.height = 5
r.size
# (10, 5)
r.size = 150, 100
r.width 
# 150
```



> 如果特性的行为怪异，务必确保你使用的是新式类(通过直接或间接地继承object或直接设置`__metaclass__`)。不然，特性的获取方法依然正常，但设置方法可能不正常(是否如此取决于使用的Python版本)。这可能有点令人迷惑 

实际上，调用函数 property时，还可：

- 不指定参数，创建的特性将既不可读也不可写
- 指定一个参数，创建的特性将只读
- 指定三个参数，第三个参数是可选的，指定用于删除属性的方法
- 指定四个参数，第四个参数是可选的，指定一个文档字符串。

这些参数分别名为：`fget` `fset` `fdel` `doc`

##### 9.5.1.1 函数 property 工作原理

Property 是一个类。它的实例包含一些魔法方法，而所有的魔法都是由这些方法完成的。

`__get__`

`__set__`

`__delete__`

它们一道定义了所谓的描述符协议。只要对象实现了这些方法中的任何一个，他就是一个描述符。描述符的独特方式在于其访问方式。例如，读取属性(具体来说，是在实例中访问类中定义的属性)时，如果它关联的是一个实现了`__get__`的对象，将不会返回这个对象，而是调用方法`__get__`并将其结果返回。实际上，这是隐藏在特性、关联的方法、静态方法和类方法(详细信息请参阅下一小节)以及super后面的机制。

#### 9.5.2 静态方法和类方法

静态方法和类方法分别包装在，staticmethod和classmethod类的对象中。

静态方法的定义中没有参数self，可直接通过类来调用。

类方法的定义中包含类似于self的参数，通常被命名为cls。对于类方法也可通过对象直接调用，但参数cls将自动关联到类。

```python
class MyClass:
    def smeth():
        print("This is a static method")
    smeth = staticmethod(smeth)
    
    def cmeth():
        print("This is a class method of", cls)
    cmeth = classmethod(cmeth)
```

Python 2.4 引入了一种名为**装饰器**的新语法

```python
class MyClass:
	@staticmethod 
    def smeth():
        print('This is a static method')
    
    @classmethod
	def cmeth(cls):
		print('This is a class method of', cls)
```

#### 9.5.3 `__getattr__`/`__setattr__`等方法

可以拦截对对象属性的所有访问企图，其用途之一是在旧式类中实现特性

- `__getattribute__(self, name):`在属性被访问时自动调用(只适用于新式类)。
- `__getattr__(self, name):`在属性被访问而对象没有这样的属性时自动调用。
- `__setattr__(self, name, value):`试图给属性赋值时自动调用。
- `__delattr__(self, name):`试图删除属性时自动调用。



```python
class Rectangle:
    def __init__ (self):
        self.width = 0
        self.height = 0
    def __setattr__(self, name, value):
        if name == 'size':
    		self.width, self.height = value
        else:
            self.__dict__[name] = value
    def __getattr__(self, name):
        if name == 'size':
        	return self.width, self.height
        else:
            raise AttributeError()
```

注意：

- 即便涉及的属性不是size，也将调用方法`__setattr__`。因此这个方法必须考虑如下两种情形:
  - 如果涉及的属性为size，就执行与以前一样的操作;
  - 否则就使用魔法属性`__dict__`。 `__dict__`属性是一个字典，其中包含所有的实例属性。之所以使用它而不是执行常规属性 赋值，是因为旨在避免再次调用`__setattr__`，进而导致无限循环。 
- 仅当没有找到指定的属性时，才会调用方法`__getattr__ `。这意味着如果指定的名称不是 size，这个方法将引发AttributeError异常。这在要让类能够正确地支持hasattr和getattr 等内置函数时很重要。如果指定的名称为size，就使用前一个实现中的表达式。 



### 9.6 迭代器

#### 9.6.1 迭代器协议

前面只使用 for 循环迭代过序列和字典，也可迭代实现了方法`__iter__`的对象。

> 在Python3中，迭代器协议有细微的变化。在以前的迭代器协议中，要求迭代器对象包含
> 方法next而不是`__next__`。



```python
class Fibs:
	def __init__(self):
		self.a = 0
		self.b = 1
	def __next__(self):
		self.a, self.b = self.b, self.a + self.b
		return self.a 
	def __iter__(self):
		return self
```



> 实现了方法`__iter__`的对象是可迭代的，而实现了方法`__next__`的对象
> 是迭代器。



```python
fibs = Fibs()
for f in fibs:
    if f>100:
        print(f)
		break
```



通过对可迭代对象调用内置函数iter，可获得一个迭代器

```python
it = iter([1,2,3])
next(it)
# 1
next(it)
# 2
```

#### 9.6.2 从迭代器创建序列

除了对迭代器和可迭代对象进行迭代(通常这样做)之外，还可将它们转换为序列。

一个这样的例子是使用构造函数list显式地将迭代器转换为列表。

```python
class TestIterator:
    value = 0
    def __next__(self):
		self.value += 1
        if self.value > 10: raise StopIteration
        return self
    def __iter__(self):
        return self
    
ti = TestIterator()
list(ti)
# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### 9.7 生成器

生成器是一种使用普通函数语法定义的迭代器。

#### 9.7.1 创建生成器

```python
def flatten(nested):
	for sublist in nested:
		for element in sublist: 
            yield element

nested = [[1, 2], [3, 4], [5]]
for num in flatten(nested):
    print(num)
 # 1 2 3 4 5

# -------------------------------
list(flatten(nested))
# [1, 2, 3, 4, 5]
    
```

包含yield语句的函数都被称为生成器

##### 9.7.1.1 简单生成器

生成器推到（也叫生成器表达式）。

其工作原理与**列表推导**相同，但不是创建一个列表(即不立即执行循环)，而是返回一个生成器，让你能够逐步执行计算。

```python
# 使用圆括号
g = ((i+2)**2 for i in range(2, 27))
next(g)
# 16
```

如果要包装可迭代对象(可能生成大量的值)，使用列表推导将立即实例化一 个列表，从而丧失迭代的优势。 

另一个好处是，直接在一对既有的圆括号内(如在函数调用中)使用生成器推导时，无需 再添加一对圆括号。换而言之，可编写下面这样非常漂亮的代码: 

```python
sum(i ** 2 for i in range(10)) 
```

#### 9.7.2 递归式生成器

```python
def flatten(nested): 
    try:
		for sublist in nested:
			for element in flatten(sublist):
				yield element 
    except TypeError:
		yield nested

list(flatten([[[1], 2], 3, 4, [5, [6, 7]], 8]))    
# [1, 2, 3, 4, 5, 6, 7, 8]
```

调用flatten时，有两种可能性(处理递归时都如此):**基线条件**和**递归条件**。在基线条件下， 要求这个函数展开单个元素(如一个数)。在这种情况下，for循环将引发TypeError异常(因为 你试图迭代一个数)，而这个生成器只生成一个元素。 

然而，如果要展开的是一个列表(或其他任何可迭代对象)，你就需要做些工作:遍历所有 的子列表(其中有些可能并不是列表)并对它们调用flatten，然后使用另一个for循环生成展开 后的子列表中的所有元素。这可能看起来有点不可思议，但确实可行。 

> 在函数flatten中，不应该对类似于字符串的对象进行迭代，主要原因有两个。首先，你想将类似于字符串的对象视为原子值，而不是应该展开的序列。其次，对这样的对象进行迭代会导致无穷递归，因为字符串的第一个元素是一个长度为1的字符串，而长度为1的字符串的第一个元素是字符串本身!



```python
def flatten(nested): 
    try:
        try: nested + ''
        expect TypeError: pass
        else: raise TypeError
		for sublist in nested:
			for element in flatten(sublist):
				yield element 
    except TypeError:
		yield nested

list(flatten([[[1], 2], 3, 4, [5, [6, 7]], 8]))    
# [1, 2, 3, 4, 5, 6, 7, 8]
```

如果表达式nested + ''引发了TypeError异常，就忽略这种异常;如果没有引发TypeError异常，内部try语句中的else子句将引发TypeError异常，这样将在外部的excpet子句中原封不动地生成类似于字符串的对象。

我没有检查nested是否是字符串，而只是检查其行为是否类似于字符串，即能否与字符串拼接。对于这种检查，一种更自然的替代方案是，使用isinstance以及字符串和类似于字符串的对象的一些抽象超类，但遗憾的是没有这样的标准类。

#### 9.7.3 通用生成器

**生成器**有两个单独的部分组成：

- 生成器的函数，是由def语句定义的，其中包含`yield`
- 生成器的迭代器，是这个函数返回的结果

#### 9.7.4 生成器的方法

在生成器开始运行后，可使用生成器和外部之间的通信渠道向它提供值 。这个通讯渠道包含如下两个端点：

- 外部世界：可访问生成器的方法 `send`，这个方法类似于 `next`，但接受一个参数
- 生成器：在挂起的生成器内部，yield 可能用作 **表达式**而不是**语句**。

> 仅当生成器被挂起后（即遇到第一个yield后），使用send才有意义。要在此之前向生成器提供信息，可使用生成器的函数的参数。如果一定要在生成器刚启动时对其调用方法send，可向它传递参数None

- 方法**throw**：用于在生成器中（yield表达式处）引发异常，调用时可提供一个异常类型、一个可选值和一个traceback对象
- 方法**close**：用于停止生成器，调用时无需提供任何参数。

方法 close （由Python垃圾收集器在需要时调用）也是基于异常的：在 yield 处引发GeneratorExit异常。因此如果要在生成器中提供一些清理代码，可将 yield 放在一条 try/finally 语句中。如果愿意，也可捕获GeneratorExit异常，但随后必须重新引发它(可能在清理后)、引发其他异常或直接返回。对生成器调用close后，再试图从它那里获取值将导致RuntimeError异常。

#### 9.7.5 模拟生成器

```python
def flatten(nested): 
    result = []
    try:
    	# 不迭代类似于字符串的对象: 
        try: nested + ''
    	except TypeError: pass
   		else: raise TypeError
    	for sublist in nested:
    		for element in flatten(sublist): 
                result.append(element)
    except TypeError: 
        result.append(nested)
    return result
```

尽管使用这种方法并不能模拟所有的生成器，但可模拟大部分生成器。例如，这无法模拟无穷生成器，因为显然不能将这种生成器的值都存储到一个列表中。

### 9.8 八皇后问题

八皇后问题是个著名的计算机科学问题，使用**生成器**可轻松地解决它。这个问题要求在棋盘上放置8个皇后，并确保任何两个皇后都不能相互攻击。
