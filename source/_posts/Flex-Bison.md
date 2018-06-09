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

这将生成`wc`可执行程序。



# Bison

Bison是一个语法分析生成器，用于生成语法分析器。