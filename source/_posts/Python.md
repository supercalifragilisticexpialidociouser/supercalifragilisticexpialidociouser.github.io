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

### 文档字符串

Python实际上是没有块注释的，上面的块注释实际上是文档字符串（Docstring），它可以出现在`def`语句之后、模块和类的开头。并且任何字符串都可以作为文档字符串，而不仅仅是长字符串。

```python
def square(x):
  'Calculates the square of the number x.'
  return x * x
```

可以象下面这样访问文档字符串：

```python
>>> square.__doc__
'Calculates the square of the number x.'
```

另外，使用`help`函数获取帮助时，也包含了函数的文档字符串：

```python
>>> help(square)
Help on function square in module __main__:
  
square(x)
Calculates the square of the number x.
```



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

获取字符的码点：

```python
>>> ord('π')
960
```

根据码点得到相应字符：

```python
>>> chr(960)
'π'
```

## 布尔类型

布尔类型`bool`它有两个值：`True`和`False`。

在Python中，下列值将被视为假：

`False`、`None`、`0`、`""`、`()`、`[]`、`{}`

除此之外的值都将被视为真。

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

- 转换标志：以`!`开头，后跟`r`（表示repr）、`s`（表示str）或`a`（表示ascii）。例如：

  ```python
  >>> print("{pi!s} {pi!r} {pi!a}".format(pi='π'))
  π 'π' '\u03c0'
  ```

- 格式说明符：以`:`开头的表达式，用于详细设置最终的格式。

  格式说明符的一般形式：

  ```
  格式说明符：
  	填充字符? 对齐说明符? (符号说明符 | '0')? '#'? 宽度? ','? 精度? 类型说明符? 
  ```
  
  这里的0表示前导0，即用0填充数值的左侧。

  | 类型说明符 | 含义                                                         |
  | ---------- | ------------------------------------------------------------ |
  | b          | 将整数表示为二进制数。                                       |
  | c          | 将整数解读为Unicode码点。                                    |
  | d          | 将整数表示为十进制数。（整数默认格式说明符）                 |
  | o          | 将整数表示为八进制数。                                       |
  | x          | 将整数表示为十六进制数，并使用小写字母。                     |
  | X          | 与x相同，但使用大写字母。                                    |
  | e          | 使用科学表示法来表示小数（用`e`来表示指数）。                |
  | E          | 与e相同，但使用`E`来表示指数。                               |
  | f          | 将小数表示为定点数。                                         |
  | F          | 与f相同，但对于特殊值（`nan`和`inf`），使用大写表示。        |
  | g          | 自动在科学表示法和定点表示法之间做出选择，且默认至少有1位小数。（小数默认格式说明符） |
  | G          | 与g相同，但使用大写来表示指数和特殊值。                      |
  | n          | 与g相同，但插入随区域而异的数字分隔符。                      |
  | %          | 将数表示为百分比值。                                         |
  | s          | 保持字符串的格式不变。（字符串默认格式说明符）               |

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

复合类型字段：

```python
>>> fullname = ['Alfred', 'Smoketoomuch']
>>> 'Mr {name[1]}'.format(name=fullname)
'Mr Smoketoomuch'
>>> import math
>>> tmpl = "The {mod.__name__} module defines the value {mod.pi} for π"
>>> tmpl.format(mod=math)
'The math module defines the value 3.141592653589793 for π'
```

类型：

```python
>>> '{:b}'.format(89)
'1011001'
```

宽度：

```python
>>> '{num:10}'.format(num=3)
'         3'
>>> '{name:10}'.format(name='Bob')
'Bob       '
```

缺省宽度时，则宽度默认为值的实际宽度。

当宽度大于值实际宽度的时，多出的部分使用设定的字符（默认是空格）填充；当宽度小于值实际宽度时，宽度自动取值实际宽度。

```python
>>> '{:3}'.format(12345)
'12345'
```

宽度包含了符号、小数点和小数位数等。

对齐方式：

```python
>>> print('{0:<10.2f}\n{0:^10.2f}\n{0:>10.2f}'.format(3.14159))
3.14      
   3.14   
      3.14
```

`<`：左对齐，`>`：右对齐，`^`：居中。

数值默认是右对齐，字符串默认是左对齐。

精度：

```python
>>> '{pi:.2f}'.format(pi=3.14159)
'3.14'
```

精度指定保留的小数位数，它是以`.`开头，后接表示小数位数的整数。

精度默认值是6。

实际上，非数值也可以指定精度：

```python
>>> '{:.5}'.format('Guido van Rossum')
'Guido'
```

填充：

```python
>>> '{:010.2f}'.format(3.1415926)   #前导0，只适用于数值。
'0000003.14'
>>> '{:0>10}'.format('abc')    #用0填充，适用于任何值（0后紧跟对齐说明符）
'0000000abc'
>>> '{:$<10.2f}'.format(3.1415926)  #用任意字符填充
'3.14$$$$$$'
>>> '{:$=10.2f}'.format(-3.1415926) #等号表示在符号与数值之间填充字符
'-$$$$$3.14'
>>> '{:=10.2f}'.format(-3.1415926)
'-     3.14'
```

缺省填充说明符，则默认以空格填充。

符号：

```python
>>> '{:+.2f}'.format(-3.1415926)
'-3.14'
>>> '{:+.2f}'.format(3.1415926)  #给正数加上符号
'+3.14'
>>> '{:-.2f}'.format(3.1415926)
'3.14'
>>> '{:-.2f}'.format(-3.1415926)
'-3.14'
>>> '{: .2f}'.format(3.1415926)  #将正号用空格代替
' 3.14'
>>> '{: .2f}'.format(-3.1415926)
'-3.14'
```

符号说明符可以是：`-`、`+`和空格。缺省符号说明符是`-`。

千位分隔符：

```python
>>> '{:,.5f}'.format(3.14**20)  #使用逗号作为千位分隔符
'8,681,463,855.99366'
```

如果要使用随区域而异的千位分隔符，应使用类型说明符`n`。

`#`：

对于二进制、八进制和十六进制数，将在它们前面加上进行前缀：

```python
>>> '{:b}'.format(42)
'101010'
>>> '{:#b}'.format(42)
'0b101010'
```

对于十进制数，以小数形式显示时，会保留小数点后的0：

```python
>>> '{:g}'.format(42)
'42'
>>> '{:#g}'.format(42)
'42.0000'
>>> '{:#f}'.format(42)
'42.000000'
>>> '{:#d}'.format(42)
'42'
```

#### format_map方法

`format_map`方法与`format`方法类似，除了`format_map`方法的参数是一个字典对象。

```python
>>> template = '''<html>
<head><title>{title}</title></head>
<body>
<h1>{title}</h1>
<p>{text}</p>
</body>'''
>>> data = {'title': 'My Home Page', 'text': 'Welcome to my home page!'}
>>> print(template.format_map(data))
<html>
<head><title>My Home Page</title></head>
<body>
<h1>My Home Page</h1>
<p>Welcome to my home page!</p>
</body>
```

### 查找

方法`find`在字符串中查找子串，如果找到，就返回首次找到的子串的第一个字符的索引，否则返回-1：

```python
>>> subject = '$$$ Get rich now!!! $$$'
>>> subject.find('$$$')
0
>>> subject.find('$$$', 1)  #指定搜索的起点
20
>>> subject.find('!!!', 0, 16)  #同时指定搜索的起点（包含）和终点（不包含）
-1
```

### 连结

方法`join`用当前字符串将作为参数的**字符串序列**中的每个元素连结起来，形成一个新的字符串：

```python
>>> dirs = '', 'usr', 'bin', 'env'
>>> '/'.join(dirs)
'/usr/bin/env'
>>> seq = ['1', '2', '3', '4', '5']
>>> '+'.join(seq)
'1+2+3+4+5'
```

### 拆分

方法`split`将当前字符串，根据参数指定的分隔符，拆分成字符串列表：

```python
>>> '/usr/bin/env'.split('/')
['', 'usr', 'bin', 'env']
>>> 'Using the default'.split()
['Using', 'the', 'default']
```

如果没有指定分隔符，则默认为一个或多个连续的空白字符（空格、制表符、换行符等）。

### 替换

方法`replace`将指定子串全部替换为另一个字符串，并返回替换后的结果：

```python
>>> 'This is a test'.replace('is', 'eez')
'Theez eez a test'
```

### 转换

方法`translate`通过一个转换表，将字符串中的指定字符转换为另一些字符，并返回转换后的结果：

```python
>>> table = str.maketrans('cs', 'kz')  #字符c将转换为字符k，s将转换为z
>>> table
{115: 122, 99: 107}
>>> 'this is an incredible test'.translate(table)
'thiz iz an inkredible tezt'
```

调用方法`maketrans`时，还可提供可选的第三个参数，指定要将哪些字符删除：

```python
>>> table = str.maketrans('cs', 'kz', ' ')
>>> 'this is an incredible test'.translate(table)
'thizizaninkredibletezt'
```

### 裁剪

方法`strip`将字符串开头和末尾的指定字符（默认是空白）删除，并返回删除后的结果：

```python
>>> '    internal whitespace is kept  '.strip()
'internal whitespace is kept'
>>> '*** SPAM * for * everyone!!! ***'.strip(' *!')
'SPAM * for * everyone'
```

类似的方法还有：`lstrip`、`rstrip`。

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

## 关系表达式

| 表达式     | 描述             |
| ---------- | ---------------- |
| x == y     | x等于y           |
| x < y      | x小于y           |
| x > y      | x大于y           |
| x <= y     | x小于或等于y     |
| x >= y     | x大于或等于y     |
| x != y     | x不等于y         |
| x is y     | x和y是同一个对象 |
| x is not y | x和y是不同的对象 |
| x in y     | x是集合y的成员   |
| x not in y | x不是集合y的成员 |

### 字符串的比较

字符串是根据字符的码点顺序进行比较的：

```python
>>> 'alpha' < 'beta'
True
>>> 'user' > 'use'
True
```

### 序列的比较

序列是根据每个元素的值来进行比较的：

```python
>>> [1, 2] > [2, 1]
False
>>> [2, [1, 4]] < [2, [1, 5]]
True
```

### 链式比较

```python
1 <= number <= 10
等价于：
1 <= number and number <= 10
```

## 逻辑表达式

`and`、`or`和`not`。

### “短路”行为

逻辑运算符`and`和`or`都有“短路”行为：

```python
>>> name = input('Please enter your name: ') or '<unknown>'
Please enter your name: 
>>> name
'<unknown>'
```

## 条件表达式

```python
status = 'friend' if name.endswith('Gumby') else 'stranger'
```

# 语句

## 语句分隔符

`;`在Python中只是语句分隔符，除了要将多条语句放在一行外，通常不需要语句分隔符。

换行符也可以作为Python的语句分隔符。

## 赋值语句

虽然Python的变量不需要声明，但它没有默认值，在使用之前必须先给它赋值。

```python
x = 3
```

### 序列解包

序列解包（或可迭代对象解包）是将一个序列（或任何可迭代对象）解包，并将得到的值存储到一系列变量中。

```python
>>> values = 1, 2, 3
>>> x, y, z = values
>>> x
1
>>> x, y = y, x  #交换值
>>> print(x, y, z)
2 1 3
```

要解包的序列包含的元素个数必须与等号左边列出的变量个数相同，否则会报错。

可以使用`*`运算符来收集多余的值：

```python
>>> a, b, *rest = [1, 2, 3, 4]
>>> rest
[3, 4]
```

还可将带星号的变量放在其他位置：

```python
>>> names = 'Albus Percival Wulfric Brian Dumbledore'
>>> first, *middle, last = names.split()
>>> middle
['Percival', 'Wulfric', 'Brian']
```

这种赋值语句的右边可以是任何类型的序列，但带星号的变量总是一个列表，即使只包含一个值：

```python
>>> a, *b, c = 'abc'
>>> a, b, c
('a', ['b'], 'c')
```

> 这种收集方式也可用于函数参数列表中。

### 链式赋值

```python
x = y = somefunction()
#等价于：
y = somefunction()
x = y
#不等价于：
x = somefunction()
y = somefunction()
```

### 复合赋值（增强赋值）

`+=`、`-=`、`*=`、`/=`、`//=`、`%=`、`**=`、`<<=`、`>>=`、`&=`、`|=`、`^=`。

## 代码块

代码块是一组语句。

Python的代码块是通过缩进的代码来创建的。可以使用空格或制表符缩进。

在同一个代码块中，各行代码的缩进量必须相同。

## 条件语句

条件语句的一般形式：

```
if 条件: 语句  #当语句只有一条时，可以放在同一行

if 条件:
	语句
	
if 条件:
	语句1
else:
	语句2
	
if 条件1:
	语句1
elif 条件2
	语句2
…
else:
	语句
```

## 循环语句

### while循环

while循环的一般形式：

```
while 条件:
  语句
```

### for-in循环

for-in循环的一般形式：

```python
for 变量 in 可迭代对象:
  语句
```

例如：

```python
words = ['this', 'is', 'an', 'ex', 'parrot']
for word in words:
  print(word)
```

#### 范围

Python提供了一个创建范围的内置函数`range`：

```python
>>> r = range(0, 10)
>>> r
range(0, 10)
>>> list(r)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

范围类似切片，包含起始位置（这里是0），但不包含结束位置（这里是10）。起始位置可以省略，默认为0：

```python
>>> range(10)
range(0, 10)
```

`range`函数还可以用第三个参数来指定步长，默认步长是1。（参见“else子句”的例子）

#### 迭代字典

```python
>>> d = {'x': 1, 'y': 2, 'z': 3}
>>> for key in d:
...   print(key, 'corresponds to', d[key])    #这里要注意缩进
... 
x corresponds to 1
y corresponds to 2
z corresponds to 3

>>> for key, value in d.items():
...   print(key, 'corresponds to', value)
... 
x corresponds to 1
y corresponds to 2
z corresponds to 3
```

#### 并行迭代

内置函数`zip`将两个序列“缝合”起来，返回一个由元组组成的可迭代对象。这样，就可以在for-in循环同时迭代两个序列：

```python
>>> names = ['anne', 'beth', 'george', 'damon']
>>> ages = [12, 45, 32, 102]
>>> list(zip(names, ages))
[('anne', 12), ('beth', 45), ('george', 32), ('damon', 102)]
>>> for name, age in zip(names, ages):
...   print(name, 'is', age, 'years old'
```

#### 迭代时获取索引

```python
for index, string in enumerate(strings):
  if 'xxx' in string:
    strings[index] = '[censored]'
```

函数`enumerate`能让你在迭代时，自动获取当前对象的索引。

### else子句

else子句紧跟在for-in循环或while循环之后，它仅当前面的循环没有调用`break`时才会执行（即循环没有提前结束）。

```python
from math import sqrt
for n in range(99, 81, -1):
  root = sqrt(n)
  if root == int(root):
    print(n)
    break
else:
  print("Didn't find it!')
```

## 跳转语句

### break语句

```python
while True:
  word = input('Please enter a word: ')
  if not word: break
  print('The word was ', word)
```

### continue语句

## pass语句

pass语句什么都不做，通常用作占位符。

## del语句

del语句用于删除对象的引用或变量。在Python中，对象只能由垃圾回收器自动回收。



# 子程序

## 函数

### 定义函数

```python
def fibs(num):
  result = [0, 1]
  for i in range(num - 2):
    result.append(result[-2] + result[-1])
  return result
```

### 返回值

在Python中，即使没有显式使用`return`语句返回的函数也有返回值——`None`。因此，Python的函数均有返回值。

### 内置函数

#### exec函数

`exec`函数将字符串作为代码执行，并且不返回任何东西。

直接在当前命名空间下执行：（很容易污染当前命名空间）

```python
>>> from math import sqrt
>>> exec('sqrt = 1')
>>> sqrt(4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
```

在显式指定的全局空间下执行：（全局命名空间必须是字典）

```python
>>> from math import sqrt
>>> scope = {}  #在全局环境定义一个命名空间
>>> exec('sqrt = 1', scope)
>>> sqrt(4)
2.0
>>> scope['sqrt']
1
```

在显式指定的局部空间下执行：（局部命名空间可以是任何映射）

#### eval函数

`eval`函数计算用字符串表示的Python表达式的值，并返回结果。

```python
>>> eval(input("Enter an arithmetic expression: "))
Enter an arithmetic expression: 6 + 18 * 2
42

>>> scope = {}  #在全局环境定义一个命名空间
>>> scope['x'] = 2  #给全局命名空间添加一些值
>>> exec('y = 3', scope)
>>> eval('x * y', scope)
6
```

与`exec`一样，也可向`eval`显式提供两个可选的命名空间（全局和局部）。

# 面向对象编程

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

列表类型是`list`。

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

##### 给元素赋值

```python
>>> names = ['Alice', 'Beth', 'Tom', 'Mary', 'Rose']
>>> names[1] = 'Kate'
>>> names
['Alice', 'Kate', 'Tom', 'Mary', 'Rose']
```

> 注意：不能给不存在的元素赋值，例如，`names[100]='Eric'`

##### 移除元素

```python
>>> names = ['Alice', 'Kate', 'Tom', 'Mary', 'Rose']

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

#清空列表
>>> lst = [1, 2, 3]
>>> lst.clear()
>>> lst
[]
```

##### 切片操作

```python
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
```

##### 添加元素

```python
#将一个对象附加到列表末尾
>>> lst = [1, 2, 3]
>>> lst.append(4)
>>> lst
[1, 2, 3, 4]

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
```

##### 复制列表

```python
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
```

##### 查找统计

```python
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
```

##### 反转列表

```python
>>> a = [1, 7, 3]
>>> a.reverse()
>>> a
[3, 7, 1]
#如果希望按相反顺序迭代列表，则使用reversed函数。它返回一个迭代器，因此，不会修改原列表。
>>> b = [1, 2, 3]
>>> list(reversed(b))  #list可以将迭代器转换成列表
>>> b
[3, 2, 1]
```

另外，方法`reverse`只能用于列表，而函数`reversed`可用于任何可迭代对象。

##### 排序

```python
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

另外，方法`sort`只能用于列表，而函数`sorted`可用于任何可迭代对象。

> 注意：`sorted`返回的是一个列表，而`reversed`返回一个可迭代对象。

##### 列表推导

```python
>>> [x * x for x in range(10)]
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

可以给列表推导加一个“Guard”，使得只有满足条件的元素才会出现在生成的列表中：

```python
>>> [x * x for x in range(10) if x % 3 == 0]
[0, 9, 36, 81]
```

列表推导中可以出现多个for部分：

```python
>>> [(x, y) for x in range(3) for y in range(3) if x == y]
[(0, 0), (1, 1), (2, 2)]
```

### 元组

元组是不可以修改的序列。

元组类型是`tuple`。

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

### 字典

字典是Python中唯一的内置映射类型。

字典类型是`dict`。

#### 字典字面量

字典由若干个键-值对组成，这种键-值对称为项（Item）。

每个键与其值之间都用冒号分隔，项之间用逗号分隔，而整个字典放在花括号内。

```python
{'age': 42, 'name': 'Gumby'}
```

字典的键可以是数、字符串或元组，即任何不可变的类型。在字典中，键必须是独一无二的，而字典中的值无此限制。

`{}`表示 空字典。

#### 构造字典

```python
>>> items = [('name', 'Gumby'), ('age', 42)]
>>> d = dict(items)
>>> d
{'age': 42, 'name': 'Gumby'}
```

通过`fromkeys`方法，可以从一个键列表中构造字典，且每个键对应的值默认是`None`：

```python
>>> {}.fromkeys(['name', 'age'])
{'age': None, 'name': None}
>>> dict.fromkeys(['name', 'age'])  #也可以直接在类dict上调用fromkey方法
{'age': None, 'name': None}
```

如果不想使用默认值`None` ，可提供特定的值：

```python
>>> dict.fromkeys(['name', 'age'], '(unknown)')
{'age': '(unknown)', 'name': '(unknown)'}
```

#### 使用字典

##### 基本操作

- `len(d)`返回字典d包含的项的数目。
- `d[k]`返回与键k相关联的值。用这种方式试图访问字典中没有的项，将引发异常。
- `d[k] = v`将值v关联到键k。如果给字典中没有的键赋值，则会创建一个新项。
- `del d[k]`删除键为k的项。
- `k in d`检查字典d中是否包含键k的项。

##### 清空字典

```python
>>> d = {}
>>> d['name'] = 'Gumby'
>>> d['age'] = 42
>>> d
{'age': 42, 'name': 'Gumby'}
>>> returned_value = d.clear()
>>> d
{}
>>> print(returned_value)  #在交互式环境中，直接执行None，将不会输出任何东西，所以需要一个print函数来输出None
None
```

##### 复制字典

浅复制：

```python
>>> x = {'username': 'admin', 'machines': ['foo', 'bar', 'baz']}
>>> y = x.copy()
>>> y['username'] = 'mlh'
>>> y['machines'].remove('bar')
>>> y
{'username': 'mlh', 'machines': ['foo', 'baz']}
>>> x
{'username': 'admin', 'machines': ['foo', 'baz']}
```

浅复制时，当替换副本中的值时，原件不受影响；而当修改副本中的值时，原件也将发生变化。

深复制：

```python
>>> from copy import deepcopy
>>> d = {}
>>> d['names'] = ['Alfred', 'Bretrand']
>>> c = d.copy()
>>> dc = deepcopy(d)
>>> d['names'].append('Clive')
>>> c
{'names': ['Alfred', 'Bretrand', 'Clive']}
>>> dc
{'names': ['Alfred', 'Bretrand']}
```

##### get方法

`get`方法与`d[k]`类似，但`get`方法访问字典中没有的项时，不会引发异常，默认将返回`None`。

可以通过`get`方法的第二个参数来显式指定当访问字典中没有的项时的返回值：

```python
>>> d = {}
>>> print(d.get('name'))
None
>>> d.get('name', 'N/A')
'N/A'
```

##### setdefault方法

`setdefault`方法类似于`get`方法，但`setdefault`方法会在指定键不存在时，会创建该键，关联的值默认为`None`，也可自己指定。

```python
>>> d = {}
>>> print(d.setdefault('name'))  #没有显式指定默认值时，则值为None
None
>>> d
{'name': None}
>>> d.setdefault('age', 18)
18
>>> d
{'name': None, 'age': 18}
```

> 如果希望有用于整个字典的全局默认值，请参阅模块`collections`中的`defaultdict`类。

##### pop方法

`pop`方法用于获取与指定键相关联的值，并将该字典项从字典中删除：

```python
>>> d = {'x': 1, 'y': 2}
>>> d.pop('x')
1
>>> d
{'y': 2}
```

##### popitem方法

方法`popitem`随机地（因为字典是无序的）获取一个字典项，并将该字典项从字典中删除：

```python
>>> d = {'title': 'Python Web Site', 'url': 'http://www.python.org', 'spam': 0}
>>> d.popitem()
('url', 'http://www.python.org')
>>> d
{'spam': 0, 'title': 'Python Web Site'}
```

> 如果希望方法`popitem`以可预测的顺序弹出字典项，请参阅模块`collections`中的`OrderedDict`类。

##### 将字典转化为项的列表

方法`items`返回一个包含字典所有项的**字典视图**，它的每个元素都为`(key, value)`的形式，对字典的修改将直接反映到视图上。

```python
>>> d = {'title': 'Python Web Site', 'url': 'http://www.python.org', 'spam': 0}
>>> it = d.items()
>>> it
dict_items([('url', 'http://www.python.org'), ('spam', 0), ('title', 'Python Web Site')])
>>> d['spam'] = 1
>>> ('spam', 0) in it
False
>>> ('spam', 1) in it
True
```

如果要将字典项复制到列表，则可以：

```python
>>> list(d.items())
[('spam', 0), ('title', 'Python Web Site'), ('url', 'http://www.python.org')]
```

##### keys方法

`keys`方法返回一个字典中的键组成的字典视图。

```python
>>> d = {'title': 'Python Web Site', 'url': 'http://www.python.org', 'spam': 0}
>>> d.keys()
dict_keys(['title', 'url', 'spam'])
```

##### values方法

`values`方法返回一个字典中的值组成的字典视图。返回的字典视图可能包含重复的值。

##### update方法

`update`方法使用一个字典中的项来更新另一个字典：

```python
>>> d = {
  'title': 'Python Web Site', 
  'url': 'http://www.python.org',
  'changed': 'Mar 14 22:09:15 MET 2016'
}
>>> x = {'title': 'Python Language Website', 'spam': 0}
>>> d.update(x)
>>> d
{'title': 'Python Language Website', 'url': 'http://www.python.org', 'changed': 'Mar 14 22:09:15 MET 2016', 'spam': 0}
```

字典x中的所有项都将添加到字典d中，如果两个字典存在相同的键，则用字典x中的项替换字典d中的项。

##### 字典推导

```python
>>> squares = {i: '{} squared is {}'.format(i, i**2) for i in range(10)}
>>> squares[8]
'8 squared is 64'
```

在列表推导中，for前面只有一个表达式，而在字典推导中，for前面有两个用冒号分隔的表达式，这两个表达式分别为键及其对应的值。

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

`print()`函数输出时，会自动在结尾跟上一个换行符`\n`。如果希望去掉这个换行符，或者以其他字符结尾，则需要使用`end`参数：

```python
>>> print('a', end=''); print('b')
ab
```

`print`函数可以将任意多个参数打印成一个空格分隔的字符串：

```python
>>> print('Age:', 42)
Age: 42
```

还可以使用参数`sep`自定义分隔符：

```python
>>> print('I', 'wish', 'to', 'register', 'a', 'complaint', sep='_')
I_wish_to_register_a_complaint
```



## 标准输入

```python
x = input("x: ")  #input函数的返回值是一个字符串
```



# 异常处理

# 断言

```python
>>> age = 10
>>> assert 0 < age < 100

>>> age = -1
>>> assert 0 < age < 100, 'The age must be realistic!'
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
AssertionError: The age must be realistic!
```

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

可以一次导入一个模块中的多个函数：

```python
>>> from somemodule import somefunction, anotherfunction, …
>>> from somemodule import *
```

可以为导入模块或函数起别名：

```python
>>> import math as foo
>>> foo.sqrt(4)
2.0
>>> from math import sqrt as bar
>>> bar(4)
2.0
```



## `__future__`模块

`__future__`是一个特殊模块，它用于存放当前Python不支持，但未来将成为标准组成部分的功能。

# 构建管理