---
title: Smalltalk
date: 2018-08-29 10:29:20
tags:
---

# 简介

Smalltalk是一个面向对象的、动态类型的编程语言。

IDE：[Pharo](https://pharo.org/)、[Squeak](https://squeak.org/)

# 词法

## 字符集

- a-z
- A-Z
- 0-9
- .+/*~<>@%|&?
- blank, tab, cr, ff, lf

## 标识符

全局变量的首字母必须大写，局部变量的首字母必须小写。

## 关键字

关键字实际上是以冒号结尾的标识符（例如`keyword:`），它只有在它形成“关键字消息”的时候才有意义。

## 保留字

 `nil`、`true`、`false`、`self`、`super`和`Smalltalk`

`self`引用消息接收者。

`super`引用定义当前代码的对象的父类，而不是引用接收者的父类。

## 数值

```smalltalk
5        "十进制数5"
16r7f    "十六进制数7f"
基数r111 "任意进制数11"
2e-5     "浮点数"
22/7     "分数"
```

## 字符

```smalltalk
$h    "字符h"
$     "$后跟一个空格表示空格字符"
```

## 字符串

```smalltalk
'这是字符串'
'I''m here'
```

字符串中的单引号，使用两个单引号（`''`）表示。

## 布尔值

`true`：`True`类的唯一实例。

`false`：`False`类的唯一实例。

## 空值

 `nil`：代表未初始化的值。

## 符号

```smalltalk
#red    "符号red"
```

## 注释

```smalltalk
"这是注释"
```

# 变量

Smalltalk中的变量是没有类型的，即它可以引用任意类型对象。因此，声明变量时，不需要指定类型。

Smalltalk的对象是有类型的（实际上是类）。

变量必须在使用前声明。

## 作用域

### 全局作用域

在`Smalltalk`字典中定义，对所有对象可见。

全局变量命名，约定以大写字母开始。

### 方法临时作用域

在方法内部声明，只在方法内部可见。

方法临时变量要在方法体的开头处声明。

方法临时变量命名，约定以小写字母开始。

### 块临时作用域

在块中声明，只在块中可见。

### 池作用域

在字典对象中的变量。

### 方法参数作用域

### 块参数作用域

### 类作用域

类变量在类和类的实例中可见。

类变量命名，约定以大写字母开始。

### 类实例作用域

类实例变量只在类中可见，在实例中不可见。相当于，java中的`private static`。

类实例变量命名，约定以大写字母开始。

### 实例变量作用域

实例变量只在对象中可见，相当于私有成员变量。

实例变量命名，约定以小写字母开始。

## 局部变量声明

```smalltalk
| x y |
```

声明了两个局部变量`x`和`y`。

# 表达式

## 赋值

```smalltalk
x := 5.
x := y := z := 6.
x := (y := 6) + 1.
```

## 块

块（也叫闭包）也是对象。

除非有显式的返回，否则块的值是它的最后一个表达式的值 。

块可以嵌套。

块的一般形式：

```smalltalk
[ 参数 | 局部变量声明 表达式 ]
```

无参块：

```smalltalk
[ y := 1. z := 2. ]
```

带参数块：

```smalltalk
| block |
block := [:x :y | x * 2 + y].
block value: 5 value: 3     "结果为13"
```

块也是对象，value是它的一个方法，它的作用就是返回块的结果。



## 消息

消息实际上就是方法调用。

### 一元消息

```smalltalk
object unaryMessage     "相当于Java：object.unaryMessage()"
myDept manager name last  "相当于Java：myDept.manager().name().last()"
```

上面例子中，`object`是接收者，而`unaryMessage`是发送给接收者的消息。

`myDept`是接收者，`manager`是发送给`myDept`的消息，`name`是发送给`myDept manager`的结果的消息，`last`是发送给`myDept manager name`的结果的消息。这形成了一个消息链。

### 二元消息

```smalltalk
5 + 3  "5是接收者对象，(+ 3)是消息，这个消息带一个参数3。"
```

预定义的二元消息：

+

\-

*

/

**：幂

//：整除

\\：求模

<

<=

\>

\>=

=：内容相等

~=：内容不相等

==：引用相等

~~：引用不相等

&：逻辑与

|：逻辑或

,：拼接两个集合

@：创建Point类的实例

->：创建Association类的实例

### 关键字消息

```smalltalk
'elephant' copyFrom: 3 to: 5
```

`'elephant'`是接收者对象，`(copyFrom: 3 to: 5)`是消息，这个消息有两个关键字`copyFrom:`和`to:`，每个关键字带一个参数。

上面例子相当于Java：`"elephant".copyFromTo(3, 5)`。

Smalltalk没有内在的控制结构，其他语言中的条件语句、循环语句，在Smalltalk中就是一个关键字消息。

Smalltalk的“条件语句”：

```smalltalk
tries > 5
ifTrue: [^ 'Too many tries']
ifFalse: [^ 'Trying again']
```

Smalltalk的“循环语句”：

```smalltalk
| tries |
tries := 0.
[tries <= 5] whileTrue: [
  self tryAgain.
  tries := tries + 1
]
"或者"
5 timesRepeat: [selt tryAgain]
```



### 消息优先级

一元消息优先级最高，二元消息其次，关键字消息最低。

通过圆括号可以改变优先级。

同等优先级下，从左到右进行运算。

### 消息级联

分号表示消息级联：

```smalltalk
|p|
p := Client new
  name: 'Jack';
  age: 32;
  address: 'Earth'.
```

相当于：

```smalltalk
|p|
p := Client new.
p name: 'Jack'.
p age: 32.
p address: 'Earth'.
```



# 集合

Smalltalk中，集合的索引都是从1开始。

## 数组

### 数组字面量

```smalltalk
#('abc' 2 $a)  "数组字面量"
```



# 语句

语句使用`.`作为分隔符，最后一条语句不需要加`.`号。

## 返回语句

```smalltalk
^ 返回对象
```

## 表达式语句

```smalltalk
x := 5.
```

# 类

类是定义某种对象特性的对象。

## 创建实例

```smalltalk
AClass new   "相当于Java：new AClass()"
```



## 方法

任何方法都会至少有一个返回值，如果有显式返回，默认将会返回`^self`。

