---
title: re2c
date: 2019-08-21 22:38:52
tags: [1.2.1]
---

# 语法

re2c程序由许多re2c块和指令组成，并混合着普通的C/C++代码。生成的词法分析器通过用户接口与外部世界通信。

```re2c
/*!re2c re2c:flags:i = 1; */         // re2c block with configuration that turns off #line directives
                                     //
#include <stdio.h>                   //    C/C++ code
                                     //
/*!max:re2c*/                        // directive that defines YYMAXFILL (unused)
/*!re2c                              // start of re2c block
    digit  = [0-9];                  //   named definition of 'digit'
    number = digit+;                 //   named definition of 'number'
*/                                   // end of re2c block
                                     //
static int lex(const char *YYCURSOR) // YYCURSOR is defined as a function parameter
{                                    //
    const char *YYMARKER;            // YYMARKER is defined as a local variable
    /*!re2c                          // start of re2c block
    re2c:define:YYCTYPE = char;      //   configuration that defines YYCTYPE
    re2c:yyfill:enable  = 0;         //   configuration that turns off YYFILL
                                     //
    * { return 1; }                  //   default rule with its semantic action
                                     //
    number {                         //   a normal rule with its semantic action
        printf("number\n");          //     ... semantic action (continued)
        return 0;                    //     ... semantic action (continued)
    }                                //   end of semantic action
                                     //
    */                               // end of re2c block
}                                    //
                                     //
int main()                           //
{                                    //
    lex("1024");                     //    C/C++ code
    lex(";]");                       //
    return 0;                        //
}                                    //
```



## re2c块

每个re2c块由一组正则表达式定义、配置和规则组成。

### 规则

规则由正则表达式后跟用户定义的动作（语义动作，在成功匹配的情况下执行的C/C++代码块）组成。

语义动作可以是用花括号`{`和`}`括起来的任意代码块，也可以是没有大括号的代码块，前面带有`:=`并以不带空格的换行结束。

如果多个规则匹配，则最长匹配优先。 如果多个规则匹配相同的字符串，则较早的规则优先。

有两种特殊的规则：

- 默认规则`*`有最低优先级，并且与它在代码中的位置无关。它匹配任何代码单元并且仅消耗一个代码单元。
- EOF规则`$`匹配输入的结尾。

### 定义

定义的默认格式：

```re2c
名称 = 正则表达式
```

当使用了`-F`或`--flex-syntax`选项时，则定义的格式为：

```re2c
名称 正则表达式
```

每个名称都应在使用前定义。

#### 正则表达式

正则表达式语法：

- `"foo"`：区分大小写的字面串。当使用兼容Flex模式时，则使用`foo`。
- `'foo'`：不区分大小写的字面串。
- `[a-xyz]`和`[^ a-xyz]`：字符类。
- `.`：除换行之外的任何字符。
- `R \ S`：字符类R与字符类S的差集。
- `R*`：出现零个或多个R。
- `R+`：出现一个或多个R。
- `R?`：出现零个或一个R。
- `R{n}`：R重复出现恰好n次。
- `R{n,}`：R重复出现至少n次。
- `R{n,m}`：R重复出现n到m次。
- `(R)`：即R。括号用于覆盖优先级或用于POSIX样式的子匹配。
- `R S`：R后跟S。
- `R | S`：R或S。
- `R / S`：R后跟S，但S不消耗。
- `名称`：引用该名称的正则表达式定义。当使用兼容Flex模式时，则使用`{名称}`。
- `@stag`
- `#mtag`

字符类和字面串可能包含以下转义序列：

- `\a`、`\b`、`\f`、`\n`、`\r`、`\t`、`\v`、`\\`
- 八进制转义符`\ooo`
- 十六进制转义符`\xhh`、`\uhhhh`和`\Uhhhhhhhh`。

### 配置

## 指令

## 用户接口

下列的符号（接口）可被词法分析器用于与外界交互。它们应由用户定义（哪些是必要的取决于具体场景），可以通过配置来定义，也可以是C/ C++的变量、函数、宏和其他语言结构。

# 构建

## 生成词法分析器代码

### 选项

## 编译代码

## 生成dot文件

## 生成转换图