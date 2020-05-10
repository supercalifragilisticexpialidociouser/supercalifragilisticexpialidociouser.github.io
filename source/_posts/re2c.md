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

<!--more-->

## re2c块

每个re2c块由一组正则表达式定义、配置和规则组成。

> 正则表达式定义和配置总是全局的，如果相同的定义或配置多次出现在不同的re2c块中，则它们相互覆盖。

默认标准re2c块是由下面指令创建：

```re2c
/*!re2c 
	…
*/
```

当使用了兼容Flex模式（即使用了`-F`或`--flex-syntax`选项）时，则块的格式为：

```re2c
%{ 
	…
%}
```

> 另外，还可创建可重用块，参见“可重用块”。

### 规则

规则由正则表达式后跟用户定义的动作（语义动作，在成功匹配的情况下执行的C/C++代码块）组成。

语义动作可以是用花括号`{`和`}`括起来的任意代码块（可跨多行）；也可以是没有大括号的代码块，前面带有`:=`并以不带空格的换行结束（只能单行）。

**如果多个规则匹配，则最长匹配优先。 如果多个规则匹配相同的字符串，则较早的规则优先。**

有两种特殊的规则：

- 默认规则`*`有最低优先级，并且与它在代码中的位置无关。它匹配任何代码单元并且仅消耗一个代码单元。
- EOF规则`$`匹配输入的结尾。

### 正则表达式定义

定义的默认格式：

```re2c
名称 = 正则表达式 ;
```

当使用了兼容Flex模式（即使用了`-F`或`--flex-syntax`选项）时，则定义的格式为：

```re2c
名称 正则表达式
```

定义和规则可以混杂在一起，只要求每个名称都应在使用前定义即可。例如：

```re2c
end = "\x00";

*   { return false; }
end { return in.lim - in.tok == YYMAXFILL; }

// macros
macro = ("#" | "%:") ([^\n] | "\\\n")* "\n";
macro { continue; }
```



#### 正则表达式

正则表达式语法：

- `"foo"`：区分大小写的字面串。当使用兼容Flex模式时，则使用`foo`。
- `'foo'`：不区分大小写的字面串。
- `[a-xyz]`和`[^a-xyz]`：字符类。
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

配置通常形如：`re2c:属性名=值;`。它们用于配置re2c代码。（详见官方文档：<http://re2c.org/manual/manual.html#configurations>）

## 指令

re2c指令通常形如：`/*!指令名 … */`。它们通常用于标注出不同类型的re2c代码。（详见官方文档：<http://re2c.org/manual/manual.html#directives>）

## 用户接口

用户接口实际上是一些可被词法分析器用于与外界交互的符号，称为基本原语（Basic Primitives）。它们应由用户定义（哪些是必要的取决于具体场景），可以通过配置来定义，也可以是C/C++的变量、函数、宏和其他语言结构。（详见官方文档：<http://re2c.org/manual/manual.html#user-interface>）

# 构建

## 生成词法分析器代码

```bash
$ re2c -s -o foo.c foo.re
```

`-s`：表示在生成的代码中，使用if语句代替switch语句。

### 选项

详见官方文档：<http://re2c.org/manual/manual.html#options>。

## 编译代码

```bash
$ gcc -o foo foo.c
```



## 生成dot文件

```bash
$ re2c -D -o foo.dot foo.re
```



## 生成转换图

```re2c
$ dot -Tpng -o foo.png foo.dot
```

需要额外安装graphviz。

输出格式主要有：png、svg和pdf。

# EOF处理

Re2c提供了许多处理输入结束情况的方法。使用哪种方式取决于正则表达式的复杂性、性能考虑因素、输入缓冲的需要以及各种其他因素。 EOF处理可能是re2c用户界面中最复杂的部分 - 它肯定需要了解生成的词法分析器的工作原理。但是作为回报，当更简单的方法足够时，允许用户为特定环境定制词法分析器，以避免通用方法的不必要开销。粗略地说，有四种主要方法：

- 使用哨兵（Sentinel）符号（简单而有效，但有限制）
- 带填充的边界检查（通用，但复杂）
- EOF规则：哨兵符号和边界检查的组合（通用和简单，可能比使用带填充的边界检查更高效或更低效，具体取决于目标语法）
- 使用通用API（用户定义，因此可能不正确）

## 使用哨兵符号

这是最简单，最有效的方法。它适用于输入足够小以适应连续内存缓冲区且存在自然“哨兵”符号的情况 。哨兵符号永远不会出现在格式良好的输入中，因此它可以附加在输入结尾处，并由词法分析器用作终止信号。

 哨兵方法非常有效，因为词法分析器不需要对输入结束执行任何额外的检查 —— 它自然地作为处理下一个字符的一部分。

```re2c
#include <assert.h>

static int lex(const char *YYCURSOR)
{
    int count = 0;
loop:
    /*!re2c
    re2c:define:YYCTYPE = char;
    re2c:yyfill:enable = 0;  //不生成YYFILL(N)代码

    *      { return -1; }
    [\x00] { return count; }  //使用\x00作为哨兵字符
    [a-z]+ { ++count; goto loop; }
    [ ]+   { goto loop; }

    */
}

int main()
{
    assert(lex("") == 0);
    assert(lex("one two three") == 3);
    assert(lex("one two 123?") == -1);
    return 0;
}
```

## 带填充的边界检查

边界检查是一种通用方法：它可以与任何输入语法一起使用。基本思路很简单：我们需要在读取下一个输入字符之前检查输入的结束。但是，如果以直接的方式实现，这将是非常低效的：检查每个输入字符将导致重大减速。 Re2c通过仅在词法分析器的某些关键状态中生成检查来避免减速。更确切地说，re2c计算底层DFA的强连接组件（SCC，大致对应于循环），并且每个SCC仅生成几个检查（通常只有一个，但通常足以使SCC非周期性）。

检查的形式为`(YYLIMIT - YYCURSOR) < n`，其中`n`是相应SCC中简单路径的最大长度。如果这个条件为真，则词法分析器调用`YYFILL(n)`，它必须提供至少n个输入字符，否则不返回。在检查后词法分析器继续运行时，可以确定无需检查即可安全读取下n个字符。

这种方法显着减少了检查次数（并且因此使词法分析器更快），但它有一个缺点。由于词法分析器一次检查多个字符，它可能会在SCC中存在少量与短路径相对应的剩余输入字符（小于n）的情况下结束，但是由于边界检查，词法分析器无法继续处理，并且`YYFILL`无法提供更多字符，因为它是输入的结束。为了解决这个问题，re2c要求在输入结尾附加由伪字符组成的额外填充（Padding）。填充长度应为`YYMAXFILL`，它等于`YYFILL`的最大`n`参数，并且必须由re2c使用`/*!max:re2c*/` 指令生成。伪字符不应该构成有效的词素（Lexeme）后缀，否则词法分析器可能被欺骗以匹配伪词素。通常，使用`NULL`字符进行填充是个好主意。

```re2c
#include <assert.h>
#include <stdlib.h>
#include <string.h>

/*!max:re2c*/

static int lex(const char *str)
{
    const size_t len = strlen(str);
    char *buf = malloc(len + YYMAXFILL);
    memcpy(buf, str, len);
    memset(buf + len, 0, YYMAXFILL); //使用0来填充输入结尾

    const char *YYCURSOR = buf;
    const char *YYLIMIT = buf + len + YYMAXFILL;
    int count = 0;

loop:
    /*!re2c
    re2c:define:YYCTYPE = char;
    re2c:define:YYFILL:naked = 1;
    re2c:define:YYFILL = "goto error;";

    *                         { goto error; }
    [\x00]                    { if (YYCURSOR == YYLIMIT) goto end; else goto error; }
    [a-z]+                    { ++count; goto loop; }
    ['] ([^'] | [\\]['])* ['] { ++count; goto loop; }  //"aha\0ha"是合法的
    [ ]+                      { goto loop; }

    */
error:
    count = -1;
end:
    free(buf);
    return count;
}

int main()
{
    assert(lex("") == 0);
    assert(lex("one two three") == 3);
    assert(lex("one two 123?") == -1);
    assert(lex("one 'two' 'th\\'ree' '123?' ''") == 5);
    assert(lex("one 'two' 'three") == -1); //缺失右单引号，匹配*规则
    return 0;
}
```

注意，单引号字符串的语法规则允许在词素中间使用任意符号，因此语法中没有自然的哨兵符号。

在此示例中，我们不使用缓冲区回填，因此`YYFILL`定义为只返回错误。请注意，只有在词法分析器到达填充（Padding）后才会调用`YYFILL`，因为只有这样才能满足检查条件。

## EOF规则

EOF规则`$`在版本1.2中引入。

基本思路是：随便指定一个字符作为哨兵符号，只有当哨兵符号被匹配（更准确地说，如果包含它的符号类被匹配）才会执行边界检查。检查的代码形如`YYLIMIT <= YYCURSOR`的条件，如果不满足这个条件，那么哨兵符号只是一个普通的输入字符，词法分析器继续。否则，这是一个真正的哨兵，并且词法分析器调用`YYFILL(n)`。如果`YYFILL`返回零，则词法分析器假定它有更多输入并尝试重新匹配。否则`YYFILL`返回非零，并且词法分析器知道它已到达输入的结尾。此时有三种可能性：

- 首先，它可能已经匹配了较短的词素。在这种情况下，它只是回滚到最后一个接受状态。
- 其次，它可能已经消耗了一些字符，但未能匹配。在这种情况下，它会回退到默认规则`*`。
- 最后，它必定处于初始状态（应该是指刚刚完成上一个词法单元的识别，准备进入下一个词法单元识别的时点）。在这情况下，它匹配EOF规则`$`。

```re2c
#include <assert.h>
#include <string.h>

static int lex(const char *str)
{
    const char *YYCURSOR = str;
    const char *YYLIMIT = str + strlen(str);
    int count = 0;

loop:
    /*!re2c
    re2c:define:YYCTYPE = char;
    re2c:yyfill:enable = 0;
    re2c:eof = 0;

    *                         { return -1; }
    $                         { return count; }
    [a-z]+                    { ++count; goto loop; }
    ['] ([^'] | [\\]['])* ['] { ++count; goto loop; }
    [ ]+                      { goto loop; }

    */
}

int main()
{
    assert(lex("") == 0);
    assert(lex("one two three") == 3);
    assert(lex("one two 123?") == -1);
    assert(lex("one 'two' 'th\\'ree' '123?' ''") == 5);
    assert(lex("one 'two' 'three") == -1);
    return 0;
}
```

## 使用通用API

通用API可以与任何上述方法一起使用。 它还允许通过在基本原语中放置EOF检查来使用用户定义的方法。 通常这是`YYSKIP`（在前进到下一个输入字符时执行检查）或`YYPEEK`（在读取下一个输入字符时执行检查）。 由此产生的方法效率很低，因为它们检查每个输入字符。 但是，在输入无法缓冲或填充且末尾不包含标记字符的情况下，它们非常有用。 在使用这种特殊方法时应该谨慎，因为很容易忽略一些极端情况并得到一种只能部分工作的方法。 此外，应该注意的是，并非所有内容都可以通过通用API表达：例如，不可能重新实现EOF规则的工作方式（特别是，不可能在成功`YYFILL`之后重新匹配字符）。

```re2c
#include <assert.h>
#include <string.h>

#define YYPEEK() *cur
#define YYSKIP() if (++cur > lim) return -1
static int lex(const char *str)
{
    const char *cur = str;
    const char *lim = str + strlen(str) + 1;
    int count = 0;

loop:
    /*!re2c
    re2c:define:YYCTYPE = char;
    re2c:yyfill:enable = 0;
    re2c:flags:input = custom;

    *                         { return -1; }
    [\x00]                    { return cur == lim ? count : -1; }
    [a-z]+                    { ++count; goto loop; }
    ['] ([^'] | [\\]['])* ['] { ++count; goto loop; }
    [ ]+                      { goto loop; }

    */
}

int main()
{
    assert(lex("") == 0);
    assert(lex("one two three") == 3);
    assert(lex("one two 123?") == -1);
    assert(lex("one 'two' 'th\\'ree' '123?' ''") == 5);
    assert(lex("one 'two' 'three") == -1);
    return 0;
}
```

注意，如果语法更复杂，当两个规则重叠并且EOF检查在更短的词素已经匹配后失败时（例如，在我们的示例中没有重叠规则），此方法可能不起作用。

# 缓冲区回填

当输入无法一次性映射到内存中时，就会出现缓冲需求：要么是太大，要么是以流式方式（如从套接字读取）。 在这种情况下，通常的技术是分配一个固定大小的内存缓冲区，并以适合缓冲区的块（Chunks）来处理输入。 当当前块被处理时，它被移出并移入新数据。实际上它稍微复杂一些，因为词法分析器状态不是由单个输入位置组成，而是由一组相互关联的位置组成：

- 游标（Cursor）：要读取的下一个输入字符（即默认API中的`YYCURSOR`或通用API中的`YYSKIP` 或 `YYPEEK`）
- 界限（Limit）：最后一个可用的输入字符之后的位置（即默认API中的`YYLIMIT`，由通用API中的`YYLESSTHAN`隐式处理）
- 标记器（Marker）：最近匹配的位置（如果有）（即默认API中的`YYMARKER`或通用API中的`YYBACKUP` 或`YYRESTORE`）
- token：当前词素的开头（隐含在re2c API中，因为它不是正常词法分析器操作所必需的，可以由用户定义和更新）
- 上下文标记器：尾随（trailing）上下文的位置（即默认API中的`YYCTXMARKER`或通用API中的`YYBACKUPCTX` 或`YYRESTORECTX`）
- 标签变量（Tag Variables）：子匹配位置（用`/*!stags:re2c*/` 和 `/*!mtags:re2c*/` 指令，以及通用API中的`YYSTAGP`、 `YYSTAGN`、`YYMTAGP`和`YYMTAGN`定义）

并非所有这些位置都在每种情况下使用，但如果使用，它们必须由`YYFILL`更新。

所有活动位置都包含在token和游标之间的段中，因此缓冲区起始处至token之间的所有内容都可以被丢弃。从token到界限（limit）的段应该移动到缓冲区的开头，缓冲区结尾处的空闲空间应填入新数据。 为了避免频繁的`YYFILL`调用，最好填入尽可能多的输入字符（即使较少的字符可能足以恢复词法分析器）。 根据使用的EOF处理方法，`YYFILL`实现的细节略有不同：EOF规则的情况比带填充的边界检查的情况稍微简单一些。 另请注意，如果使用`-f`或`--storable-state`选项，则`YYFILL`的语义略有不同（在关于可存储状态的部分中有所描述）。

## 带EOF规则的YYFILL

YYFILL调用由默认API中的条件`YYLIMIT <= YYCURSOR`和通用API中的`YYLESSTHAN()`触发。

如果使用EOF规则，则`YYFILL`是一个类似函数的原语，它不接受任何参数并返回一个整数值（ 非零返回值表示`YYFILL`失败）。

成功的`YYFILL`调用必须提供至少一个字符并相应地调整输入位置。 界限必须总是被设置为缓冲区中的最后一个输入之后的位置，并且界限位置处的字符必须是`re2c:eof`配置指定的哨兵符号。 下图显示了`YYFILL`调用之前和之后缓冲区中输入位置的相对位置（哨兵符号标记为`＃`，第二张图显示了没有足够的输入填充整个缓冲区的情况）。

```
               <-- shift -->
             >-A------------B---------C-------------D#-----------E->
             buffer       token    marker         limit,
                                                  cursor
>-A------------B---------C-------------D------------E#->
             buffer,  marker        cursor        limit
             token

               <-- shift -->
             >-A------------B---------C-------------D#--E (EOF)
             buffer       token    marker         limit,
                                                  cursor
>-A------------B---------C-------------D---E#........
             buffer,  marker       cursor limit
             token
```

例子：

```re2c
#include <stdio.h>
#include <string.h>

#define SIZE 4096

typedef struct {
    FILE *file;
    char buf[SIZE + 1], *lim, *cur, *tok;
    int eof;
} Input;

static int fill(Input *in)
{
    if (in->eof) {
        return 1;
    }
    const size_t free = in->tok - in->buf;
    if (free < 1) {
        return 2;
    }
    memmove(in->buf, in->tok, in->lim - in->tok);
    in->lim -= free;
    in->cur -= free;
    in->tok -= free;
    in->lim += fread(in->lim, 1, free, in->file);
    in->lim[0] = 0;
    in->eof |= in->lim < in->buf + SIZE;
    return 0;
}

static void init(Input *in, FILE *file)
{
    in->file = file;
    in->cur = in->tok = in->lim = in->buf + SIZE;
    in->eof = 0;
    fill(in);
}

#define YYFILL() fill(in)
static int lex(Input *in)
{
    int count = 0;
loop:
    in->tok = in->cur;
    /*!re2c
    re2c:define:YYCTYPE = char;
    re2c:define:YYCURSOR = in->cur;
    re2c:define:YYLIMIT = in->lim;
    re2c:eof = 0;

    *                         { return -1; }
    $                         { return count; }
    [a-z]+                    { ++count; goto loop; }
    ['] ([^'] | [\\]['])* ['] { ++count; goto loop; }
    [ ]+                      { goto loop; }

    */
}

int main()
{
    FILE *f = fopen("input.txt", "rb");
    if (!f) return 1;

    Input in;
    init(&in, f);
    printf("count: %d\n", lex(&in));

    fclose(f);
    return 0;
}
```



## 带填充的YYFILL

在默认情况下（即当不使用EOF规则时），`YYFILL`是一个类似函数的原语，它接受单个参数并且不返回任何值。 `YYFILL`调用由默认API中的条件`(YYLIMIT - YYCURSOR) < n`和通用API中的`YYLESSTHAN(n)`触发。传递给`YYFILL`的参数是必须提供的最小字符数。如果没有这样做，`YYFILL`不能返回词法分析器（因此最好将其作为一个宏，在失败时从调用函数返回）。如果成功调用`YYFILL`，则必须将界限位置设置为缓冲区中最后一个输入位置之后的一个位置，或者设置为`YYMAXFILL`个填充（Padding）的结尾处（如果`YYFILL`已成功读取至少`n`个字符，但不足以填充整个缓冲）。下图显示了`YYFILL`调用之前和之后缓冲区中输入位置的相对位置（第二张图片上的`YYMAXFILL`个填充用`#`符号标记）。

```
               <-- shift -->                 <-- need -->
             >-A------------B---------C-----D-------E---F--------G->
             buffer       token    marker cursor  limit

>-A------------B---------C-----D-------E---F--------G->
             buffer,  marker cursor               limit
             token

               <-- shift -->                 <-- need -->
             >-A------------B---------C-----D-------E-F        (EOF)
             buffer       token    marker cursor  limit

>-A------------B---------C-----D-------E-F###############
             buffer,  marker cursor                   limit
             token                        <- YYMAXFILL ->
```

例子：

```re2c
#include <stdio.h>
#include <string.h>

/*!max:re2c*/
#define SIZE 4096

typedef struct {
    FILE *file;
    char buf[SIZE + YYMAXFILL], *lim, *cur, *tok;
    int eof;
} Input;

static int fill(Input *in, size_t need)
{
    if (in->eof) {
        return 1;
    }
    const size_t free = in->tok - in->buf;
    if (free < need) {
        return 2;
    }
    memmove(in->buf, in->tok, in->lim - in->tok);
    in->lim -= free;
    in->cur -= free;
    in->tok -= free;
    in->lim += fread(in->lim, 1, free, in->file);
    if (in->lim < in->buf + SIZE) {
        in->eof = 1;
        memset(in->lim, 0, YYMAXFILL);
        in->lim += YYMAXFILL;
    }
    return 0;
}

static void init(Input *in, FILE *file)
{
    in->file = file;
    in->cur = in->tok = in->lim = in->buf + SIZE;
    in->eof = 0;
    fill(in, 1);
}

#define YYFILL(n) if (fill(in, n) != 0) return -1
static int lex(Input *in)
{
    int count = 0;
loop:
    in->tok = in->cur;
    /*!re2c
    re2c:define:YYCTYPE = char;
    re2c:define:YYCURSOR = in->cur;
    re2c:define:YYLIMIT = in->lim;

    *                         { return -1; }
    [\x00]                    { return (YYMAXFILL == in->lim - in->tok) ? count : -1; }
    [a-z]+                    { ++count; goto loop; }
    ['] ([^'] | [\\]['])* ['] { ++count; goto loop; }
    [ ]+                      { goto loop; }

    */
}

int main()
{
    FILE *f = fopen("input.txt", "rb");
    if (!f) return 1;

    Input in;
    init(&in, f);
    printf("count: %d\n", lex(&in));

    fclose(f);
    return 0;
}
```

# 可存储状态

使用`-f`或 `--storable-state`选项re2c将生成一个词法分析器，可以存储其当前状态，返回到调用者，然后在其停止的位置恢复操作。

