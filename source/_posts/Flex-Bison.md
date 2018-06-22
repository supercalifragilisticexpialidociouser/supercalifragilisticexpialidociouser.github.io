---
title: Flex-Bison
date: 2018-06-09 16:26:12
tags:
---

# Flex

Flex是一个词法分析生成器，用于生成词法分析器。

## 入门

### 编写Flex程序

Flex程序的扩展名通常为`.l`或`.lex`。

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

调用`yylex()`，会开始读取输入，直到动作代码识别出一个记号（token），并返回这个记号（通常是返回记号编号，本次记号值保存在自定义的全局变量中，本次匹配的输入文本保存在全局变量`yytext`中）。每次`ylex` 返回时，它会记住当前处理的位置。当程序再次调用`yylex()`时，词法分析器会从这个位置开始继续分析输入。如果动作代码没有返回，则词法分析将会立即继续分析输入。例如，匹配空白字符的模式并不返回，所以词法分析器会继续分析接下来的输入。

### 生成词法分析器

```bash
$ flex wc.l
```

这将生成`lex.yy.c`。

### 构建程序

```bash
$ cc -o wc -lfl lex.yy.c
```

> `-lfl`表示链接一个叫`fl`的Flex库，该库提供了一些辅助例程，例如自带的`main` 程序可以帮助快速编程和调试。如果不需要这些工具，则该库是可选的。

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

模式行包含一个模式和动作（模式匹配输入时所执行的C代码），两者之间使用若干空白字符分隔。如果C代码超过一条语句或跨越多行，则必须用`{` 和 `}` （或者`%{` 和 `%}`）括起来。

当Flex词法分析器运行时，它将输入依次与模式行的模式进行匹配。每当它发现一个匹配，它就执行与这个模式关联的C代码。

如果模式后面紧跟一个竖线（`|`）而不是C代码，则表示该模式与下一模式共用相同的C代码。

当输入无法匹配任何模式时，则执行`ECHO`宏（将所有没有被匹配的输入，原样输出到`yyout`。它等价于`fprintf(yyout, "%s", yytext);`）。也可以通过`%option nodefault` 、命令行参数`-s`或者命令行参数`--nodefault`来禁用这个默认行为。

### 用户子例程

用户子例程中的内容将被Flex原样复制到C文件。

这个部分通常包括规则中需要调用的例程，在大型程序里，这些支撑代码通常会放在另外一个单独的源文件中。

## 模式

### 正则表达式

- `.`：匹配除换行符（`\n`）以外的任意单一字符。
- `[]`：字符类，可以匹配方括号中的任意一个字符。
  字符类中第一个字符是`^`时，表示匹配除了方括号内字符以外的任意一个字符。
  字符类中的`-`表示字符范围，例如：`[a-z]` 表示任意小写字母。
  字符类中可以识别转义序列。
  字符`-`和`]`如果是方括号内第一个字符，则表示普通字符。
- `{-}`：匹配左侧字符类减去右侧字符类的结果中的任一字符。
- `{+}`：匹配左侧字符类加上右侧字符类的结果中的任一字符。
- `*`：匹配零个或多个紧接在前面的模式。
- `+`：匹配一个或多个紧接在前面的模式。
- `?`：匹配零个或一个紧接在前面的模式。
- `{N}`：匹配N个紧接在前面的模式。
- `{N, M}`：匹配N至M个（包含N和M个）紧接在前面的模式。`M`可以省略，即`{N,}`表示匹配至少N个紧接在前面的模式。`{1,}`与`+`意义相同，`{0,}`与`*`意义相同。
- `{模式名}`：引用这个名字的模式。
- `\`：表示元字符自身或C语言转义序列。
- `()`：把一系列的正则表达式组合在一起。
- `|`：匹配左侧模式或右侧模式。
- `“…”`：所有引号中的字符将基于字面意义被解释。
- `/`：尾部上下文（trailing context），匹配斜线前的模式，但是要求其后紧跟着斜线后的模式。斜线后匹配的内容不会被“消耗掉”，它们会返还给输入，以便于后续匹配。每个模式行只允许一个尾部上下文操作符，而且一个模式行不能既有斜线又有尾部`$`。
- `^`：当它是模式的第一个字符时，匹配一行的开始。它出现在方括号中第一个字符表示补集。其他情况下，它没有特殊含义。
- `$`：当它是模式的最后一个字符时，匹配一行的结束（相当于`/\n`），否则没有特殊含义。
- `<起始状态名或起始状态名列表>`：只出现在模式的开头，表示该模式仅在给定的起始状态下被应用。
- `<<EOF>>`：匹配文件末尾。这个伪模式通常用来进行清理工作、切换到其他文件等等。

### 处理二义性模式

Flex通过两个简单规则来解决模式的二义性：

- 匹配输入时匹配尽可能多的字符串；
- 如果两个模式都可以匹配的话，匹配在程序中更早出现的模式。

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

### 从文件读取输入

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

词法分析器到达`yyin`（是`FILE *`类型）的结束位置时，它将调用`yywrap()`。这样做的目的是，当有另一个输入文件时，`yywrap` 可以调整`yyin` 的值并且通过返回0来重新开始词法分析。如果要结束词法分析，则返回1，同时词法分析器会返回一个零记号来表明文件结束。

`fl`库中包含一个默认的`yywrap()`，它总是返回1。如果你的词法分析器不使用`yywrap()` 来切换文件，可以使用选项`%option noyywrap`来移除对`yywrap()`的调用。特殊记号`<<EOF>>`通常是更好的处理文件结束情况的方式。

### 读取多个文件

```flex
%option noyywrap

%{
int chars = 0;
int words = 0;
int lines = 0;

int totchars = 0;
int totwords = 0;
int totlines = 0;
%}

%%

[a-zA-Z]+	{ words++; chars += strlen(yytext); }
\n		{ chars++; lines++; }
.		{ chars++; }

%%

main(int argc, char **argv)
{
  int i;

  if(argc < 2) { /* just read stdin */
    yylex();
    printf("%8d%8d%8d\n", lines, words, chars);
    return 0;
  }

  for(i = 1; i < argc; i++) {
    FILE *f = fopen(argv[i], "r");
  
    if(!f) {
      perror(argv[1]);
      return (1);
    }
    yyrestart(f);
    yylex();
    fclose(f);
    printf("%8d%8d%8d %s\n", lines, words, chars, argv[i]);
    totchars += chars; chars = 0;
    totwords += words; words = 0;
    totlines += lines; lines = 0;
  }
  if(argc > 1)
    printf("%8d%8d%8d total\n", totlines, totwords, totchars);
  return 0;
}
```

当需要顺序读取多个文件时，我们每打开一个文件就调用一次`yyrestart(fp)`，把词法分析器的输入切换到新的标准输入输出文件`fp`。宏`YY_NEW_FILE` 等同于`yyrestart(yyin)`。

### 输入缓冲区

词法分析器实际上并不是直接读取输入来分析，而是先将输入读取到缓冲区，再从缓冲区中读取字符进行分析。

Flex使用宏`YY_BUFFER_STATE` 来表示输入缓冲区。

我们可以使用`yy_create_buffer`来为标准输入输出文件中创建缓冲区，使用`yy_scan_string` 为字符串（以空字符结尾）创建缓冲区，使用`yy_scan_buffer`为长度确定的数据流创建缓冲区。

使用`yy_switch_to_buffer`切换到新的缓冲区。

### 读取输入

每当输入缓冲区为空时，词法分析器就会调用宏`YY_INPUT`来读取输入到当前缓冲区。

出于最大的灵活性的考虑，你可以重新定义宏`YY_INPUT`。

另外，Flex还提供了两个在动作代码中比较有用的宏：`input()`和`unput()`。每次调用`input()` 将返回输入流的下一个字符，它可以帮助我们读取一小段输入而不用定义相应的模式。每次对`unput(c)` 的调用将把字符`c`推回到输入流。

## 记号

记号实际上有两个组成部分：记号编号和记号值。

记号编号是一个整数，通常从258开始（这样可以避免与文字字符记号产生冲突，bison就是自动从258开始指派每个记号的编号）。

记号值用于区分一组相似的记号。

```flex
%{
   enum yytokentype {
     NUMBER = 258,
     ADD = 259,
     SUB = 260,
     MUL = 261,
     DIV = 262,
     ABS = 263,
     EOL = 264 /* end of line */
   };

   int yylval;
%}

%%
"+"	{ return ADD; }
"-"	{ return SUB; }
"*"	{ return MUL; }
"/"	{ return DIV; }
"|"     { return ABS; }
[0-9]+	{ yylval = atoi(yytext); return NUMBER; }
\n      { return EOL; }
[ \t]   { /* ignore white space */ }
.	{ printf("Mystery character %c\n", *yytext); }
%%
main()
{
  int tok;

  while(tok = yylex()) {
    printf("%d", tok);
    if(tok == NUMBER) printf(" = %d\n", yylval);
    else printf("\n");
  }
}
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