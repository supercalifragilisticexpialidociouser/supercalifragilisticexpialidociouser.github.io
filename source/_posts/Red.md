---
title: Red
date: 2018-11-01 09:32:24
tags: [0.6.3]
---

# 简介

[Red](https://www.red-lang.org/)是一个全栈的编程语言，它是[Rebol](http://rebol.com/)的开源替代方案。

Red程序可解释执行，也可以编译并生成单个可执行文件。

Red是跨平台的，有一个原生的、跨平台的GUI系统，并有UI方言和绘图方言。

Red技术栈由两个层次组成：Red语言（高级）、Red/System（低级DSL，用于系统编程）。

Red社区：<https://gitter.im/red/red>

# 开发环境

## 安装

[下载Red可执行程序](https://www.red-lang.org/p/download.html)，不需要安装。

在Linux下：（以Ubuntu为例，其他参见官网）

```bash
$ chmod u+x red-xxx
$ dpkg --add-architecture i386
$ apt update
$ apt install libc6:i386 libcurl3:i386
```

### 从源码构建

1. 下载Red源码并解压；

2. 下载Rebol解释器，并放到Red源码根目录中；

3. 打开Rebol解释器，并运行：

   ```rebol
   do/args %red.r {%yourscript.red}
   ```

   这将生成`yourscript.exe`。

## 交互式

在Windows下，直接双击下载的`Red-xxx.exe`，首次将自动创建Red GUI控制台（在`C:\ProgramData\Red\`中。如果升级了Red，则最好要删除该目录下的文件，否则可能仍然使用旧版本GUI控制台）。下次运行该文件时，将自动打开GUI控制台。

如果要在命令提示符（cmd或PowerShell）下打开Red交互式环境，则需要加上`--cli`选项，否则是打开GUI控制台。（Linux下，加不加`--cli`都一样）

```powershell
PS C:\Users\Jo> red --cli
```

在Linux下，在终端上：

```bash
$ red-xxx
>> print "Hello"
Hello
```

要退出交互式环境，可以运行函数`quit`或`q`，也可以按下`Ctrl-C`（不适用于GUI控制台）。

## 脚本式

### 脚本

解释的编程语言一次执行一行代码。解释语言的程序称为“脚本”（Script）。 Red并没有真正被解释，因为它在运行之前进行了一些编译（排序），但它的程序通常被称为脚本。

#### 第一个脚本

hello.red：

```red
Red []

print "Hello world!"
```

每个Red脚本必须以`Red`（这里区分大小写）开头，之后跟一个包含有关您的程序信息的块（在Red中，方括号内的任何内容都称为“块”）。

Red是不区分大小写的，除了极少数特殊情况外。

#### 注释

行注释：`; 从分号开始一直到行尾`或者`comment "行注释"`。

块注释：`comment {块注释}`、`{块注释}`或者`comment [块注释]`。

此外，`Red […]`之前的内容都将被解释器忽略，因此，也可将`Red […]`之前的内容视为注释。

### 编译

开发模式编译会生成多个文件，但只有可执行文件和`libRedRT.so`（或`libRedRT.dll`）是运行程序必须的：

```bash
$ red -c hello.red
```

发布模式编译只会生成一个可执行文件：

```bash
$ red -r hello.red
```

交叉编译：

```bash
$ red -r -t Windows hello.red 
```

`-t`选项指定目标平台，合法的值有：

- `MSDOS`：Windows下的默认。Windows控制台应用（运行时，总是会打开命令行提示符）。
- `Windows`：Windows GUI应用。
- `WindowsXP`：Windows GUI应用，但不支持触摸API。
- `Linux`：Linux下的默认。
- `Linux-ARM`
- `RPi`
- `Darwin`：macOS的控制台或GUI应用。
- `macOS`：macOS的GUI应用。
- `Syllable`
- `FreeBSD`
- `Android`
- `Android-x86`

另外，可以通过`-o`选项，自定义生成的可执行文件名，默认与被编译的脚本文件名相同。

```bash
$ red -c -o ~/hi hello.red
```

### 直接运行

直接运行模式也称调试模式编译，它直接运行脚本，而不生成可执行程序。

```bash
$ red -d hello.red
或者
$ red hello.red
```

### 给Red脚本传递参数

在命令行上，脚本文件名后面的所有内容都作为参数传递给脚本。这些参数作为块存储在`system/options/args`中。

arguments.red：

```red
Red []

probe system/options/args
```

执行脚本：

```bash
$ red arguments.red foo bar
["foo" "bar"]
```



## IDE

建议使用[Visual Studio Code](https://code.visualstudio.com/)  + [Red extension](https://marketplace.visualstudio.com/items?itemName=red-auto.red)。

## 帮助

### `help`和`?`

函数`help`或`?`可以获取目标的帮助信息：

```red
>> ? print
USAGE:
     PRINT value

DESCRIPTION: 
     Outputs a value followed by a newline. 
     PRINT is a native! value.

ARGUMENTS:
     value        [any-type!]
     
>> a: [1 2 3]
== [1 2 3]
>> help a
A is a block! value: [1 2 3]
```

如果你不确切地知道你在找什么，`?`将为您搜索：

```red
>> ? -to
     hex-to-rgb      function!     Converts a color in hex format to a tuple...
     link-sub-to-parent function!     [face [object!] type [word!] old new /loc...
     link-tabs-to-parent function!     [face [object!] /init /local faces visible?]
```

对数据类型使用`?`，将列出声明为该类型的所有变量：

```red
>> ? tuple!
```

### 列出所有全局函数

`what`函数会列出所有全局函数。

```bash
>> what
```

### 查看源码

`source`函数可以查看指定的mezzanine函数或自定义函数的源码：

```bash
>> source replace
```

> Red解释器本身包含两种函数：原生函数（可能由其他编程语言编写）和mezzanine函数（由Red编写）。

### 查看解释器信息

`about`函数可以查看Red解释器的版本号和构建时间等信息。

# 数据类型

# 变量

Red中的变量实际上是没有类型，可以随时赋给不同值。

## 赋值

Red没有专门的声明语法，变量的首次赋值可以看作是声明。

变量在求值时必须有值。

有两种方式可以给Red变量赋值。

第一种：`变量:`

```red
>> myBirthday: 30/07/1963
== 30-Jul-1963
>> print myBirthday
30-Jul-1963
```

注意：冒号与它前面的变量之间不能有空白字符。

第二种：`set`函数

```red
>> set 'test 33
== 33
```

`set`函数还可以一次给多个变量赋值：

```red
>> set [a b c] 10
== 10
>> b
== 10
```

### 反赋值

可以使用`unset`函数取消变量的赋值：

```red
>> set 'test "hello"
== "hello"
>> print test
hello
>> unset 'test
>> print test
*** Script Error: test has no value
```

## 原样返回

在变量之前加一个冒号（它们之间不能出现空白字符），则表示不对变量关联的内容进行求值，原样返回变量的内容。

```red
>> imprimire: :print
== make native! [[
   "Outputs a value followed by a newline" 
   ...
>> imprimire "hello"
hello
```

上面代码中，`imprimire`现在具有与`print`相同的功能。

# 符号

以单引号开头的标识符是符号，它的值就是它本身。

```red
>> print something
*** Script Error: something has no value
*** Where: print
*** Stack:  

>> print 'something
something

>> type? :print
== native!
>> type? 'print
== word!
```

# 表达式

## 求值

对字面量进行求值，结果为它本身。

### 块求值

块默认是不求值的，它被当作数据看待。如果需要对块求值，则需要使用`do`函数。

```red
>> [1 + 2]
== [1 + 2]

>> do [1 + 2]
== 3
```

如果块中包含多个表达式，每个表达式都会被求值，但仅返回最后一个表达式的结果：

```red
>> do [
[        1 + 2
[        3 + 4
[    ]
== 7
```

如果希望返回块中每个表达式的值，则需要使用`reduce`函数：

```red
>> reduce [
[        1 + 2
[        3 + 4
[    ]
== [
    3 
    7
]
```

当`do`函数的参数是一个文件时，它将加载这个文件，并将文件当作块来执行：

```red
do %script.red
```

`do`函数还可以执行字符串中包含的表达式，它首先将该字符串转换成一个块，再执行：

```red
>> do "1 + 2"
== 3
```



### 求值顺序

Red总是从左到右求值，运算符之间没有优先级。

运算符优先于函数应用，唯一的例外是如果运算符的第二个操作数是函数，则先对该函数应用进行求值。

括号中的表达式优先级最高。

```red
>> 2 + 3 * 5 
== 25 ; not 17!

>> a: [ square-root 16 8 + 2 8 / 2 77]
== [square-root 16 8 + 2 8 / 2 77]
>> reduce a 
== [4.0 10 4 77]

>> (square-root 16) + square-root 16 
== 8.0

>> reduce [add 8 + 2 * 3 8 / 2 divide 16 / 2 2 * 2] 
== [34 2]
```



# 控制结构

# 子程序

## 函数

### Refinements

## 运算符

# 作用域

# 数据结构

# 输入和输出

# 异常

# 断言

