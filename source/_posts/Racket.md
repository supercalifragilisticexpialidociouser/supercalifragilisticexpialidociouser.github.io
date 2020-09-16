---
title: Racket
date: 2020-09-16 11:18:49
tags: [7.8]
---

Racket源自PLT Scheme。Racket现在已经演变成一个面向编程语言的语言（即通过Racket的宏机制实现的许多类Racket语言）的平台。

# 起步

## 安装

DrRacket：Racket自带的IDE。它有两个编辑区，上面的那个是定义区，下面的是交互区。

racket：是核心编译器、解释器和运行时系统。

raco：用于安装包、构建库等。

## 交互环境

方式一：使用DrRacket

方式二：racket + 代码编辑器

## 程序结构

Racket程序的结构：

```
#lang ‹langname› ‹topform›*
```

### 选择语言

```scheme
#lang racket
```

### 注释

以分号开始一直到行尾为注释部分。

```scheme
; 这是行注释
```

### 顶层句式

**句式**（Syntactic Forms）有点类似其他语言中的语句。

## 运行脚本

```bash
$ racket xxx.rkt
```

## 制作可执行程序

第一种方式：在DrRacket中选择 `Racket|Create Executable...` 菜单。

第二种方式：在命令行中，运行 `raco exe xxx.rkt`。

第三种方式：在源文件的开头加上`#! /usr/bin/env racket`。要改变源文件的权限为可执行，即 `chmod +x xxx.rkt`。

# 语言模型

## 求值模型

Racket求值（evaluation）可以看作是表达式简化到值的过程。

还没被简化到值的表达式总是可以被分成两部分：

1. redex（reducible expression）：它是当前可以单步简化的部分。
2. continuation：进一步求值的上下文。即 redex 在被归约成值后，表达式如何继续简化。

例如，在表达式`(- 4 (+ 1 1))`中，`(+ 1 1)`是redex部分，而`(- 4 [])`是continuation部分（其中`[]`表示redex归约的结果）。

### 求值策略

Racket表达式使用应用序求值，即在计算某个表达式之前，它的部分或全部子表达式必须已经被求值。

但是，每个Racket句式都可以有自己不同的求值策略。

### 尾部位置

当子表达式的 continuation 与包含它的表达式的 continuation 相同时，我们说这个子表达式位于包含它的表达式的**尾部位置**（tail position）。

### 多返回值

Racket表达式可以返回多个值。

### 值与对象

值是表达式的结果，而对象持有被值引用的数据，即值是对象的引用。对象被修改后，所有引用它的值都会反应这个修改。

一些对象可直接充当值，例如：布尔值、`(void)`、精确的小整数等。

顶层变量与值是不一样的，在求值过程中，变量需要进一步求值，而值不需要。变量是值的占位符（placeholder）。

### 垃圾回收

当一个对象没有被引用或者只是通过弱引用（weak references）可到达（reachable）时，这个对象就可以被垃圾回收器回收。（弱引用会被不同的值替换，通常是 `#f`）

`fixnum`、字符、`null`、`#t`、`#f`、`eof`、`#<void>`，以及被`quote`的值总是可到达的（即不会被回收）。

## 语法模型