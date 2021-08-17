---
title: 《Python 基础教程第三版》摘录Part2
date: 2019-04-04 17:49:50
tags:
- Python
categories:
- Python
---



## 前言

断断续续把这《Python 基础教程第三版》翻了一遍了，对于Python的工作原理，还是不甚清晰。要让只是掌握起来还是需要使用这门语言然后，让编程变得有趣。Python 的优点之一是能够快速的编写程序（无需编译链接等），然后运行查看结果。接下来我该动手写点儿Demo 了 ：）

<!--more-->



## 10. 开箱即用

### 10.1 模块

#### 10.1.1 模块就是程序

```python
import sys
sys.path.append('C:/python')
```

> 在UNIX中，不能直接将字符串`'~/python'`附加到`sys.path`末尾，而必须使用完整的路径 (如`'/home/yourusername/python'`)。如果你要自动创建完整的路径，可使用`sys.path.expanduser('~/python') `

当你导入模块时，可能发现其所在目录中除源代码文件外，还新建了一个名为`__pycache__`的子目录(在较旧的Python版本中，是扩展名为`.pyc`的文件)。这个目录包含处理后的文件，Python能够更高效地处理它们。以后再导入这个模块时，如果.py文件未发生变化，Python将导入处理后的文件，否则将重新生成处理后的文件。删除目录`__pycache__`不会有任何害处，因为必要时会重新创建它。

这告诉解释器，除了通常将查找的位置外，还应到目录`C:\python`中去查找这个模块。这样做 后，就可以导入这个模块了(它存储在文件`C:\python\hello.py`中)。 

```python
import hello Hello, world! 

```



> 在大多数情况下，只导入一次是重要的优化，且在下述特殊情况下显得尤为重要:两个模块彼此导入对方。



**重新加载**模块：

```python
import importlib
hello = importlib.reload(hello) 
# Hello, world!

```

#### 10.1.2 模块是用来下定义的

模块在首次被导入程序时执行。

##### 10.1.2.1 在模块中定义函数

在模块中定义的类和函数以及对其进行赋值的变量都将成为模块的属性。

通过将代码放在模块中，就可在多个程序中使用它们，从而实现**重用代码**。要让代码是可重用的，务必将其模块化!

##### 10.1.2.2 在模块中添加测试代码

```python
# hello4.py
def hello():
	print("Hello, world!")
def test(): 
    hello()
if __name__ == '__main__': 
    test()

```

如果将这个模块作为程序运行，将执行函数hello;如果导入它，其行为将像普通模块一样。

#### 10.1.3 让模块可用

·sys.path一开始就包含正确的目录(你的模块所在的目录)。为此有两种办法:

**将模块放在正确的位置**

- 只需找出Python解释器到哪里去查找模块，再将文件放在这个地方即可。
- 你使用的计算机中，如果Python解释器是管理员安装的，而你有没有管理员权限，就可能无法将模块保存到Python使用的目录中。 

```python
import sys, pprint
# 可在模块sys的变量path中找到目录列表(即搜索路径)
pprint.pprint(sys.path) 
# ['C:\\Python35\\Lib\\idlelib','C:\\Python35', 'C:\\Python35\\DLLs', 'C:\\Python35\\lib', 'C:\\Python35\\lib\\plat-win', 'C:\\Python35\\lib\\lib-tk', 'C:\\Python35\\lib\\site-packages']

```

只要模块位于类似于site-packages这样的地方，所有的程序就都能够导入它。

**告诉解释器到哪里去查找**

- 不希望Python解释器的目录中充斥着你编写的模块。
- 没有必要的权限，无法将文件保存到Python解释器的目录中。
- 想将模块放在其他地方。

办法之一是直接修改sys.path，但这种做法不常见。标准做法是将模块所在的目录包含在环境变量`PYTHONPATH`中。

除使用环境变量`PYTHONPATH`外，还可使用路径配置文件。这些文件的扩展名为`.pth`，位于一些特殊目录中，包含要添加到`sys.path`中的目录。

#### 10.1.4 包

为组织模块，可将其编组为包(package)。模块存储在扩展名为.py的文件中，而包则是一个目录。要被Python视为包，目录必须包含文件`__init__.py`。

一种简单的包布局：

| 文件/目录                      | 描述                  |
| ------------------------------ | --------------------- |
| ~/python/                      | PYTHONPATH 中的目录   |
| ~/python/drawing/              | 包目录（包含drawing） |
| ~/python/drawing/`__init__.py` | 包代码（模块drawing） |
| ~/python/drawing/colors.py     | 模块 colors           |
| ~/python/drawing/shapes.py     | 模块 shapes           |

示例：

```python
import drawing # (1) 导入drawing包
import drawing.colors # (2) 导入drawing包中的模块colors
from drawing import shapes # (3) 导入模块shapes
```

### 10.2 探索模块

#### 10.2.1 模块包含什么

##### 1. 使用dir

```python
[n for n in dir(copy) if not n.startwith('_')]
# ['Error', 'PyStringMap', 'copy', 'deepcopy', 'dispatch_table', 'error', 'name', 't', 'weakref']
```

##### 2. 变量`__all__`



#### 10.2.2 使用help获取帮助

```python
help(copy.copy)
# Help on function copy in module copy:

#copy(x)
#    Shallow copy operation on arbitrary Python objects.
#    See the module's __doc__ string for more info.
```



#### 10.2.3 文档

文档是有关模块信息的自然来源。

```python
# 函数range的描述
print(range.__doc__)
# range(stop) -> list of integers
# range(start, stop[, step]) -> list of integers

# Return a list containing an arithmetic progression of integers.
# range(i, j) returns [i, i+1, i+2, ..., j-1]; start (!) defaults to 0.
# When step is given, it specifies the increment (or decrement).
# For example, range(4) returns [0, 1, 2, 3].  The end point is omitted!
# These are exactly the valid indices for a list of 4 elements.
```

#### 10.2.4 使用源代码

一种办法是像解释器那样通过sys.path来查找，但更快捷的方式是查看模块的特性`__file__`。

```python
print(copy.__file__)
# /usr/local/Cellar/python@2/2.7.15/Frameworks/Python.framework/Versions/2.7/lib/python2.7/copy.pyc
```

### 10.3 标准库：一些深受欢迎的模块

#### 10.3.1 sys

模块sys让你能够访问与Python解释器紧密相关的变量和函数。

```python
# reverseargs.py 
import sys
args = sys.argv[1:] # 注意到跳过了sys.argv 的第一个元素，即脚本的名称
args.reverse() 
print(' '.join(args))

```

#### 10.3.2 os

模块os让你能够访问多个操作系统服务。



**webbrowser**

函数`os.system`可用于完成很多任务，但就启动Web浏览器这项任务而言，有一种更佳的解决方案:使用模块`webbrowser`。

```python
import webbrowser 
webbrowser.open('http://www.python.org')

```



#### 10.3.3 fileinput

模块fileinput让你能够轻松地迭代一系列文本文件中的所有行。

```bash
$ python some_script.py file1.txt file2.txt file3.txt

# 如果使用模块fileinput，在UNIX管道中使用cat调用脚本的效果将与以命令行参数的方式向脚本提供文件名一样
$ cat file.txt | python some_script.py

```



#### 10.3.4 集合、堆和双端队列

##### 10.3.4.1 集合

```python
set(range(10))
# {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}

```



可使用序列(或其他可迭代对象)来创建集合，也可使用花括号显式地指定。请注意，不能仅使用花括号来创建空集合，因为这将创建一个空字典

```python
type({})
# <class 'dict'>

```



需要计算两个集合的并集的函数时，可使用set中方法union的未关联版本。

集合是可变的，因此不能用作字典中的键。另一个问题是，集合只能包含不可变(可散列)的值，因此不能包含其他集合。

```python
a = set()
b = set()
a.add(b)
# Traceback (most recent call last):
# File "<stdin>", line 1, in ? 
# TypeError: set objects are unhashable 
a.add(frozenset(b))

```

在需要将集合作为另一个集合的成员或字典中的键时，`frozenset`很有用。

##### 10.3.4.2 堆

Python 没有独立的堆类型，而只有一个包含一些堆操作函数的模块--`heapq`。

必须使用列表来表示堆对象本身。

它们虽然不是严格排序的，但必须保证一点:位置i处的元素总是大于位置i // 2处的元素(反过来说就是小于位置2 * i和2 * i + 1处的元素)。这是底层堆算法的基础，称为**堆特征(heap property)**。

| 函数                 | 描述                          |
| -------------------- | ----------------------------- |
| heappush(heap, x)    | 将x压入堆中                   |
| heappop(heap)        | 从堆中弹出最小的元素          |
| heapify(heap)        | 让列表具备堆特征              |
| heapreplace(heap, x) | 弹出最小的元素，并将x压入堆中 |
| nlargest(n, iter)    | 返回iter中n个最大的元素       |
| nsmallest(n, iter)   | 返回iter中n个最小的元素       |

##### 10.3.4.3 双端队列

在需要按添加元素的顺序进行删除时，双端队列很有用。在模块collections中，包含类型deque以及其他几个集合(collection)类型。

```python
>>> from collections import deque >>> q = deque(range(5))
>>> q.append(5)
>>> q.appendleft(6)
>>> q
deque([6, 0, 1, 2, 3, 4, 5]) >>> q.pop()
5
>>> q.popleft()
6
>>> q.rotate(3)
>>> q
deque([2, 3, 4, 0, 1])
>>> q.rotate(-1)
>>> q
deque([3, 4, 0, 1, 2])


```

#### 10.3.5 time

模块time包含用于获取当前时间、操作时间和日期、从字符串中读取日期、将日期格式化为字符串的函数。日期可表示为实数(从“新纪元”1月1日0时起过去的秒数。“新纪元”是一个随平台而异的年份，在UNIX中为1970年)，也可表示为包含9个整数的元组

| 函数                       | 描述                                      |
| -------------------------- | ----------------------------------------- |
| asctime([tuple])           | 将时间元组转换为字符串                    |
| localtime([secs])          | 将秒数转换为表示当地时间的日期元组        |
| mktime(tuple)              | 将时间元组转换为当地时间                  |
| sleep(secs)                | 休眠(什么都不做)secs秒                    |
| strptime(string[, format]) | 将字符串转换为时间元组                    |
| time()                     | 当前时间(从新纪元开始后的秒数，以UTC为准) |

另外，还有两个较新的与时间相关的模块:`datetime`和`timeit`。前者提供了日期和时间算术支持，而后者可帮助你计算代码段的执行时间。

#### 10.3.6 random

模块`random`包含生成伪随机数的函数，有助于编写模拟程序或生成随机输出的程序。

真正的随机应考虑使用模块`os`中的函数`urandom`。模块`random`中的`SystemRandom`类基于的功能与`urandom`类似，可提供接近于真正随机的数据。

| 函数                             | 描述                                         |
| -------------------------------- | -------------------------------------------- |
| random()                         | 返回一个0~1(含)的随机实数                    |
| getrandbits(n)                   | 以长整数方式返回n个随机的二进制位            |
| uniform(a, b)                    | 返回一个a~b(含)的随机实数                    |
| randrange([start], stop, [step]) | 从range(start, stop, step)中随机地选择一个数 |
| choice(seq)                      | 从序列seq中随机地选择一个元素                |
| shuffle(seq[, random])           | 就地打乱序列seq                              |
| sample(seq, n)                   | 从序列seq中随机地选择n个值不同的元素         |

编写与统计相关的程序时，可使用其他类似于`uniform`的函数，它们返回按各种分布随机采集的数字，如贝塔分布、指数分布、高斯分布等。

#### 10.3.7 shelve 和 json

1. **shelve 返回的对象并非普通映射**
   1. 临时变量
   2. 将函数`open`的参数`writeback`设置为`True`
2. Python 提供了用于处理` JSON` 字符串的模块` json`

#### 10.3.8 re

##### 10.3.8.1 正则表达式

可匹配文本片段的模式

1. 通配符

2. 对特殊字符进行转义（为表示模块`re`要求的单个反斜杠，需要在字符串中书写两个反斜杠`\\`，让解释器对其
   进行转义）

3. 字符集（字符集只能匹配一个字符）

   1. 要指定排除字符集，可在开头添加一个`^`字符

      > 一般而言，对于诸如句点、星号和问号等特殊字符，要在模式中将其用作字面字符而不
      > 是正则表达式运算符，必须使用反斜杠对其进行转义。在字符集中，通常无需对这些字符进
      > 行转义，但进行转义也是完全合法的。

4. 二选一和子模式

   ```python
   # 表示二选一的特殊字符：管道字符(|)
   'p(ython|erl)'
   # 圆括号内的这部分(子模式)
   
   ```

   

5. 可选模式和重复模式

   1. 通过在子模式后面加上问号，可将其指定为可选的，即可包含可不包含

      ```python
      r'(http://)?(www\.)?python\.org'
      # 等价于 
      '(http://)?(www\\.)?python\\.org'
      
      ```

   > 对句点进行了转义，以防它充当通配符。
   >
   > 1. 为减少所需的反斜杠数量，我使用了原始字符串。  每个可选的子模式都放在圆括号内。
   > 2. 每个可选的子模式都可以出现，也可以不出现。 
   >
   > 问号表示可选的子模式可出现一次，也可不出现。还有其他几个运算符用于表示子模式可重复多次。 
   >
   > 3. (pattern)*:pattern可重复0、1或多次。
   > 4. (pattern)+:pattern可重复1或多次。
   > 5. (pattern){m,n}:模式可重复m~n次。 

6. 字符串的开头`^`和末尾`$`

##### 10.3.8.2 模块 re  的内容

函数match在模式与字符串开头匹配时就返回True，而不要求模式与整个字符串匹配。如果要求与整个字符串匹配，需要在模式末尾加上一个美元符号。美元符号要求与字符串末尾匹配，从而将匹配检查延伸到整个字符串。



##### 10.3.8.3 匹配对象和编组



##### 10.3.8.4 替换中的组号和函数

**贪婪和非贪婪模式**

对于所有的重复运算符，都可在后面加上问号来将其指定为非贪婪的。

##### 10.3.8.5 找出发件人

```python
# find_sender.py
import fileinput, re
pat = re.compile('From: (.*) <.*?>$') 
for line in fileinput.input():
	m = pat.match(line)
	if m: print(m.group(1))
```



```python
import fileinput, re
pat = re.compile(r'[a-z\-\.]+@[a-z\-\.]+', re.IGNORECASE) 
addresses = set()
for line in fileinput.input():
	for address in pat.findall(line):
		addresses.add(address) 
for address in sorted(addresses):
	print address
```



##### 10.3.8.6 模板系统示例

模板(template)是一种文件，可在其中插入具体的值来得到最终的文本。

```python
import fileinput, re
# 中括号括起的字段匹配
field_pat = re.compile(r'\[(.+?)\]')
# 把变量收集到这里
scope = {}
# 调用re.sub
def replacement(match):
	code = match.group(1)
	try:
		# 如果字段为表达式就返回其结果
		return str(eval(code, scope))
	except SyntaxError:
		# 否则在当前作用域内执行该赋值语句
		# 并返回一个空字符串
		return ''
	
# 获取所有文本并合并成一个字符串：
lines = []
for line in fileinput.input():
	lines.append(line)
text = ''.join(lines)

# 替换所有与字段模式匹配的内容
print(field_pat.sub(replacement, text))
```



## 11. 文件

### 11.1 打开文件

函数`open`将文件名作为唯一必不可少的参数，并返回一个文件对象。

#### 11.1.1 文件模式

| 函数open 的参数mode的最常见取值 |                                        |
| ------------------------------- | -------------------------------------- |
| 值                              | 描述                                   |
| r                               | 读取模式（默认）                       |
| w                               | 写入模式                               |
| x                               | 独占写入模式                           |
| a                               | 附加模式                               |
| b                               | 二进制模式（与其他模式结合使用）       |
| t                               | 文本模式（默认值，与其他模式结合使用） |
| +                               | 读写模式（与其他模式结合使用）         |

### 11.2 文件的基本用法

| 三个标准流 |                                                              |
| ---------- | ------------------------------------------------------------ |
| sys.stdin  | 一个标准数据输入源                                           |
| sys.stdout | 数据通常出现在屏幕上，但可使用管道将其重定向到另一个程序的标准输入 |
| sys.stderr | 错误消息                                                     |

#### 11.2.1 读取和写入

```python
f.write()
f.read()

```

#### 11.2.2 使用管道重定向输出

管道将一个命令的 标准输出链接到下一个命令的标准输入 

##### 11.2.2.1 随机存取

**`seek` **

方法 `seek(offset[, whence])`将当前位置(执行读取或写入的位置)移到 `offset` 和 `whence` 指定的地方。参数` offset `指定了字节(字符)数，而参数` whence `默认为 `io.SEEK_SET(0)`，这意味着偏移量是相对于文件开头的(偏移量不能为负数)。参数` whence` 还可设置为` io.SEEK_CUR(1)`或 `io.SEEK_END(2)`，其中前者表示相对于当前位置进行移动(偏移量可以为负)，而后者表示相对于文件末尾进行移动。

**`tell`**

方法 `tell()`返回当前位于文件的什么位置



#### 11.2.3 读取和写入

要读取一行(从当前位置到下一个分行符的文本)，可使用方法`readline`。

要读取文件中的所有行，并以列表的方式返回它们，可使用方法readlines。



方法writelines与readlines相反:接受一个字符串列表(实际上，可以是任何序列或可迭代对象)，并将这些字符串都写入到文件(或流)中。请注意，写入时不会添加换行符，因此你必须自行添加。另外，没有方法writeline，因为可以使用write。

#### 11.2.4 关闭文件

别忘了调用方法close将文件关闭。

对于写入过的文件，一定要将其关闭，因为Python可能缓冲你写入的数据(将数据暂时存储在某个地方，以提高效率)。

`flush`刷新缓存



要确保文件得以关闭，可使用一条`try/finally`语句，并在`finally`子句中调用`close`。 

```python
# 在这里打开文件 
try: 
	# 将数据写入到文件中 
finally: 
    file.close()

```

实际上，有一条专门为此设计的语句，那就是`with`语句。 

```python
with open("somefile.txt") as somefile: 
	do_something(somefile) 
# with语句让你能够打开文件并将其赋给一个变量(这里是somefile)。

```

   

**上下文管理器**

上下文管理器是支持两个方法的对象:`__enter__`和`__exit__`。

```python
# 方法__enter__不接受任何参数，在进入with语句时被调用，其返回值被赋给关键字as后 面的变量。

# 方法__exit__接受三个参数:异常类型、异常对象和异常跟踪。它在离开方法时被调用 (通过前述参数将引发的异常提供给它)。如果__exit__返回False，将抑制所有的异常。

# 文件也可用作上下文管理器。它们的方法__enter__返回文件对象本身，而方法__exit__ 关闭文件。

```

#### 11.2.5 使用文件的基本用法

```
read(n)
read()
readline()
readlines()
write(string)
writelines(list)

```



### 11.3 迭代文件内容

在本节的所有示例中，我都将使用一个名为`process`的虚构函数来表示对每个字符或行所做的处理，你可以用自己的喜欢的方式实现这个函数

```python
def process(string): 
    print('Processing:', string)

```



#### 11.3.1 每次一个字符（或字节）

```python
with open(filename) as f: 
    char = f.read(1) 
    while char:
		process(char) 
        char = f.read(1)
# 到达文件末尾时，方法read将返回一个空字符串，但在此之前， 返回的字符串都只包含一个字符(对应于布尔值True)。
with open2(filename) as f:
    while true:
        char = f.read(1)
        if not char: break
        process(char)

```



#### 11.3.2 每次一行

```python
 with open(filename) as f:
         while True:
			line = f.readline() 
            if not line: break 
            process(line)

```

#### 11.3.3 读取所有内容

```python
with open(filename) as f:
	for char in f.read():
		process(char)

```



```python
with open(filename) as f:
	for line in f.readlines():
		process(line)

```



#### 11.3.4 使用 fileinput 实现延迟行迭代（它只读取实际需要的文本部分）

```python
import fileinput
for line in fileinput.input(filename):
	process(line)

```

#### 11.3.5 文件迭代器

文件实际上是可迭代的，这意味着可在for循环中直接使用它们来迭代行。

```python
with open(filename) as f:
	for line in f:
		process(line)

```



```python
# 在不将文件对象赋给变量的情况下迭代文件
for line in open(filename):
	process(line)

```



```python
import sys
# sys.stdin也是可迭代的
for line in sys.stdin:
	process(line)

```

> 另外，可对迭代器做的事情基本上都可对文件做，如(使用`list(open(filename)))`将其转换为字符串列表，其效果与使用`readlines`相同



## 12 图形用户界面

Tkinter是一个跨平台的Python GUI工具包，成熟而且使用广泛。

#### 12.1.2 布局

#### 12.1.3 事件处理



## 13 数据库支持

### 13.1 Python 数据库 API

#### 13.1.1 全局变量

所有与DB API2.0兼容的数据库模块都必须包含三个全局变量，它们描述了模块的特征。这样做的原因是，这个API设计得很灵活，无需进行太多包装就能配合多种不同的底层机制使用。

在很多情况下，一种更现实的做法是检查这些变量，看看给定的模块是否是程序能够接受的。如果不是，就显示合适的错误消息并退出或者引发异常

| 变量名       | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| apilevel     | 使用的 Python DB API 版本                                    |
| threadsafety | 模块的线程安全度如何；是一个 0~3的整数。0 表示线程补鞥呢共享模块；3表示模块是绝对线程安全的；1 表示线程可共享模块本身，但不能共享连接；2表示线程可贡献模块和连接，大不能共享游标。 |
| paramstyle   | 在SQL查询中使用哪种参数风格。'format';'pyformat';'qmark'表示使用问号;'numeric'表示使用:1和:2;'named'表示使用:foobar这样的形式表示字段。 |

#### 13.1.2 异常

Python DB API 指定的异常

| 异常              | 超类          | 描述                                   |
| ----------------- | ------------- | -------------------------------------- |
| StandardError     |               | 所有异常的超类                         |
| Warning           | StandardError | 发生非致命问题时引发                   |
| Error             | StandardError | 所有错误条件的超类                     |
| InterfaceError    | Error         | 与接口(而不是数据库)相关的错误         |
| DatabaseError     | Error         | 与数据库相关的错误的超类               |
| DataError         | DatabaseError | 与数据相关的问题，如值不在合法的范围内 |
| OperationalError  | DatabaseError | 数据库操作内部的错误                   |
| IntegrityError    | DatabaseError | 关系完整性遭到破坏，如键未通过检查     |
| InternalError     | DatabaseError | 数据库内部的错误，如游标无效           |
| ProgrammingError  | DatabaseError | 用户编程错误，如未找到数据库表         |
| NotSupportedError | DatabaseError | 请求不支持的功能，如回滚               |

#### 13.1.3 连接和游标

要使用底层的数据库系统，必须先连接到它，为此可使用名称贴切的函数connect。

| 函数 connect 的常用参数 |                                  |          |
| ----------------------- | -------------------------------- | -------- |
| 参数名                  | 描述                             | 是否可选 |
| dsn                     | 数据源名称，具体含义随数据库而异 | 否       |
| user                    | 用户名                           | 是       |
| password                | 用户密码                         | 是       |
| host                    | 主机名                           | 是       |
| database                | 数据库名称                       | 是       |

函数 connect 返回一连接对象，表示当前到数据库的会话。连接对象的方法如下：

| 方法名称   | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| close()    | 关闭连接对象。之后，连接对象及其游标将不可用                 |
| commit()   | 提交未提交的事务—如果支持的话；否则什么都不做                |
| rollback() | 回滚提交的事务(可能不可用—并非所有的数据库都支持事务)，可用时，这个方法撤销所有未提交的事务 |
| cursor()   | 返回连接的游标对象                                           |

说到方法 `cursor()` 就必须说说：游标对象。

| 名称                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| callproc(name[, params])   | 使用指定的参数调用指定的数据库过程(可选)                     |
| close()                    | 关闭游标。关闭后游标不可用                                   |
| execute(oper[, params])    | 执行一个 SQL 操作 — 可能指定参数                             |
| executemany(oper, pseq)    | 执行指定的SQL操作多次，每次都序列中的一组参数                |
| fetchone()                 | 以序列的方式取回查询结果中的下一行；如果没有更多的行，就返回 None |
| fetchmany([size])          | 取回查询结果中的多行，其中参数size的默认值认为arraysize      |
| fetchall()                 | 以序列的序列的方式取回余下的所有行                           |
| nextset()                  | 跳到下一个结果集，这个方法是可选的                           |
| setinputsizes(sizes)       | 用于为参数预定义内存区域                                     |
| setoutputsize(size[, col]) | 为取回大量数据而设置缓冲区长度                               |
|                            |                                                              |

游标对象的属性

| 名称        | 描述                          |
| ----------- | ----------------------------- |
| description | 由结果列描述组成的序列(只读)  |
| rowcount    | 结果包含的行数(只读)          |
| arraysize   | fetchmany返回的行数，默认为 1 |

#### 13.1.4 类型

| DB API 构造函数和特殊值         |                                                |
| ------------------------------- | ---------------------------------------------- |
| 名称                            | 描述                                           |
| 名称                            | 创建包含日期值的对象                           |
| Time(hour, minute, second)      | 创建包含时间值的对象                           |
| Timestamp(y, mon, d, h, min, s) | 创建包含时间戳的对象                           |
| DateFromTicks(ticks)            | 根据从新纪元开始过去的秒数创建包含日期值得对象 |
| TimeFromTicks(ticks)            | 根据从新纪元开始过去的秒数创建包含时间值得对象 |
| TimestampFromTicks(ticks)       | 根据从新纪元开始过去的秒数创建包含时间戳得对象 |
| Binary(string)                  | 创建包含二进制字符串值的对象                   |
| STRING                          | 苗书记与字符串的列                             |
| BINARY                          | 秒睡二进制列(如LONG或RAW)                      |
| NUMBER                          | 描述数字列                                     |
| DATETIME                        | 描述日期/时间列                                |
| ROWID                           | 描述行ID列                                     |

### 13.2 SQLite 和 PySQLite

```python
import sqlite3
# 创建直接到数据库文件的连接，只需要提供一个文件名(可以是文件的相对路径或绝对路径)
# 如果指定的文件不存在，将自动创建它
conn = sqlite3.connect('somedatabase.db')

# 从连接获得游标
curs = conn.cursor()
# 提交
conn.commit()
# 关闭连接
conn.close()

```



## 14 网络编程

#### 14.1.1 模块 socket (套接字)

套接字是模块socket中socket类的实例。实例化套接字时最多可指定3个参数：

- 地址族(默认为`socket.AF_INET`)

- 流套接字(`socket.SOCK_STREAM` 默认值)；数据报套接字(`socket.SOCK_DGRAM`)

- 协议(默认值 0)

  

服务器套接字先调用方法`bind`，在调用方法` listen`来监听指定的地址。然后，客户端套接字就可以连接到服务器了，办法是调用方法`connect`并提供调用方法`bind`时指定的地址(在服务器端，可使用函数`socket.gethostname`)。这里的地址是一个格式为`(host,port)`的元组，在其中host是主机名(如`www.example.com`)，port 是端口号。方法`listen`接收一个参数--待办任务清单的长度(即最多可有多少个连接在队列中等待接纳，达到这个数量后将开始拒绝连接)。

服务器套接字开始监听后，就可以接收客户端连接了，这是使用方法`accept`来完成的。这个方法将阻断(等待)到客户端连接到来为止，然后返回一个格式为`(client, address)`的元组，其中client是一个客户端套接字，而address是前面解释过的地址。服务器能以其认为合适的方式处理客户端连接，然后再次调用`accept`以接着等待新连接到来。

> 这里讨论的服务器编程形式称为阻断(同步)网络编程。在14.3节，你将看到非阻断(异步)网络编程示例，以及如何使用线程来同时处理多个客户端。

为传输数据，套接字提供了两个方法：`send`和`recv`。要发送数据，可调用方法`send`并提供一个字符串；要接收数据，可调用`recv`并制定最多接收多少个字节的数据。如果不确定该指定什么数字，1024是个不错的选择。

> 可使用的端口号通常受到限制。在Linux或UNIX系统中，需要有管理员权限才能使用1024以下的端口号。这些编号较小的端口是供标准服务使用的。例如，端口80供Web服务器使用。另外，使用Ctrl+C停止服务器后，可能需要等待一段时间才能使用该服务器原来使用的端口(否则，可能出现“地址已被占用”错误消息)。



```python
#! /usr/local/bin/python3
# server
import socket
s = socket.socket()

host = socket.gethostbyname("")
port = 1234
s.bind((host, port))

s.listen(5)

while True:
    c, addr = s.accept()
    print('Got connection from', addr)
    msg = 'Thank you for connecting'
    c.send(msg.encode())
    c.close()

```



```python
#! /usr/local/bin/python3
# client
import socket

s = socket.socket()

host = socket.gethostbyname("")
port = 1234

s.connect((host, port))
print(s.recv(1024))


```



#### 14.1.2 模块 urllib 和 urllib2

##### 1. 打开远程文件

```python
from urllib.request import urlopen
webpage = urlopen('http://www.python.org')

```



> 要在没有联网的情况下尝试使用模块urllib，可使用以`file:`打头的URL访问本地文件，如`file:c:\text\somefile.txt`(别忘了对反斜杠进行转义)。 

urlopen 返回的类思域文件的对象支持方法 close/read/readline/readlines，还支持迭代等。

##### 2. 获取远程文件

让 urllib 替你下载文件，并将其副本存储在一个本地文件中，可使用 urlretrieve。这个函数不返回一个类似于文件的对象，而返回一个格式为`(filename, headers)`的元组，其中 filename是本地文件的名称(由urllib自动创建)，而headers包含一些有关远程文件的信息。

```python
# 获取Python官网的主页，并将其存储到文件C:\python_webpage.html中
urlretrieve('http://www.python.org', 'C:\\python_webpage.html')

```

> urlcleanup 清理临时文件

| 一些使用函数               |                                                              |
| -------------------------- | ------------------------------------------------------------ |
| 名称                       | 描述                                                         |
| quote(string[, safe])      | 返回一个字符串，其中所有的特殊字符都已经替换为对URL友好的版本 |
| quote_plus(string[, safe]) | 类似于quote，但也将空格替换为加号                            |
| unquote(string)            | 与quote相反                                                  |
| unquote_plus(string)       | 与quote_plus 相反                                            |
| urlencode(query[, doseq])  | 将映射或由包含两个元素的元组组成的序列转换为"使用URL编码的"字符串。 |
|                            |                                                              |



#### 14.1.3 其他模块

| 标准库中与网络相关的模块 |                                |
| ------------------------ | ------------------------------ |
| asynchat                 | 包含补充 asyncore 的功能       |
| asyncore                 | 异步套接字处理程序             |
| cgi                      | 基本的CGI支持                  |
| cookie                   | Cookie对象操作，主要用于服务器 |
| cookielib                | 客户端cookie支持               |
| email                    | 电子邮件支持（包括MIME）支持   |
| ftplib                   | FTP客户端模块                  |
| gopherlib                | Gopher客户端模块               |
| httplib                  | HTTP客户端模块                 |
| imaplib                  | IMAP4客户端模块                |
| mailbox                  | 读取多种邮箱格式               |
| mailcap                  | 通过mailcap文件访问MIME配置    |
| mhlib                    | 访问MH邮箱                     |
| nntplib                  | NNTP客户端模块                 |
| poplib                   | POP客户端模块                  |
| robotparser              | 解析web服务器robot文件         |
| SimpleXMLRPCServer       | 一个简单的XML_RPC服务器        |
| smtpd                    | SMTP服务器模块                 |
| smtplib                  | SMTP客户端模块                 |
| telnetlib                | Telnet客户端模块               |
| urlparse                 | 用于解读url                    |
| xmlrpclib                | XML_RPC客户端支持              |



### 14.2 SocketServer 及相关的类

模块SocketServer是标准库提供的服务器框架的基石，这个框架包括`BaseHTTPServer`、`SimpleHTTPServer`、`CGIHTTPServer`、`SimpleXMLRPCServer`和`DocXMLRPCServer`等服务器，它们在基本服务器的基础上添加了各种功能。

SocketServer包含4个基本的服务器:

- TCPServer(支持TCP套接字流)
- UDPServer(支持UDP数据报套接字)
- UnixStreamServer
- UnixDatagramServer



```python
from socketserver import TCPServer, StreamRequestHandler
class Handler(StreamRequestHandler):
	def handle(self):
        addr = self.request.getpeername()
        print('Got connection from', addr) 
        self.wfile.write('Thank you for connecting')
server = TCPServer(('', 1234), Handler) 
server.serve_forever()

```



### 14.3 多个连接

处理多个连接的主要方式有三种:

- 分叉(forking)
- 线程化
- 异步I/O

通过结合使用 SocketServer 中的混合类和服务器类，很容易实现分叉和线程化。即便不使用这些类，这两种方式也很容易实现。

然而，它们确实存在缺点。分叉占用的资源较多， 且在客户端很多时可伸缩性不佳(但只要客户端数量适中，分叉在现代UNIX和Linux系统中的效率很高。如果系统有多个CPU，效率就更高了);而线程化可能带来同步问题。

> 分叉和线程
>
> 你可能不知道分叉和线程是什么，这里简单地说说。分叉是一个UNIX术语。对进程(运行的程序)进行分叉时，基本上是复制它，而这样得到的两个进程都将从当前位置开始继续往下执行，且每个进程都有自己的内存副本(变量等)。原来的进程为父进程，复制的进程为子进程。所幸进程能够判断它们是原始进程还是子进程(通常查看函数fork的返回值)，因此能够执行不同的操作。
>
> 
>
> 在分叉服务器中，对于每个客户端连接，都将通过分叉创建一个子进程。父进程继续监听新连接，而子进程负责处理客户端请求。客户端请求结束后，子进程直接退出。由于分叉出来的进程并行地运行，因此客户端无需等待
>
> 
>
> 鉴于分叉占用的资源较多(每个分叉出来的进程都必须有自己的内存)，还有另一种解决方案:线程化。线程是轻量级进程(子进程)，都位于同一个进程中并共享内存。这减少了占用的资源，但也带来了一个缺点:由于线程共享内存，你必须确保它们不会彼此干扰或同时修改同一项数据，否则将引起混乱。这些问题都属于同步问题。在现代操作系统(不支持分叉的Windows除外)中，分叉的速度其实非常快，较新的硬件能够更好地应付其资源消耗。如果你不想处理麻烦的同步问题，分叉可能是不错的选择。
>
> 
>
> 然而，如果能够完全杜绝并行性，就再好不过了。在本章中，将介绍基于函数select的其他解决方案。另一种避免线程和分叉的办法是使用Stackless Python`(http://stackless. com)`，它是一个能够快速而轻松地在不同上下文之间切换的Python版本。它支持一种类似于线程的并行方式，名为微线程，其可伸缩性比真正的线程高得多。



#### 14.3.1 使用SocketServer实现分叉和线程化



```python
# 分叉服务器
from socketserver import TCPServer, ForkingMixIn, StreamRequestHandler
class Server(ForkingMixIn, TCPServer): pass 
class Handler(StreamRequestHandler):
  
  def handle(self):
      addr = self.request.getpeername()
      print('Got connection from', addr)
      self.wfile.write('Thank you for connecting')
server = Server(('', 1234), Handler) 
server.serve_forever()

```



```python
# 线程化服务器
from socketserver import TCPServer, ThreadingMixIn, StreamRequestHandler
class Server(ThreadingMixIn, TCPServer): pass 
class Handler(StreamRequestHandler):
	def handle(self):
        addr = self.request.getpeername()
        print('Got connection from', addr) 
        self.wfile.write('Thank you for connecting')
    
server = Server(('', 1234), Handler)
server.serve_forever()

```



#### 14.3.2 使用select和poll实现异步I/O

当服务器与客户端通信时，来自客户端的数据可能时断时续。如果使用了分叉和线程化，这 就不是问题:因为一个进程(线程)等待数据时，其他进程(线程)可继续处理其客户端。然而， 另一种做法是只处理当前正在通信的客端。你甚至无需不断监听，只需监听后将客户端加入队 列即可。 

这就是框架asyncore/asynchat(参见第24章)和Twisted(参见14.4节)采取的方法。这种功 能的基石是函数select或poll(如果系统支持)。这两个函数都位于模块select中，其中poll的可 伸缩性更高，但只有UNIX系统支持(Windows不支持)。 



### 14.4 Twisted

这是一个事件驱动的Python网络框架，最初是为编写网络游戏开发的，但现被各种网络软件使用。在Twisted中，
你能实现事件处理程序，就像在GUI工具包(参见第12章)中一样。实际上，Twisted与多个常用的GUI工具包(Tk、GTK、Qt和wxWidgets)配合得天衣无缝。

Twisted是一个功能极其丰富的框架，支持Web服务器和客户端、SSH2、SMTP、POP3、IMAP4、AIM、ICQ、IRC、MSN、Jabber、NNTP、DNS等!



#### 14.4.2 编写 Twisted 服务器

本章前面编写的简单套接字服务器非常清晰，其中有些包含显式的事件循环，用于查找新连接和新数据。基于SocketServer的服务器有一个隐式的循环，用于查找连接并为每个连接创建处理程序，但处理程序必须显式地读取数据。Twisted(与第24章将讨论的框架asyncore/asynchat一样)采用的是基于事件的方法。要编写简单的服务器，只需实现处理如下情形的事件处理程序:客户端发起连接，有数据到来，客户端断开连接(以及众多其他的事件)。专用类可在基本类的基础上定义更细致的事件，如包装“数据到来”事件，收集换行符之前的所有数据再分派“数据行到来”事件。

> 延迟对象(deferred)和延迟执行(deferredexecution)



## 15 Python 和 Web

### 15.1 屏幕抓取

```python
from urllib.request import urlopen
import re
p = re.compile('<a href="(/jobs/\\d+)/">(.*?)</a>')
text = urlopen('http://python.org/jobs').read().decode() 
for url, name in p.findall(text):
	print('{} ({})'.format(name, url))

```

缺点：

- 正则表达式不容易理解，尤其是当HTML代码和查询更加复杂
- 不能处理独特的 HTML内容



#### 15.1.1 Tidy 和 XHTML 解析

标准库提供的通用的HTML解析方法是基于事件的：你编写事件处理程序，供解析程序处理 9 数据时调用。标准库模块html.parser让你能够以这种方式对极不严谨的HTML进行解析，但要基 于文档结构来提取数据(如第二个二级标题后面的第一项)，在存在标签缺失的情况下恐怕就只 能靠猜了。 

##### 1. Tidy

Tidy是用于对格式不正确且不严谨的HTML进行修复的工具。Tidy并不能修复HTML文件存在的所有问题，但确实能够确保文件是格式良好的(即所有元素都嵌套正确)，这让解析工作容易得多。

##### 2. 获取 Tidy

##### 3. 为何使用XHTML

##### 4. 使用 HTMLParser

使用HTMLParser意味着继承它，并重写各种事件处理方法，如handle_starttag和handle_data。



#### 15.1.2 Beautiful Soup

Beautiful Soup是一个小巧而出色的模块，用于解析你在Web上可能遇到的不严谨且格式糟糕的HTML



#### 15.2 使用CGI（通用网关接口）创建动态网页

1. 准备web服务器
2. 添加 `!#`行
3. 设置文件权限
4. CGI安全
5. 简单的CGI脚本
6. 使用cgitab调试
7. 使用模块cgi
8. 简单的表单

### 15.3 使用web框架

Flask:一个简单的Web框架，让你能够将代码作为Web应用发布，同时不用过多操心Web 部分。

### 15.4 web 服务：更高级的抓取

#### 15.4.1 RSS和相关内容

Mark Pilgrim开发的Universal Feed Parser

#### 15.4.2 使用XML-RPC进行远程过程调用

远程过程调用是对基本网络交互的抽象:客户端程序请求服务器程序执行计算并返回结果，但这个过程被伪装成简单的过程(函数或方法)调用。

XML-RPC可能是最简单的远程过程调用机制，它使用HTTP和XML来实现网络通信。

> 远程过程调用可与表述性状态转义式(REST)网络编程比肩，不过这两种机制有天壤之别。基于REST的(RESTful)程序也能让客户端以编程方式访问服务器，但服务器程序不能有任何隐藏的状态，返回什么样的数据完全由指定的URL(在HTTP POST中，是客户端提供的额外数据)决定。
>
> 在RESTful编程中，经常使用的一种协议是JavaScript对象表示法(JSON，http://www.json.org)，它简单而优雅，让你能够使用纯文本格式来表示复杂的对象。标准库模块json提供了对JSON格式的支持。

#### 15.4.3 SOAP

SOAP1也是一种将XML和HTTP用作底层技术的消息交换协议



## 16 测试基础

### 16.1 先测试再编码

**测试驱动的编程**

#### 16.1.1 准确的需求说明

测试程序就是需求说明，可帮助确保程序开发过程紧扣这些需求。

#### 16.1.2 做好应对变化的准备

> 代码覆盖率
>
> 覆盖率(coverage)是一个重要的测试概念。运行测试时，很可能达不到运行所有代码的理想状态。(实际上，最理想的情况是，使用各种可能的输入检查每种可能的程序状态，但这根本不可能做到。)优秀测试套件的目标之一是确保较高的覆盖率，为此可使用覆盖率工具，它们测量测试期间实际运行的代码所占的比例。



#### 16.1.3 测试四步曲

1. 确定需要实现的新功能
2. 编写实现功能的框架代码，让程序能够运行，但测试依然无法通过
3. 编写让测试刚好能够通过的代码
4. 改进代码以全面准确的实现所需功能，同时确保测试一样能够成功



### 16.2 测试工具

#### 16.2.1 doctest

```python
def square(x): 
  '''
  计算平方并返回结果
  >>> square(2) 4
  >>> square(3) 9
	'''
	return x * x

```

> 如果这里编写的是真实函数，我将(或者说应该)根据前面制定的规则先编写文档字符串，再使用doctest运行脚本看看测试是否会失败，然后添加刚好让测试得以通过的代码(如使用测试语句来处理文档字符串中的具体输入)，接下来确保实现是正确的。



#### 16.2.2 unittest

```python
import unittest, my_math
class ProductTestCase(unittest.TestCase):
  def test_integers(self):
  	for x in range(-10, 10):
  		for y in range(-10, 10):
  			p = my_math.product(x, y)
  			self.assertEqual(p, x * y, 'Integer multiplication failed')
        
  def test_floats(self): 
    for x in range(-10, 10):
      for y in range(-10, 10):
        x = x / 10
        y = y / 10
        p = my_math.product(x, y)
        self.assertEqual(p, x * y, 'Float multiplication failed')
if __name__ == '__main__': unittest.main()

```

函数`unittest.main`负责替你运行测试:实例化所有的`TestCase`子类，并运行所有名称以`test` 打头的方法。 

模块unittest区分**错误**和**失败**



### 16.3 超越单元测试

源代码检查和性能分析

#### 16.3.1 使用PyChecker 和  PyLint 检查源代码

要使用 PyChecker 来检查文件，可运行这个脚本并将文件名作为参数。

使用 PyLint 检查文件时，需要将模块(或包)名作为参数。



PyChecker和PyLint都可作为模块(分别是pychecker.checker和pylint.lint)导入，但它们 并不是为了以编程方式使用而设计的。 

我建议不去解决这些问题，就以原本的方式使用PyChecker和PyLint，即将其作为命令行工具使用。在Python中，可通过模块subprocess来使用命令行工具。

```python
import unittest, my_math
from subprocess import Popen, PIPE
class ProductTestCase(unittest.TestCase):
  #在这里插入以前的测试
  def test_with_PyChecker(self):
  	cmd = 'pychecker', '-Q', my_math.__file__.rstrip('c') 
  	pychecker = Popen(cmd, stdout=PIPE, stderr=PIPE)
    self.assertEqual(pychecker.stdout.read(), '')
    
  def test_with_PyLint(self):
  	cmd = 'pylint', '-rn', 'my_math'
  	pylint = Popen(cmd, stdout=PIPE, stderr=PIPE)
    self.assertEqual(pylint.stdout.read(), '')
    
if __name__ == '__main__': unittest.main()

```

调用检查器脚本时，我指定了一些命令行开关，以免无关的输出干扰测试。对于pychecker，我指定了开关`-Q`(quiet，意为静默);对于pylint，我指定了开关`-rn`(其中n表示no)以关闭报告，这意味着将只显示警告和错误。



因此，即便有PyChecker和PyLint这样出色的工具，依然需要依赖于手工调试，而这要求我们知道程序的特殊之处。

#### 16.3.2 性能分析

标准库包含一个卓越的性能分析模块profile，还有一个速度更快C语言版本，名为cProfile。

> 标准库还包含一个名为timeit的模块，提供了一种对小段Python代码的运行时间进行测试 的简单方式。在进行详尽的性能分析方面，模块timeit的用处不大，但在只需确定一段 代码花了多长时间才执行完毕时，这是一个很不错的工具。手工测量的结果通常不准确 (除非你对这方面了如指掌)，因此使用timeit通常是更好的选择。 



## 17 扩展 Python

### 17.1 鱼和熊掌兼得

1. 使用Python开发原型
2. 对程序进行性能分析找出瓶颈
3. 使用 C(或者C++、C#、Java等)扩展重写瓶颈部分

这样得到的架构(包含一个或多个C语言组件的Python框架)将非常强大，因为它兼具这两 门语言的优点。关键在于选择正确的工具来完成每项任务，这样既能获得使用高级语言(Python) 开发复杂系统的好处，又能使用低级语言(C)来开发较小(还可能较简单)但速度至关重要的 组件 

但别忘了，下面的做法也有用武之地:使用编译型语言编写程序，并在其中嵌入Python解释器来执行少量的脚本和扩展。在这种情况下，嵌入Python追求的不是速度而是灵活性。从很多方面说，这与编写编译型扩展的目的是一样的，也是为了鱼和熊掌兼得，只是重点不同。

### 17.2 简单易行的方式：Jython 和 IronPython

```python
public class JythonTest {
  public void greeting() {
    System.out.println("Hello, world!");
  }
}

# javac JythonTest.java
```

> 如果你使用Java进行开发，也可使用命令jythonc将Python类编译成Java类，然后就可将其导入到Java程序中。

```python
CLASSPATH=JythonTest.class jython
import JythonTest
test=JythonTest()
test.greeting()

```

> 在与Java类交互方面，Jython有几把刷子。其中最有用的功能是，让你能够像访问普通属性一样访问JavaBean属性。
>
> 不用像下面这样做: 
>
> b = awt.Button()
>
> b.setEnabled(False) 
>
> 而可这样做: 
>
> b = awt.Button() 
>
> b.enabled = False 
>
> 实际上，所有属性也都可在构造函数中通过关键字参数来设置。因此可像下面这样做:
>
> b = awt.Button(enabled=False) 
>
> 这适用于表示多个参数的元组，也适用于Java成例(如事件监听器)的函数参数。 
>
> def exit(event): 
>
> ​	java.lang.System.exit(0) 
>
> b = awt.Button("Close Me!", actionPerformed=exit) 
>
> 在Java中，必须实现一个包含方法actionPerformed的类，再使用b.addActionListener来 添加这个类的实例。 



### 17.3 编写 C 语言扩展

使用C语言编写Python扩展时，必须遵循严格的API。

以下工具可帮助提高程序的速度

- Cython
- PyPy
- Weave
- NumPy
- ctypes
- subprocess
- PyCXX
- SIP
- Boost.Python

#### 17.3.1 SWIG

是简单包装器和接口生成器，是一个适用于多种语言的工具。

#### 17.3.2 手工编写扩展

可手工编写可作为共享库直接导入到Python中的C语言代码。为此， 必须遵循Python/C API:对于每个函数，你都需要负责完成引用计数、提取参数以及创建 返回值等工作;另外，还需编写将C语言库转换为模块的代码，包括列出模块中的函数以 及创建模块初始化函数。 



## 18 程序打包

### 18.1 Setuptools

工具包能够编写安装脚本。根据约定，这种安装脚本被铭铭为 setup.py。使用这种脚本，可安装模块、包和扩展。

#### 18.2 Setuptools 的命令

可使用多个命令来运行setup.py脚本，如build、build_ext、install、
sdist和bdist。

#### 18.3 编译扩展

可使用Setuptools 来自动编译C语言扩展，并让 Setuptools自动确定Python安装包位置以及该使用哪个编译器。还可让他自动运行 SWIG

#### 18.4 可执行的二进制文件

Setuptools扩展py2exe可用来从Python程序创建可执行的Windows二进制文件以及其他一些文件(可使用安装程序方便地安装)。无需单独安装Python解释器，就可运行这些.exe文件。在macOS中，扩展py2app提供了与py2exe类似的功能。



## 19 趣味编程

### 19.3 原型设计

原型(prototype)指的是尝试性实现，即一个模型。它实现了最终程序的主要功能，但在后期可能需要重写，也可能不用重写。通常，最初的原型都能变成可行的程序

### 19.4 配置

**通过提取程序中的常量，可让以后修改程序变得更容易。通过将这些常量放在配 置文件中，让用户能够配置程序，使其按自己希望的方式行事。通过使用环境变量和命 令行选项，可进一步提高程序的可配置性。 **

#### 19.4.1 提取常量

要指出变量被视为符号常量，可遵循一种特殊的命名约定:只在变量名中使用大写字母并用下划线分隔单词。

#### 19.4.2 配置文件

使用配置文件有利有弊。一方面，配置很有用;但另一方面，使用针对整个项目的中央
共享变量库可能降低项目的模块化程度(即增大耦合程度)。因此，使用配置文件时，务
必不要破坏抽象(如封装)。

> 配置的级别
>
> - 配置文件:参见19.4.1节。
> - 环境变量:可使用字典`os.environ`来获取它们。
> - 在命令行中向程序传递的开关和参数:要处理命令行参数，可直接使`sys.argv;`要 处理开关(选项)，应使用第10章提到的模块`argparse`。 



### 19.5 日志

日志大致上就是收集与程序运行相关的数据，供你事后进行研究或积累。print语句是一种简单的日志形式。要使用这种日志形式，只需在程序开头包含一条类似于下面的语句:

```python
log = open('logfile.txt', 'w')
print('Downloading file from URL', url, file=log) 
text = urllib.urlopen(url).read()
print'File successfully downloaded', file=log)
```

如果程序在下载期间崩溃，这种方法的效果就不会很好。更安全的做法是，在每条日志语句前后都打开和关闭文件(至少应该在写入后刷新文件)。这样，即便程序崩溃，也将看到日志文件的最后一行为`“Downloading file from URL”`，从而知道下载失败了。

- 记录不同类型的条目(信息、调试信息、警告、自定义类型等)。默认情况下，只记录警 告。(这就是我在代码清单19-3中显式地将level设置为logging.INFO的原因所在。) 
- 只记录与程序特定部分相关的条目。 
- 记录有关时间、日期等方面的信息。 
- 记录到其他位置，如套接字。 
- 配置日志器，将一些或大部分日志过滤掉，这样无需重写程序就能获得所需的日志信息。 

模块logging非常复杂，文档中还提供了其他很多相关的信息。 

**日志对找出程序存在的问题或监视其行为大有裨益。你可自己动手使用print语句
实现简单的日志，但最安全的做法是使用标准库中的模块logging。**



### 补充

```python
#!/usr/bin/python
'''
是告诉操作系统执行这个脚本的时候，调用/usr/bin下的python解释器；
'''


#!/usr/bin/env python
'''
这种用法是为了防止操作系统用户没有将python装在默认的/usr/bin路径里。当系统看到这一行的时候，首先会到env设置里查找python的安装路径，再调用对应路径下的解释器程序完成操作。
'''
```

- `#!/usr/bin/python` 相当于写死了python路径
- `#!/usr/bin/env python` 会去环境设置寻找python目录，推荐这种写法







