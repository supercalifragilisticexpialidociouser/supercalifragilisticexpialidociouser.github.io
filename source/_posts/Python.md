---
title: Python
date: 2018-09-15 12:15:06
tags: [3.7.0]
---

# 简介

# 开发环境

## 安装

## 交互式环境

安装完Python后，执行命令`python`将进入交互式环境。如果同时安装了版本2和3的Python，则命令`python`通常是指Python 3，另外还有命令`python2`和`python3`。

```bash
$ python
>>> print("Hello, world!")
```

退出交互式环境使用函数`quit()`或快捷键`Ctrl-D`。

另外，官网上也提供了一个在线的交互式环境：https://www.python.org/shell/

### 获取帮助

如果你需要有关 Python 中任何函数或语句的快速信息，那么你可以使用内置的 `help()` 函数。

```python
>>> help('return')
```

> 提示：按下 `q` 退出帮助。

## IDE

[PyCharm](http://www.jetbrains.com/pycharm/)

# 入门

## 编码

### 第一个程序

hello.py：

```python
print("Hello world!")
```

### 源文件

Python程序的源代码保存在扩展名是`.py`的源文件中。

### 字符集

Python 2默认采用US-ASCII字符集，Python 3默认采用UTF-8字符集。

如果源文件要使用其他编码，可使用特殊的注释来指定：

```python
# -*- coding: latin-1 -*-
```



### 转义序列

### 编码规范

## 构建

## 运行

### 直接运行脚本

```bash
$ python hello.py
```

### shebang方式运行脚本

首先，要在`hello.py`的第一行添加`#!/usr/bin/env python`。

然后，赋予脚本可执行的权限：`chmod a+x hello.py`。

```bash
$ ./hello.py
```



# 程序结构

## 保留字

## 注释

| 注释                       | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `# …`                      | 单行注释。可嵌套。                                           |
| `''' … '''` 或 `""" … """` | 块注释。可跨多行，但不可以嵌套。（块注释实际上是Python中的长字符串） |

# 基本类型

## 数值类型

### 整数类型

```python
81    #十进制整数
0xAF  #十六进制整数
0o17  #八进制整数
0b1011 #二进制整数
```

Python的整数类型是：`int`。

### 浮点类型

Python的浮点类型是：`float`。

### 复数类型

Python本身提供了对复数的支持，复数类型是`complex`。

虚数都以`j`或`J`结尾。

Python没有专门的虚数类型，虚数可视为实部为0的复数。

```python
>>> (1 + 3j) * (9 + 4j)
(-3 + 31j)
```

另外通过`cmath`标准库专门用于处理复数：

```python
>>> import cmath
>>> cmath.sqrt(-1)    #注意：math.sqrt()不能用于负数
1j
```

## 字符类型

Python没有专门用于表示字符的类型，一个字符就是只包含一个元素的字符串。

## 布尔类型

布尔类型`bool`它有两个值：`True`和`False`。

## 空类型

类`NoneType`表示空类型，它只有一个值`None`，表示什么都没有。

## 获取类型信息

```python
>>> type(3.14)
<class 'float'>
```

# 声明

Python变量不需要声明。

Python变量实际上是没有类型的，而对象是有类型：

```python
>>> a = 1
>>> a = 'ok'
```



# 数组

# 字符串

字符串也是不可修改的序列（`bytearray`例外）。

字符串的类型是`str`。

## 字符串字面量

### 常规字符串

Python的常规字符串使用单引号或双引号括起来。

```python
"Let's go!"
'"Hello world!" she said'
```

### 长字符串

长字符串可以跨越多行，并且可以包含单引号和双引号，而无需转义：

```python
''''Hello world!'
she said'''
或者
""""Hello world"
she said"""
```

> 其实常规字符串也可以跨多行，只要在行尾加上反斜杠，这样反斜杠和换行符将被转义，即被忽略：
>
> ```python
> "Hello, \
> world!"
> ```

### 原始字符串

原始字符串以`r`为前缀，它不处理转义序列：

```python
r'C:\nowhere\foo\bar'
r"C:\nowhere\foo\bar"

r'''C:\nowhere
\foo\bar'''

r"""C:\nowhere
\foo\bar"""
```

一个例外是，原始字符串中的引号需要转义，并且执行转义的反斜杠也将包含在最终的字符串中：

```python
>>> print(r'Let\'s go!')
Let\'s go!
```

另外，原始字符串的最后一个字符不能是反斜杠，除非对其进行转义。如果确实需要以反斜杠结尾，建议将反斜杠单独作为一个字符串：

```python
>>> print(r'C:\Program Files\foo\bar' '\\')
C:\Program Files\foo\bar\
```

### bytes

`bytes`是一个不可变的字节串：

```python
b'Hello world!'
b"Hello world!"

b'''Hello
world!'''

b"""Hello
world!"""
```

将字符串编码为`bytes`：

```python
>>> "Hello, world!".encode()     #默认使用UTF-8编码
b'Hello, world!'
>>> "Hello, world!".encode("UTF-32")
b'xff\xfe\x00\x00H'\x00\x00\x00e\x00\x00\x00l\x00\x00\x00l\x00\x00\x00o\x00\x00\x00,\x00\x00\x00 \x00\x00\x00w\x00\x00\x00o\x00\x00\x00r\x00\x00\x00l\x00\x00\x00d\x00\x00\x00!\x00\x00\x00'
```

### bytearray

`bytearray`是`bytes`的可变版本。

```python
>>> x = bytearray(b"Hello!")
>>> x[1] = ord(b"u")    #必须使用要插入的字符的序数值（0~255）
>>> x
bytearray(b'Hullo!')
```



## 构造字符串

类`str`和函数`repr`可以构造常规字符串：

```python
>>> repr("Hello, \nworld!")
"'Hello, \\nworld'"
>>> str("Hello, \nworld")
'Hello, \nworld'
>>> str(b"Hello world!", encoding="utf-8")
'Hello world!'
```

构造`bytes`：

```python
>>> bytes("Hello world!", encoding="utf-8")
b'Hello world!'
```

## 使用字符串

### 拼接

```python
s1 = "Hello " + "world!"
s2 = "Hello " 'world!'  #连接输入两个字符串字面量，将会自动拼接成一个字符串
```

### 格式化

Python字符串格式设置方法主要有：格式字符串、模板字符串、format方法。

#### 格式字符串

```python
>>> format = 'Hello, %s. %s enough for ya?'
>>> values = ('world', 'Hot')
>>> format % values
'Hello, world. Hot enough for ya?'
```

格式字符串中的`%s`称为转换说明符，类似C语言的`printf`函数。

`%`运算符是字符串格式设置运算符，它的左操作数是格式字符串，右操作数是要设置格式的值。

#### 模板字符串

```python
>>> from string import Template
>>> tmpl = Template("Hello, $who! $what enough for ya?")
>>> tmpl.substitute(who="Mars", what="Dusty")
'Hello, Mars! Dusty enough for ya?'
```

#### format方法

`format`方法可以将字符串中的插值（Interpolation）替换成方法的参数。

插值用花括号括起来。如果要在最终结果中包含花括号字符，则使用两个花括号进行转义（即`{{`和`}}`）。

插值由三部分组成，其中每个部分都是可选的：

- 字段名：索引或标识符。
- 转换标志：以`!`开头，后跟`r`（表示repr）、`s`（表示str）或`a`（表示ascii）。
- 格式说明符：以`:`开头的表达式，用于详细设置最终的格式。

按顺序配对：

```python
>>> '{}, {} and {}'.format('first', 'second', 'third')
'first, second and third'
```

按索引配对：

```python
>>> '{2}, {0} and {1}'.format('first', 'second', 'third')
'third, first and second'
```

按名字配对：

```python
>>> from math import pi
>>> '{name} is approximately {value:.2f}.'.format(value=pi, name='π')
'π is approximately 3.14'
```

f字符串：

```python
>>> from math import e
>>> "Euler's constant is roughly {e}.".format(e=e)
#上面变量名与字段名相同时，可以使用f字符串进行简写：
>>> f"Euler's constant is roughly {e}."
'Euler's constant is roughly 2.718281828459045.'
```

可以混合使用按顺序配对和按名字配对，也可以混合使用按索引配对和按名字配对，但不能混合使用按顺序配对和按索引配对：

```python
>>> "{foo} {} {bar} {}".format(1, 2, bar=4, foo=3)
'3 1 4 2'
>>> "{foo} {1} {bar} {0}".format(1, 2, bar=4, foo=3)
'3 2 4 1'
```



# 枚举类型

# 表达式

## 算术表达式

乘方的优先级高于正负号：

```python
-3 ** 2  #-9
(-3) ** 2  #9
```

### 除法

浮点除`/`：

```python
1 / 2  #0.5
```

整除`//`：

```python
10 // 3  #3
10 // -3  #-4
-10 // 3  #-4
-10 // -3 #3
5.0 // 2.4  #2.0
```

Python中整除采取向下取整的策略。

> Python 2中，浮点除的效果与整除是一样的，要获取Python 3中的浮点除功能，只要在程序开头添加如下语句：
>
> ```python
> from __future__ import division
> ```

`math.ceil(数值)`：以浮点数的方式返回向上取整的结果。

`math.floor(数值)`：以浮点数的方式返回向下取整的结果。

`round(数值[, 保留小数位数])`：四舍五入为指定的精度，正好为5时舍入到偶数。（实际上是对二进制数进行舍入）

```python
>>> import math
>>> math.floor(32.9)
32
>>> math.floor(-32.3)
-33
>>> math.ceil(32.3)
33
>>> math.ceil(-32.9)
-32
>>> round(3.14)
3
>>> round(3.14159, 2)
3.14
>>> round(3.15, 1)
3.1
>>> round(3.225, 2)
3.23
>>> round(3.5)
4
>>> round(4.5)
4
```



### 求模

```python
10 % 3  #1
10 % -3 #-2
-10 % 3 #2
-10 % -3 #-1
```

`x % y`等价于`x - ((x // y) * y)`。

# 语句

## 语句分隔符

`;`在Python中只是语句分隔符，除了要将多条语句放在一行外，通常不需要语句分隔符。

换行符也可以作为Python的语句分隔符。

## 赋值语句

虽然Python的变量不需要声明，但它没有默认值，在使用之前必须先给它赋值。

```python
x = 3
a, b = 1, 3
a, b = b, a  #交换值
```



# 子程序

# 集合类型

## 序列

序列（Sequence）中的每个元素都有编号（或索引），其中第一个元素的索引为0。

Python中的序列有：列表、元组和字符串。

### 通用操作

#### 索引

```python
>>> 'Hello'[0]
'H'
>>> 'Hello'[-1]
'o'

>>> fourth = input('Year: ')[3]
Year: 2005
>>> fourth
'5'
```

负数索引表示从最后一个元素开始往左数。

#### 切片

切片（Slicing）可用来访问序列中特定范围内的元素。

```python
>>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> numbers[3:6]
[4, 5, 6]
```

冒号左边索引指定的元素包含在切片内，但冒号右边索引指定的元素不包含在切片内。

冒号右边索引可以超出序列索引的范围，例如：取出从第八个元素开始到序列结尾的所有元素：

```python
>>> numbers[7:12]
[8, 9, 10]
```

取出最后三个元素：

```python
>>> numbers[-3:]
[8, 9, 10]
>>> numbers[-3:-1]
[8, 9]
>>> numbers[-3:0]
[]
```

取出前三个元素：

```python
>>> numbers[:3]
[1, 2, 3]
```

复制整个序列：

```python
>>> numbers[:]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

取出从第三个元素到倒数第三个元素的切片：

```python
>>> numbers[2:-2]
[3, 4, 5, 6, 7, 8]
```

指定步长：

```python
>>> numbers[:10:2]
[1, 3, 5, 7, 9]
>>> numbers[::-2]    #步长不能为0，但可以为负数，即从右向左提取元素
[10, 8, 6, 4, 2]
>>> numbers[8:3:-1]
[9, 8, 7, 6, 5]
>>> numbers[5::-2]
[6, 4, 2]
```

#### 拼接

```python
>>> [1, 2, 3] + [4, 5]
[1, 2, 3, 4, 5]
```

#### 重复

```python
>>> '#' * 5
'#####'
>>> 3 * [0]
[0, 0, 0]
```

#### in

```python
>>> users = ['mlh', 'foo', 'bar']
>>> input("Enter your user name: ") in users
Enter your user name: mlh
True
```

通常`in`运算符只能检查指定一个元素（本身也可以是另一个序列）是否属于某个序列。但对于字符串，还可以检查字符串是否包含指定子串：

```python
>>> 'P' in 'Python'
True
>>> 'foo' in 'abcfoobar'
True
```

```python
db = [['tom', '123'], ['kate', '2323']]
username = input('User name: ')
pin = input("PIN code: ")
if [username, pin] in db: print('Access granted')
```

#### 元素个数

```python
>>> len(numbers)
10
```

#### 最大元素和最小元素

```python
>>> max(numbers)
10
>>> min(numbers)
1
>>> min(9, 3, 2, 5)
2
```



### 列表

列表是可以修改的序列。

#### 列表字面量

```python
[1, 2, 'ok']
```

#### 构造列表

```python
>>> list('Hello')
['H', 'e', 'l', 'l', 'o']
```

> 要将字符序列转换为字符串，可使用`.join()`方法。

#### 使用列表

```python
>>> names = ['Alice', 'Beth', 'Tom', 'Mary', 'Rose']

#给元素赋值（注意：不能给不存在的元素赋值，例如，names[100]='Eric'）
>>> names[1] = 'Kate'
>>> names
['Alice', 'Kate', 'Tom', 'Mary', 'Rose']

#删除指定索引处的元素
>>> del names[2]
>>> names
['Alice', 'Kate', 'Mary', 'Rose']

#弹出元素并返回这个非None元素
>>> a = [1, 2, 3]
>>> a.pop()  #弹出末尾元素
3
>>> a
[1, 2]
>>> a.pop(0) #弹出指定索引处的元素
1
>>> a
[2]

#删除列表中第一个为指定值的元素,如果列表中不存在指定值则引发异常
>>> x = ['to', 'be', 'or', 'not', 'to', 'be']
>>> x.remove('be')
>>> x
['to', 'or', 'not', 'to', 'be']

#给切片赋值
>>> name = list('Perl')
>>> name
['P', 'e', 'r', 'l']
>>> name[1:] = list('ython')  #可将切片替换为长度与其不同的序列。
>>> name
['P', 'y', 't', 'h', 'o', 'n']
# 使用切片赋值还可在不替换原有元素的情况下插入新元素：
>>> numbers = [1, 5]
>>> numbers[1:1] = [2, 3, 4] #替换了一个空切片，相当于插入
>>> numbers
[1, 2, 3, 4, 5]
>>> numbers[1:4] = []  #给切片赋空序列，相当于删除切片，即：del numbers[1:4]
>>> numbers
[1, 5]

#将一个对象附加到列表末尾
>>> lst = [1, 2, 3]
>>> lst.append(4)
>>> lst
[1, 2, 3, 4]

#清空列表
>>> lst.clear()
>>> lst
[]

#扩展列表
>>> a = [1, 2, 3]
>>> b = [4, 5, 6]
>>> a.extend(b)   #也可以是：a[len(a):] = b
>>> a  #扩展会直接修改被扩展的列表，而拼接则是返回一个全新列表
[1, 2, 3, 4, 5, 6]

#在指定索引前插入元素
>>> a = [1, 2, 3]
>>> a.insert(2, 'four')  #相当于：a[2:2] = ['four']
>>> a
[1, 2, 'four', 3]

#复制列表
>>> a = [1, 2, 3]
>>> b = a.copy()  #也可以使用：b = a[:] 或 b = list(a)
>>> b[1] =4
>>> a
[1, 2, 3]
#只是将a赋值给b，并不是复制，这时a和b指向同一个列表：
>>> a = [1, 2, 3]
>>> b = a
>>> b[1] =4
>>> a
[1, 4, 3]

#统计元素在列表中出现的次数
>>> ['to', 'be', 'or', 'not', 'to', 'be'].count('to')
2
>>> x = [[1, 2], 1, 1, [2, 1, [1, 2]]]
>>> x.count(1)
2
>>> x.count([1, 2])
1

#查找指定值在列表中第一次出现的索引
>>> knights = ['We', 'are', 'the', 'knights', 'who', 'say', 'ni']
>>> knights.index('who')
4
#如果在列表中没有找到指定值，将引发异常

#反转列表
>>> a = [1, 7, 3]
>>> a.reverse()
>>> a
[3, 7, 1]
#如果希望按相反顺序迭代列表，则使用reversed函数。它会一个迭代器。
>>> b = [1, 2, 3]
>>> list(reversed(b))  #list可以将迭代器转换成列表
>>> b
[3, 2, 1]

#排序
>>> x = [4, 6, 2, 1, 7, 9]
>>> x.sort()  #按升序排序
>>> x
[1, 2, 4, 6, 7, 9]
#不修改原列表，而是返回一个新的已排序列表，使用sorted函数
>>> x = [4, 6, 2, 1, 7, 9]
>>> y = sorted(x)
>>> x
[4, 6, 2, 1, 7, 9]
>>> y
[1, 2, 4, 6, 7, 9]
#sort方法和sorted函数都可以接受两个可选参数：key和reverse。
#key参数是一个函数，用它来为每个元素生成一个键，再根据这些键对元素进行排序：
>>> s = ['aardvade', 'abdlef', 'acme', 'add', 'aegie']
>>> s.sort(key=len)
>>> s
['add', 'acme', 'aegie', 'abdlef', 'aardvade']
#reverse是一个布尔值，指示是否对列表进行倒序排列：
>>> x = [4, 6, 2, 1, 7, 9]
>>> x.sort(reverse=True)
>>> x
[9, 7, 6, 4, 2, 1]
```



### 元组

元组是不可以修改的序列。

#### 元组字面量

```python
>>> 1, 2, 3  #圆括号是可选的
(1, 2, 3)
>>> ()  #空元组
()
>>> 42,  #单个元素的元组，逗号不能省略
(42,)
```

#### 构造元组

```python
>>> tuple([1, 2, 3])
(1, 2, 3)
>>> tuple('abc')
('a', 'b', 'c')
```

#### 使用元组

```python
>>> x = 1, 2, 3
>>> x[0:2]
(1, 2)
```

元组的切片也是元组，就像列表的切片也是列表一样。

元组可以作为映射中的键，而列表不行。因为，映射的键必须是不可修改的。

## 映射

## 集

# 输入和输出

## 标准输出

Python 2使用`print`语句进行标准输出：

```python
print "Hello"
```

Python 3使用`print()`函数进行标准输出：

```python
print(2 * 2)
```

## 标准输入

```python
x = input("x: ")  #input函数的返回值是一个字符串
```



# 异常处理

# 图形用户界面

# 数据库支持

# 网络编程

# 模块

## 导入模块

```python
>>> import math
>>> math.floor(32.9)
```

如果不想每次调用模块中的函数都要指定模块名前缀，则可以使用`import`的变种：

```python
>>> from math import sqrt
>>> sqrt(9)
```

如果存在命名冲突，则不能使用这种变种进行导入。

## `__future__`模块

`__future__`是一个特殊模块，它用于存放当前Python不支持，但未来将成为标准组成部分的功能。

# 构建管理