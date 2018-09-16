---
title: Python
date: 2018-09-15 12:15:06
tags: [3.7.0]
---

# 简介

# 开发环境

## 交互式环境

安装完Python后，执行命令`python`将进入交互式环境。如果同时安装了版本2和3的Python，则命令`python`通常是指Python 3，另外还有命令`python2`和`python3`。

```bash
$ python
>>> print("Hello, world!")
```

退出交互式环境使用函数`quit()`或快捷键`Ctrl-D`。

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

### 转义序列

### 编码规范

## 构建

## 运行

直接运行脚本：

```bash
$ python hello.py
```

shebang方式运行：

首先，要在`hello.py`的第一行添加`#!/usr/bin/env python`。

然后，赋予脚本可执行的权限：`chmod a+x hello.py`。

```bash
$ ./hello.py
```



# 程序结构

## 保留字

## 注释

| 注释        | 说明                             |
| ----------- | -------------------------------- |
| `# …`       | 单行注释。可嵌套。               |
| `''' … '''` | 块注释。可跨多行，但不可以嵌套。 |

# 基本类型

## 数值类型

### 整数类型

```python
81    #十进制整数
0xAF  #十六进制整数
0o17  #八进制整数
0b1011 #二进制整数
```

### 复数类型

Python本身提供了对复数的支持。

虚数都以`j`或`J`结尾。

Python没有专门的虚数类型，虚数可视为实部为0的复数。

```python
>>> (1 + 3j) * (9 + 4j)
(-3 + 31j)
```

另外通过`cmath`标准库专门用于处理复数：

```python
>>> import cmath
>>> cmath.sqrt(-1)
1j
```



# 声明

Python变量不需要声明。

# 数组

# 字符串

## 字符串字面量

Python的字符串使用单引号或双引号括起来。

```python
"Let's go!"
'"Hello world!" she said'
```

## 构造字符串

类`str`和函数`repr`可以构造字符串：

```python
>>> repr("Hello, \nworld!")
"'Hello, \\nworld'"
>>> str("Hello, \nworld")
'Hello, \nworld'
```



## 使用字符串

### 字符串的长度



### 拼接

```python
s1 = "Hello " + "world!"
s2 = "Hello " 'world!'  #连接输入两个字符串字面量，将会自动拼接成一个字符串
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
```



# 子程序

# 集合类型

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