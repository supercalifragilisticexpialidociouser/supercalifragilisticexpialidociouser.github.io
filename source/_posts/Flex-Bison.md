---
title: Flex-Bison
date: 2018-06-09 16:26:12
tags:
---

# Flex

Flex是一个词法分析生成器，用于生成词法分析器。

## 入门

### 编写Flex程序

Flex程序的扩展名通常为`.l`。

wc.l：

```flex
/* 定义部分 */
%{
int chars = 0;
int words = 0;
int lines = 0;
%}

%%

/* 规则部分 */
[a-zA-Z]+	{ words++; chars += strlen(yytext); }
\n		{ chars++; lines++; }
.		{ chars++; }

%%

/* 用户子例程 */
main()
{
  yylex();
  printf("%8d%8d%8d\n", lines, words, chars);
}
```

### 生成词法分析器

```bash
$ flex wc.l
```

这将生成`lex.yy.c`。

### 构建程序

```bash
$ cc -o wc -lfl lex.yy.c
```

> `-lfl`表示链接一个叫`fl`的Flex库，该库提供了一些辅助例程，例如自带的`main` 程序可以帮助快速编程和调试，而`yywrap()`主要作为桩函数来使用。如果不需要这些工具，则该库是可选的。

这将生成`wc`可执行程序。

### 运行

```bash
$ wc
The boy stood on the burning deck
shelling peanuts by the peck
^D

2 12 63
```

> wc从控制台读取输入。

## Flex程序结构

Flex程序由三个部分构成：定义部分、规则部分和用户子例程（subroutine）。三个部分通过`%%`行分隔。

```flex
...定义部分...

%%

...规则部分...

%%

...用户子例程...
```

“定义部分”和“规则部分”是必需部分，而“用户子例程”和它前面的`%%`是可选的。

### 定义部分

定义部分包括选项、文字块、定义、开始条件和转换。

以空白字符开始的行将被原样复制到C文件中。

### 规则部分

规则部分包括模式行和C代码。

规则部分包含的C代码是指以空白字符开始的行，或者是处于`%{` 和 `%}`之间的内容，它们会被原封不动地复制到`yylex` 函数中。

模式行包含一个模式和模式匹配输入时所执行的C代码，两者之间使用若干空白字符分隔。如果C代码超过一条语句或跨越多行，则必须用`{` 和 `}` （或者`%{` 和 `%}`）括起来。

当Flex词法分析器运行时，它将输入依次与模式行的模式进行匹配。每当它发现一个匹配，它就执行与这个模式关联的C代码。

如果模式后面紧跟一个竖线（`|`）而不是C代码，则表示该模式与下一模式共用相同的C代码。

当输入无法匹配任何模式时，则执行`ECHO`宏（将所有没有被匹配的输入，原样输出到`yyout`。它等价于`fprintf(yyout, "%s", yytext);`）。也可以通过`%option nodefault` 、命令行参数`-s`或者命令行参数`--nodefault`来禁用这个默认行为。

### 用户子例程

用户子例程中的内容将被Flex原样复制到C文件。

这个部分通常包括规则中需要调用的例程，在大型程序里，这些支撑代码通常会放在另外一个单独的源文件中。

## 模式

### 正则表达式

### 处理二义性模式

## 上下文相关性

Flex提供了多种方式来做到模式的左上下文相关和右上下文相关，也就是与记号的上文和下文相关。

### 左上下文相关

有三种方法可以做到左上下文相关：

- 特殊的行首模式字符（模式开关的`^`）；
- 起始状态；
- 显式代码。

显式代码例子：

```flex
%{
int flag = 0;
%}
%%
a {flag = 1;}
b {flag = 2;}
zzz {
  switch(flag) {
    case 1: a_zzz_token(); break;
    case 2: b_zzz_token(); break;
    default: plain_zzz_token(); break;
  }
  flag = 0;
}
```

### 右上下文相关

也有三种方法可以做到右上下文相关：

- 特殊的行尾模式字符（模式结尾的`$`，等价于`/\n`）；
- 斜线操作符`/`；
- `yyless()`。

`yyless()`让词法分析器推回刚刚读取到的字符，它的参数表明需要保留的字符个数。例如：

```flex
abcde {yyless(3);}
```

这个例子与`abc/de`模式的效果基本一致，因为`yyless(3)`保留三个字符`abc`，并推回剩下的二个字符`de`。唯一的差别是在这个例子里，`yytext`包含所有5个字符，而且`yyleng`的值是5而不是3。

## 定义（替换）

定义（或替换）用于为正则表达式命名，然后在规则部分通过`{名字}`来引用它们。这个名字所代表的正则表达式将被代入到模式中，并且该正则表达式会被认为已经用圆括号括起。例如：

```flex
DIG [0-9]
%%
{DIG}+  process_integer();
{DIG}+\.{DIG}*  |
\.{DIG}+  process_real();
```



## 输入管理

除非你另做安排，否则词法分析器总是通过名为`yyin`读取输入。`yyin`默认是标准输入`stdin`。

为让它从文件中读取输入，只需在第一次调用`yylex`之前，把任何打开的文件赋予`yyin`。

```c
%{
int chars = 0;
int words = 0;
int lines = 0;
%}

%%

[a-zA-Z]+	{ words++; chars += strlen(yytext); }
\n		{ chars++; lines++; }
.		{ chars++; }

%%

main(argc, argv)
int argc;
char **argv;
{
  if(argc > 1) {
    if(!(yyin = fopen(argv[1], "r"))) {
      perror(argv[1]);
      return (1);
    }
  }

  yylex();
  printf("%8d%8d%8d\n", lines, words, chars);
}

yywrap() { return 1; }
```



## 选项

## 文字块

## 起始状态

Flex有一个强大的特性——起始状态（start state，也称为起始条件、起始规则），它允许我们指定在特定时刻哪些模式可以被用来匹配。

词法分析器从状态0（也可用`INITIAL`）开始，它是默认的起始状态。其他所有状态必须在定义部分通过`%s`或`%x`来声明。

要从一个起始状态切换到另一个起始状态，要使用宏`BEGIN`，例如：

```flex
%x MYSTATE
%%
… {BEGIN MYSTATE;}
…
<MYSTATE>…  {BEGIN INITIAL;}
```



# Bison

Bison是一个语法分析生成器，用于生成语法分析器。