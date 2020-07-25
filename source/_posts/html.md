---
title: html
date: 2020-07-25 17:40:49
tags: [5]
---

# 入门

HTML 指的是超文本标记语言 (**H**yper **T**ext **M**arkup **L**anguage)。

# HTML文档

HTML 文档*描述网页*，它由*包含 HTML 标签*和纯文本组成，它的扩展名是`.html`。

Web 浏览器的作用是读取 HTML 文档，并以网页的形式显示出它们。

> 用于处理HTML文档的软件称为用户代理（user agent），浏览器是最主要的用户代理。

一个典型的HTML文档结构：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
    
</body>
</html>
```

## `<!DOCTYPE>` 声明

`<!DOCTYPE>`是一个文档类型标记，用于告诉标准通用标记语言（SGML）解析器，它应该使用什么样的文档类型定义（DTD）来解析文档，并且它的根元素是什么。

> 实际上，通过`<!DOCTYPE>`可以在一个文档中混合使用多种标记语言。

HTML5：

```html
<!DOCTYPE html>
```

> 注：HTML5不是基于 SGML，因此不要求引用 DTD。

HTML 4.01：

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```

XHTML 1.0：

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

## 元数据

元数据可以向浏览器提供文档的一些信息，它包含在head元素内部。

### 字符编码

目前在大部分浏览器中，直接输出中文会出现中文乱码的情况，这时候我们就需要在头部将字符声明为 UTF-8 或 GBK。

## 内容

文档内容包含在body元素之中。

## 注释

注释以标签`<!--`开头，以`-->`结尾。浏览器会忽略这两个标签之间的一切内容。

# HTML元素

HTML元素是一种用来向浏览器说明文档内容的**结构和含义**的工具。（而CSS则是用于控制内容的**显现形式**）

HTML 元素指的是从开始标签（start tag）到结束标签（end tag）的所有代码。

**元素的内容**是开始标签与结束标签之间的内容，某些 HTML 元素可以具有空内容（empty content），例如`<div/>`，相当于`<div></div>`的简写。

有一类称为虚元素（void element）的，可以有两种表示法：

1. 只使用开始标签，例如`<hr>`；
2. 类似空元素，例如`<hr/>`。

大多数 HTML 元素可以互相嵌套。

HTML 标签是由*尖括号*包围的关键词，不区分大小写，比如 `<html>`。HTML 标签通常是*成对出现*的，标签对中的第一个标签是开始标签，第二个标签是结束标签。

![HTML元素](html/HTML元素.png)

## 元素之间的关系

父元素、子元素、后代元素、祖先元素、兄弟元素。

一个元素能以什么样的元素为父元素或子元素是有限制的，这些限制通过元素类型表现出来。

## 元素类型

元数据元素（metadata element）：用来构建HTML文档的基本结构，以及就如何处理文档向浏览器提供信息和指示。

流元素（flow element）

短语元素（phrasing element）

所有短语元素都是流元素，但并非所有流元素都是短语元素。

## 元素属性

元素可以用属性（attribute）进行配置。一个元素可以应用多个属性，属性之间以一个或几个空格分隔。多个属性的顺序未作要求。

属性总是在 HTML 元素的*开始标签*中设置，它总是以名称/值对的形式出现，比如：

![元素属性](html/元素属性.png)

属性值既可以使用双引号界定，也可以用单引号界定。

### 布尔属性

布尔属性可以不需要设定一个值，只需将属性名添加到元素中即表示`true`。

为布尔属性指定任意字符串作为其值，效果是一样的：

```html
<input disable>
<input disable="">
<input disable="disable">
```

### 自定义属性

用户可以自定义属性，但自定义属性名必须以`data-`开头。

```html
<input disabled="true" data-creator="adam" data-purpose="collection">
```

自定义属性与CSS和JavaScript结合起来很有用。

### 公共属性

# HTML实体

在 HTML 中，某些字符是预留的。如果希望正确地显示预留字符，我们必须在 HTML 源代码中使用字符实体（character entities）。

字符实体类似这样：

```html
&entity_name;

或者

&#entity_number;
```

## 常用的字符实体

| 显示结果 | 描述              | 实体名称            | 实体编号  |
| :------- | :---------------- | :------------------ | :-------- |
|          | 空格              | `&nbsp;`            | `&#160;`  |
| <        | 小于号            | `&lt;`              | `&#60;`   |
| >        | 大于号            | `&gt;`              | `&#62;`   |
| &        | 和号              | `&amp;`             | `&#38;`   |
| "        | 引号              | `&quot;`            | `&#34;`   |
| '        | 撇号              | `&apos; `(IE不支持) | `&#39;`   |
| ￠       | 分（cent）        | `&cent;`            | `&#162;`  |
| £        | 镑（pound）       | `&pound;`           | `&#163;`  |
| ¥        | 元（yen）         | `&yen;`             | `&#165;`  |
| €        | 欧元（euro）      | `&euro;`            | `&#8364;` |
| §        | 小节              | `&sect;`            | `&#167;`  |
| ©        | 版权（copyright） | `&copy;`            | `&#169;`  |
| ®        | 注册商标          | `&reg;`             | `&#174;`  |
| ™        | 商标              | `&trade;`           | `&#8482;` |
| ×        | 乘号              | `&times;`           | `&#215;`  |
| ÷        | 除号              | `&divide;`          | `&#247;`  |

> 实体名称对大小写敏感！
>
> 完整的实体符号参考，请访问 [HTML 实体符号参考手册](https://www.w3school.com.cn/tags/html_ref_entities.html)。

# SVG编程