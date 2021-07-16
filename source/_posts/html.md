---
title: html
date: 2020-07-25 17:40:49
tags: [5]
---

[HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML) 指的是超文本标记语言 (**H**yper **T**ext **M**arkup **L**anguage)，是用于构造网页及其内容的代码。

# HTML文档

HTML 文档*描述网页内容的结构*，它由一系列HTML元素组成，它的扩展名是`.html`。

> 最佳实践：完全以小写字母命名文件夹和文件，不要有空格，并用连字符分隔单词。

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

# 文档类型

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

# 文档语言

```html
<html lang="en-US">
<html lang="zh-Hans"> （简体中文）
```

这在很多方面都很有用。如果设置了语言，搜索引擎将更有效地为您的 HTML 文档编制索引，并且对于使用屏幕阅读器的人更有帮助。

您还可以将文档的子部分设置为被识别为不同的语言。例如：

```html
<p>Japanese example: <span lang="ja">ご飯が熱い。</span>.</p>
```

语言代码由[ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1)标准定义。您可以在 HTML 和 XML中的[语言标签中](https://www.w3.org/International/articles/language-tags/)找到有关它们的更多信息。

在父层元素声明的语言信息，将被子层元素所继承，除非这些元素显式声明了不同的语言。

# HTML注释

注释以标签`<!--`开头，以`-->`结尾。浏览器会忽略这两个标签之间的一切内容。

# HTML元素

HTML元素是一种用来向浏览器说明文档内容的**结构和含义**的工具。（而CSS则是用于控制内容的**显现形式**）

HTML 元素指的是从开始标签（start tag）到结束标签（end tag）的所有代码。

HTML 标签是由*尖括号*包围的关键词，不区分大小写，比如 `<html>`。HTML 标签通常是*成对出现*的，标签对中的第一个标签是开始标签，第二个标签是结束标签。

开始标签与结束标签之间的内容是**元素的内容**。

![HTML元素](html/HTML元素.png)

## 元素嵌套

大多数 HTML 元素可以互相嵌套，形成父元素、子元素、后代元素、祖先元素、兄弟元素关系。

一个元素能以什么样的元素为父元素或子元素是有限制的，这些限制通过元素类别表现出来。

## 元素类别

在 HTML 中有两个重要的元素类别需要了解：块级元素和内联元素。

- 块级元素在页面上形成一个可见的块。块级元素出现在它前面的内容之后的新行上。块级元素之后的任何内容也会出现在新行上。块级元素通常是页面上的结构元素。块级元素不会嵌套在行内元素内，但它可能嵌套在另一个块级元素内。
- 内联元素包含在块级元素中，并且仅围绕文档内容的一小部分（而不是整个段落或内容分组）。内联元素不会导致文档中出现新行。它通常与文本一起使用。

> **注意**：HTML5 重新定义了元素类别：请参阅[元素内容类别](https://html.spec.whatwg.org/multipage/indices.html#element-content-categories)。虽然这些定义比它们的前辈更准确、更明确，但新定义比*块*和*内联*更难理解*。*本文将保留这两个术语。

## 空元素

某些 HTML 元素没有内容，称为空元素（empty content 或 void element）。

空元素有两种表示法：

1. 只使用开始标签（HTML），例如`<hr>`；
2. `<…/>`（XHTML），例如`<hr/>`。

空元素有：`<area>`、`<base>`、`<br>`、`<col>`、`<colgroup>`、`<command>`、`<embed>`、`<hr>`、`<img>`、`<input>`、`<keygen>`、`<link>`、`<meta>`、`<param>`、`<source>`、`<track>`、`<wbr>`。

## 元素属性

元素可以用属性（attribute）进行配置。一个元素可以应用多个属性，属性之间以一个或几个空格分隔。多个属性的顺序未作要求。

属性总是在 HTML 元素的*开始标签*中设置，它总是以名称/值对的形式出现，比如：

![元素属性](html/元素属性.png)

属性值既可以使用双引号界定，也可以用单引号界定。

> 不包含 ASCII 空格或任何`"` `'` ``` `=` `<` `>`等字符的简单属性值可以保持不加引号，但建议对任何属性值都加引号

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

公共属性可用于任何 HTML 元素。

| 属性                                                         | 描述                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------- |
| [accesskey](https://www.w3school.com.cn/tags/att_standard_accesskey.asp) | 规定激活元素的快捷键。                                 |
| [class](https://www.w3school.com.cn/tags/att_standard_class.asp) | 规定元素的一个或多个类名（引用样式表中的类）。         |
| [contenteditable](https://www.w3school.com.cn/tags/att_global_contenteditable.asp) | 规定元素内容是否可编辑。                               |
| [contextmenu](https://www.w3school.com.cn/tags/att_global_contextmenu.asp) | 规定元素的上下文菜单。上下文菜单在用户点击元素时显示。 |
| [data-*](https://www.w3school.com.cn/tags/att_global_data.asp) | 用于存储页面或应用程序的私有定制数据。                 |
| [dir](https://www.w3school.com.cn/tags/att_standard_dir.asp) | 规定元素中内容的文本方向。                             |
| [draggable](https://www.w3school.com.cn/tags/att_global_draggable.asp) | 规定元素是否可拖动。                                   |
| [dropzone](https://www.w3school.com.cn/tags/att_global_dropzone.asp) | 规定在拖动被拖动数据时是否进行复制、移动或链接。       |
| [hidden](https://www.w3school.com.cn/tags/att_global_hidden.asp) | 规定元素仍未或不再相关。                               |
| [id](https://www.w3school.com.cn/tags/att_standard_id.asp)   | 规定元素的唯一 id。                                    |
| [lang](https://www.w3school.com.cn/tags/att_standard_lang.asp) | 规定元素内容的语言。                                   |
| [spellcheck](https://www.w3school.com.cn/tags/att_global_spellcheck.asp) | 规定是否对元素进行拼写和语法检查。                     |
| [style](https://www.w3school.com.cn/tags/att_standard_style.asp) | 规定元素的行内 CSS 样式。                              |
| [tabindex](https://www.w3school.com.cn/tags/att_standard_tabindex.asp) | 规定元素的 tab 键次序。                                |
| [title](https://www.w3school.com.cn/tags/att_standard_title.asp) | 规定有关元素的额外信息。                               |
| [translate](https://www.w3school.com.cn/tags/att_global_translate.asp) | 规定是否应该翻译元素内容。                             |

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

# HTML头部

HTML 头部是[`<head>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head)元素的内容——与元素的内容`<body>`（在浏览器中加载时显示在页面上）不同，头部的内容不显示在页面上。相反，头部的工作是包含有关文档的元数据。

## 文档标题

[`<title>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/title)元素是表示整个 HTML 文档（而不是文档正文）标题的元数据。

## 元数据

元数据（[`<meta>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta)元素）可以向浏览器提供文档的一些信息。

### 指定文档的字符编码

目前在大部分浏览器中，直接输出中文会出现中文乱码的情况，这时候我们就需要在头部将字符声明为 UTF-8 或 GBK。

```html
<meta charset="utf-8">
```

### 添加作者和描述

许多`<meta>`元素包括`name`和`content`属性：

- `name`指定元元素的类型；它包含什么类型的信息。
- `content` 指定实际的元内容。

```html
<meta name="author" content="Chris Mills">
<meta name="description" content="The MDN Web Docs Learning Area aims to provide
complete beginners to the Web with all they need to know to get
started with developing web sites and applications.">
```

> **注意**：许多`<meta>`功能不再使用。例如，`<meta name="keywords" content="fill, in, your, keywords, here">`。

## 网站图标

```html
<link rel="icon" href="favicon.ico" type="image/x-icon">
```

网站图标的格式可以是：`.ico`、`.gif`或`.png`，但使用 ICO 格式将确保它可以在 Internet Explorer 6 中使用。

> **注意：**如果您的站点使用内容安全策略 (CSP) 来增强其安全性，则该策略适用于网站图标。如果您遇到网站图标未加载的问题，请确认[`Content-Security-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)标头的[`img-src`指令](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/img-src)没有阻止对其进行访问。

## 应用CSS

[`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link)

```html
<link rel="stylesheet" href="my-css-file.css">
```

## 应用JavaScript

[`<script>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script)

```html
<script src="my-js-file.js" defer></script>
```

`defer`指示浏览器在页面完成解析 HTML 后运行 JavaScript。这很有用，因为它可以确保在 JavaScript 运行之前加载所有 HTML（脚本和 HTML 仍是同时加载。脚本将按照它们出现在页面上的顺序加载（确保了脚本之间的依赖顺序），它们在页面内容全部加载后才会运行），这样您就不会因为 JavaScript 尝试访问页面上尚不存在的 HTML 元素而导致错误。

> 这个问题的一个老式解决方案曾经是将您的脚本元素放在正文的底部（例如，就在`</body>`标签之前），这样它就会在所有 HTML 被解析后加载。此解决方案的问题在于，在加载 HTML DOM 之前完全阻塞脚本的加载/解析。在包含大量 JavaScript 的大型网站上，这可能会导致严重的性能问题，从而降低网站速度。

除了延迟运行脚本外，还有一个异步运行脚本——`async`属性。它将在遇到`<script>`时下载脚本而不会阻塞页面。但是，一旦下载完成，脚本就会执行，从而阻塞页面呈现。您无法保证脚本将以任何特定顺序运行。在页面中的脚本彼此独立运行且不依赖于页面上的其他脚本时才适合使用`async`。

以下是不同脚本加载方法的直观表示以及这对您的页面的意义：

![异步与延迟](html/async-defer.jpg)

> **注意**：该`<script>`元素可能看起来像一个空元素，但它不是，因此需要一个结束标记。

除了指向外部脚本文件之外，您还可以选择将脚本放在`<script>`元素中。

```html
<script>
    document.getElementById('demo').innerHTML = '我的第一段JavaScript';
</script>
```

> 旧的JavaScript例子也许会使用`type`属性，例如`<script type='text/javascript'>`。现在已经不需要了，JavaScript已经是HTML中的默认脚本语言了。

# HTML正文

文档正文包含在[`<body>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/body)元素之中。

## HTML中的空格

无论您在 HTML 元素内容中使用多少空格（包括换行符），HTML 解析器在呈现代码时会将每个空格序列缩减为一个空格。

# 排版

## 标题

有六个标题元素：[`<h1>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Heading_Elements)（最好每页使用一个——这是顶级标题）、[`<h2>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Heading_Elements)、[`<h3>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Heading_Elements)、[`<h4>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Heading_Elements)、[`<h5>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Heading_Elements)和[`<h6>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Heading_Elements)。每个元素代表文档中不同级别的内容；`<h1>`表示主标题、`<h2>`表示副标题、`<h3>`表示副副标题等。

```html
<h1>I am the title of the story.</h1>
```

## 段落

[`<p>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/p)

```html
<p>I am a paragraph, oh yes I am.</p>
```

## 列表

### 无序列表

无序列表用于标记与条目顺序无关的项目列表。[`<ul>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ul)  [`<li>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/li)

```html
<ul>
    <li>milk</li>
    <li>eggs</li>
    <li>bread</li>
    <li>hummus</li>
</ul>
```

### 有序列表

[`<ol>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ol)

```html
<ol>
    <li>Drive to the end of the road</li>
    <li>Turn right</li>
    <li>Go straight across the first two roundabouts</li>
    <li>Turn left at the third roundabout</li>
    <li>The school is on your right, 300 meters up the road</li>
</ol>
```

### 描述列表

列表的目的是标记一组条目及其相关描述，例如术语和定义、问题和答案等。[`<dl>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl) [`<dt>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dt) [`<dd>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dd)

```html
<dl>
    <dt>soliloquy</dt>
    <dd>In drama, where a character speaks to themselves, representing their inner thoughts or feelings and in the process relaying them to the audience (but not to other characters.)</dd>
    <dt>monologue</dt>
    <dd>In drama, where a character speaks their thoughts out loud to share them with the audience and any other characters present.</dd>
    <dt>aside</dt>
    <dd>In drama, where a character shares a comment only with the audience for humorous or dramatic effect. This is usually a feeling, thought, or piece of additional background information.</dd>
</dl>
```

请注意，允许一个术语具有多个描述，例如：

```html
<dl>
    <dt>aside</dt>
    <dd>In drama, where a character shares a comment only with the audience for humorous or dramatic effect. This is usually a feeling, thought, or piece of additional background information.</dd>
    <dd>In writing, a section of content that is related to the current topic, but doesn't fit directly into the main flow of content so is presented nearby (often in a box off to the side.)</dd>
</dl>
```



### 列表嵌套

```html
<ol>
    <li>Remove the skin from the garlic, and chop coarsely.</li>
    <li>Remove all the seeds and stalk from the pepper, and chop coarsely.</li>
    <li>Add all the ingredients into a food processor.</li>
    <li>Process all the ingredients into a paste.
        <ul>
            <li>If you want a coarse "chunky" hummus, process it for a short time.</li>
            <li>If you want a smooth hummus, process it for a longer time.</li>
        </ul>
    </li>
</ol>
```

## 引文（quotations）

### 块引文

如果块级内容的一部分（无论是一个段落、多个段落、一个列表等）是从其他地方引用的，您应该将其包装在一个[`<blockquote>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote)元素中以表示这一点，并将指向引文来源的 URL作为[`cite`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote#attr-cite)属性的值。

```html
<blockquote cite="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote">
    <p>The <strong>HTML <code>&lt;blockquote&gt;</code> Element</strong> (or <em>HTML Block Quotation Element</em>) indicates that the enclosed text is an extended quotation.</p>
</blockquote>
```

### 内联引文

内联引用的工作方式完全相同，只是它们使用了[`<q>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/q)元素。

```html
<p>The quote element — <code>&lt;q&gt;</code> — is <q cite="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/q">intended for short quotations that don't require paragraph breaks.</q></p>
```

### 参考

[`<cite>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/cite)元素用来引用参考的资料，例如：书、论文、文章、博客、电影、帖子等。

```html
<p>According to the <a href="/en-US/docs/Web/HTML/Element/blockquote">
    <cite>MDN blockquote page</cite></a>:
</p>

<blockquote cite="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote">
    <p>The <strong>HTML <code>&lt;blockquote&gt;</code> Element</strong> (or <em>HTML Block Quotation Element</em>) indicates that the enclosed text is an extended quotation.</p>
</blockquote>

<p>The quote element — <code>&lt;q&gt;</code> — is <q cite="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/q">intended for short quotations that don't require paragraph breaks.</q> -- <a href="/en-US/docs/Web/HTML/Element/q"><cite>MDN q page</cite></a>.</p>
```



## 强调

在 HTML 中，我们使用[`<em>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/em)元素来表示强调，使用[`<strong>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/strong)元素来表示更强的强调。

```html
<p>This liquid is <strong>highly toxic</strong> —
if you drink it, <strong>you may <em>die</em></strong>.</p>
```

默认情况下，浏览器将`<em>`样式设置为斜体，将`<strong>`样式设置为粗体文本，但您不应纯粹使用这些标签来获得斜体或粗体样式。

## 斜体、粗体、下划线

像这样[`<b>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/b)（粗体）、[`<i>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/i)（斜体）和[`<u>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/u)（下划线）只影响表现而不影响语义的元素被称为**表现元素**，不应再使用。因为正如我们之前看到的，语义对可访问性、搜索引擎优化等非常重要。

最佳实践：

- [`<i>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/i) ：外来词、分类名称、技术术语、思想...
- [`<b>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/b) ：关键词、产品名称、引导句......
- [`<u>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/u) ：专有名称、拼写错误......

由于超链接经常使用下划线，因此最好使用 CSS 将`<u>`的默认下划线样式更改为更适合内容的样式。

```html
<!-- scientific names -->
<p>
    The Ruby-throated Hummingbird (<i>Archilochus colubris</i>)
    is the most common hummingbird in Eastern North America.
</p>

<!-- foreign words -->
<p>
    The menu was a sea of exotic words like <i lang="uk-latn">vatrushka</i>,
    <i lang="id">nasi goreng</i> and <i lang="fr">soupe à l'oignon</i>.
</p>

<!-- a known misspelling -->
<p>
    Someday I'll learn how to <u style="text-decoration-line: underline; text-decoration-style: wavy;">spel</u> better.
</p>

<!-- Highlight keywords in a set of instructions -->
<ol>
    <li>
        <b>Slice</b> two pieces of bread off the loaf.
    </li>
    <li>
        <b>Insert</b> a tomato slice and a leaf of
        lettuce between the slices of bread.
    </li>
</ol>
```

## 缩写

元素[`<abbr>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/abbr) 的内容是缩略词，它的`title`属性提供了该缩略词的完整形式。当鼠标悬停在缩略词上时，将出现工具提示，显示`title`属性的值。

```html
<p>We use <abbr title="Hypertext Markup Language">HTML</abbr> to structure our web documents.</p>
```

## 联系信息

 [`address`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/address)

```html
<address>
  <p>
    Chris Mills<br>
    Manchester<br>
    The Grim North<br>
    UK
  </p>

  <ul>
    <li>Tel: 01234 567 890</li>
    <li>Email: me@grim-north.co.uk</li>
  </ul>
</address>

<address>
  <p>Page written by <a href="../authors/chris-mills/">Chris Mills</a>.</p>
</address>
```

## 上下标

[`<sup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/sup)（上标）[`<sub>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/sub)（下标）

```html
<p>My birthday is on the 25<sup>th</sup> of May 2001.</p>
<p>Caffeine's chemical formula is C<sub>8</sub>H<sub>10</sub>N<sub>4</sub>O<sub>2</sub>.</p>
```

## 代码

有许多元素可用于使用 HTML 标记计算机代码：

- [`<code>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/code)：用于标记通用的计算机代码片段。
- [`<pre>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/pre)：用于保留空格（通常是代码块）——如果您在文本中使用缩进或多余的空格，浏览器将忽略它并且您将不会在呈现的页面上看到它。`<pre></pre>`但是，如果您将文本包装在标签中，您的空白将与您在文本编辑器中看到的相同。
- [`<var>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/var): 用于专门标记变量名称。
- [`<kdb>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/kbd)：用于标记输入到计算机的键盘（和其他类型）输入。
- [`<samp>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/samp)：用于标记计算机程序的输出。

```html
<pre><code>var para = document.querySelector('p');

para.onclick = function() {
  alert('Owww, stop poking me!');
}</code></pre>

<p>You shouldn't use presentational elements like <code>&lt;font&gt;</code> and <code>&lt;center&gt;</code>.</p>

<p>In the above JavaScript example, <var>para</var> represents a paragraph element.</p>

<p>Select all the text with <kbd>Ctrl</kbd>/<kbd>Cmd</kbd> + <kbd>A</kbd>.</p>

<pre>$ <kbd>ping mozilla.org</kbd>
<samp>PING mozilla.org (63.245.215.20): 56 data bytes
64 bytes from 63.245.215.20: icmp_seq=0 ttl=40 time=158.233 ms</samp></pre>
```

## 时间和日期

[`<time>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)元素以机器可读格式标记时间和日期。

```html
<!-- Standard simple date -->
<time datetime="2016-01-20">20 January 2016</time>
<!-- Just year and month -->
<time datetime="2016-01">January 2016</time>
<!-- Just month and day -->
<time datetime="01-20">20 January</time>
<!-- Just time, hours and minutes -->
<time datetime="19:30">19:30</time>
<!-- You can do seconds and milliseconds too! -->
<time datetime="19:30:01.856">19:30:01.856</time>
<!-- Date and time -->
<time datetime="2016-01-20T19:30">7.30pm, 20 January 2016</time>
<!-- Date and time with timezone offset -->
<time datetime="2016-01-20T19:30+01:00">7.30pm, 20 January 2016 is 8.30pm in France</time>
<!-- Calling out a specific week number -->
<time datetime="2016-W04">The fourth week of 2016</time>
```

## 内联非语义元素

[`<span>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/span)是一个内联非语义元素，只有当您想不出更好的语义文本元素来包装您的内容，或者不想添加任何特定含义时，才应该使用它。

```html
<p>The King walked drunkenly back to his room at 01:00, the beer doing nothing to aid him as he staggered through the door <span class="editor-note">[Editor's note: At this point in the play, the lights should be down low]</span>.</p>
```

## 换行

[`<br>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/br)在段落中换行。

```html
<p>There once was a man named O'Dell<br>
Who loved to write HTML<br>
But his structure was bad, his semantics were sad<br>
and his markup didn't read very well.</p>
```

## 水平线

[`<hr>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/hr)元素在文档中创建一条水平线，表示文本中的主题更改（例如主题或场景的更改）。

```html
<p>Ron was backed into a corner by the marauding netherbeasts. Scared, but determined to protect his friends, he raised his wand and prepared to do battle, hoping that his distress call had made it through.</p>
<hr>
<p>Meanwhile, Harry was sitting at home, staring at his royalty statement and pondering when the next spin off series would come out, when an enchanted distress letter flew through his window and landed in his lap. He read it hazily and sighed; "better get back to work then", he mused.</p>
```



# 超链接

超链接允许我们将文档链接到其他文档或资源，链接到文档的特定部分，或在某个网址上提供应用程序。

> **注意**：超链接的URL 可以指向 HTML 文件、文本文件、图像、文本文档、视频和音频文件，或任何存在于 Web 上的内容。如果网络浏览器不知道如何显示或处理文件，它会询问您是否要打开文件（在这种情况下，打开或处理文件的职责会传递给设备上合适的本机应用程序）或下载文件（在这种情况下，您可以稍后尝试处理）。
>
> 最佳实践：
>
> - 在链接到*同一网站*内的其他位置时，您应该尽可能使用相对链接。当您链接到另一个网站时，您需要使用绝对链接。当您使用绝对 URL 时，浏览器首先在域名系统 ( [DNS](https://developer.mozilla.org/en-US/docs/Glossary/DNS) )上查找服务器的真实位置。然后它转到该服务器并找到正在请求的文件。使用相对 URL，浏览器只查找在同一服务器上请求的文件。如果您在可以使用相对 URL 的地方使用绝对 URL，您会不断地使您的浏览器做额外的工作，这意味着它的执行效率会降低。
>
> - 不要在链接文本中重复 URL。
>
> - 不要在链接文本中说“链接”或“链接到”——这只是噪音。
>
> - 尽量减少同一文本的多个副本链接到不同位置的情况。
>
> - 对需要在新页签或窗口中打开的链接，使用图标或样式显式标记出来。例如：
>
>    ```html
>    <a href="https://firefox.com" class="external" rel=" noopener">下载 Firefox</a>
>                                  
>    <style>
>        a.external:after {
>            background: transparent url(/static/media/external.e091ac5d.svg) 0 0 no-repeat;
>            background-size: 16px;
>            content: "";
>            display: inline-block;
>            height: 16px;
>            margin-left: 3px;
>            width: 16px;
>            box-sizing: border-box;
>        }
>    </style>
>    ```
>
>    

```html
<a href="https://www.mozilla.org/en-US/">the Mozilla homepage</a>
```

## 使用 title 属性添加支持信息

将鼠标悬停在链接上，就会以工具提示方式显示`title`属性的值。

```html
<a href="https://www.mozilla.org/en-US/"
   title="The best place to find more information about Mozilla's
          mission and how to contribute">the Mozilla homepage</a>
```

> **注意**：链接标题仅在鼠标悬停时显示，这意味着依赖键盘控制或触摸屏浏览网页的人将难以访问标题信息。如果标题的信息对于页面的可用性确实很重要，那么您应该以所有用户都可以访问的方式呈现它，例如将其放入常规文本中。

## 块级链接

几乎任何内容都可以做成链接，甚至是[块级元素](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/Getting_started#block_versus_inline_elements)。

```html
<a href="https://www.mozilla.org/en-US/">
    <img src="mozilla-image.png" alt="mozilla logo that links to the mozilla homepage">
</a>
```

## 文档片段

可以链接到 HTML 文档的特定部分（称为**文档片段）**，而不仅仅是链接到文档的顶部。为此，您首先必须为要链接到的元素分配一个[`id`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes#attr-id)属性。

```html
<h2 id="Mailing_address">Mailing address</h2>
```

然后在URL的末尾跟上一个`#`号，并在它后面跟上将要链接的`id`：

```html
<p>Want to write us a letter? Use our <a href="contacts.html#Mailing_address">mailing address</a>.</p>
```

甚至可以使用文档片段来链接到*当前文档的另一部分*：

```html
<p>The <a href="#Mailing_address">company mailing address</a> can be found at the bottom of this page.</p>
```

## 链接到下载

当您链接到要下载而不是在浏览器中打开的资源时，您可以使用该`download`属性提供默认保存文件名。

```html
<a href="https://download.mozilla.org/?product=firefox-latest-ssl&os=win64&lang=en-US"
   download="firefox-latest-64bit-installer.exe">
    Download Latest Firefox for Windows (64-bit) (English, US)
</a>
```

## 电子邮件链接

```html
<a href="mailto:nowhere@mozilla.org">Send email to nowhere</a>
```

事实上，电子邮件地址是可选的。如果您省略它并且[`href`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a#attr-href)是“mailto:”，则用户的电子邮件客户端将打开一个没有目标地址的新外发电子邮件窗口。这通常用作“分享”链接，用户可以单击这些链接将电子邮件发送到他们选择的地址。

这是一个包含抄送、密送、主题和正文的示例：

```html
<a href="mailto:nowhere@mozilla.org?      
         cc=name2@rapidtables.com&
         bcc=name3@rapidtables.com&
         subject=The%20subject%20of%20the%20email&body=The%20body%20of%20the%20email">
    Send mail with cc, bcc, subject and body
</a>
```

注意：每个字段的值必须是 URL 编码的，即包含非打印字符（不可见字符，如制表符、回车符和分页符）和空格百分比转义。

## 在目标中打开链接

`<a>` 标签的 target 属性规定在何处打开链接文档。

如果在一个 `<a>` 标签内包含一个 `target` 属性，浏览器将会载入和显示用这个标签的 `href` 属性命名的、名称与这个目标吻合的框架或者窗口中的文档。如果这个指定名称或 `id` 的框架或者窗口不存在，浏览器将打开一个新的窗口，给这个窗口一个指定的标记，然后将新的文档载入那个窗口。从此以后，超链接文档就可以指向这个新的窗口。

### 在新窗口中打开链接

```html
<h3>Table of Contents</h3>
<ul>
    <li><a href="pref.html" target="view_window">Preface</a></li>
    <li><a href="chap1.html" target="view_window">Chapter 1</a></li>
    <li><a href="chap2.html" target="view_window">Chapter 2</a></li>
    <li><a href="chap3.html" target="view_window">Chapter 3</a></li>
</ul>
```

当用户第一次选择内容列表中的某个链接时，浏览器将打开一个新的窗口，将它标记为 "view_window"，然后在其中显示希望显示的文档内容。如果用户从这个内容列表中选择另一个链接，且这个 "view_window" 仍处于打开状态，浏览器就会再次将选定的文档载入那个窗口，取代刚才的那些文档。

### 在框架中打开链接

框架：

```html
<frameset cols="100,*">
    <frame src="toc.html">
    <frame src="pref.html" name="view_frame">
</frameset>
```

toc.html：

```html
<h3>Table of Contents</h3>
<ul>
    <li><a href="pref.html" target="view_frame">Preface</a></li>
    <li><a href="chap1.html" target="view_frame">Chapter 1</a></li>
    <li><a href="chap2.html" target="view_frame">Chapter 2</a></li>
    <li><a href="chap3.html" target="view_frame">Chapter 3</a></li>
</ul>
```

当用户从左边框架中的目录中选择一个链接时，浏览器会将这个关联的文档载入并显示在右边这个 "view_frame" 框架中。当其他链接被选中时，右边这个框架中的内容也会发生变化，而左边这个框架始终保持不变。

### 特殊目标

有 4 个保留的目标名称用作特殊的文档重定向操作：

| 值          | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| _blank      | 总在一个新打开、未命名的窗口中打开被链接文档。               |
| _self       | 默认。在相同的框架或窗口中打开被链接文档。                   |
| _parent     | 在父窗口或者包含来超链接引用的框架的框架集中打开被链接文档。如果这个链接是在顶级窗口或者在顶级框架中，那么它与目标 `_self` 等效。 |
| _top        | 在整个窗口中打开被链接文档。                                 |
| *framename* | 在指定的框架中打开被链接文档。注意：除了4个保留的目标名以下划线开头，任何其他用一个下划线作为开头的窗口或者目标都会被浏览器忽略，因此，不要将下划线作为文档中定义的任何框架 `name` 或 `id` 的第一个字符。 |

## `<base>`元素

`<base>` 元素为页面上的所有相对URL规定基准地址或默认目标。默认情况下，页面中的相对URL的基准地址就是当前页面的地址。

```html
<head>
    <base href="https://www.example.com/">
    <base target="_blank">
    <!-- <base target="_top" href="https://example.com/"> -->
</head>
```

注意：[Open Graph](https://ogp.me/)标签不理`<base>`标签，应始终使用绝对 URL。例如：

```html
<meta property="og:image" content="https://example.com/thumbnail.jpg">
```

# 布局

HTML 提供了可以用来表示网站结构的专用标签，例如：

- **页眉：**[`<header>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/header)，通常作为`<body>`、`<article>`和`<section>`的子项。
- **导航栏：**[`<nav>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/nav)。
- **主要内容是：**[`<main>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main)与由代表的各种内容的小节[`<article>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article)（例如：论坛帖子、杂志或报纸文章、博客条目、产品卡、用户提交的评论、交互式小部件或小工具，或任何其他独立的内容项）、[`<section>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section)（节，例如：迷你地图、一组文章标题和摘要）和[`<div>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div)（块级非语义元素，只有在您想不出更好的语义块元素来使用，或者不想添加任何特定含义时才应该使用它）元件。
- **侧边栏：**[`<aside>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/aside) ，经常放在`<main>`里面。
- **页脚：**[`<footer>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/footer)。

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">

        <title>My page title</title>
        <link href="https://fonts.googleapis.com/css?family=Open+Sans+Condensed:300|Sonsie+One" rel="stylesheet" type="text/css">
        <link rel="stylesheet" href="style.css">

        <!-- the below three lines are a fix to get HTML5 semantic elements working in old versions of Internet Explorer-->
        <!--[if lt IE 9]>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.3/html5shiv.js"></script>
        <![endif]-->
    </head>

    <body>
        <!-- Here is our main header that is used across all the pages of our website -->

        <header>
            <h1>Header</h1>
        </header>

        <nav>
            <ul>
                <li><a href="#">Home</a></li>
                <li><a href="#">Our team</a></li>
                <li><a href="#">Projects</a></li>
                <li><a href="#">Contact</a></li>
            </ul>

            <!-- A Search form is another common non-linear way to navigate through a website. -->

            <form>
                <input type="search" name="q" placeholder="Search query">
                <input type="submit" value="Go!">
            </form>
        </nav>

        <!-- Here is our page's main content -->
        <main>

            <!-- It contains an article -->
            <article>
                <h2>Article heading</h2>

                <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Donec a diam lectus. Set sit amet ipsum mauris. Maecenas congue ligula as quam viverra nec consectetur ant hendrerit. Donec et mollis dolor. Praesent et diam eget libero egestas mattis sit amet vitae augue. Nam tincidunt congue enim, ut porta lorem lacinia consectetur.</p>

                <h3>Subsection</h3>

                <p>Donec ut librero sed accu vehicula ultricies a non tortor. Lorem ipsum dolor sit amet, consectetur adipisicing elit. Aenean ut gravida lorem. Ut turpis felis, pulvinar a semper sed, adipiscing id dolor.</p>

                <p>Pelientesque auctor nisi id magna consequat sagittis. Curabitur dapibus, enim sit amet elit pharetra tincidunt feugiat nist imperdiet. Ut convallis libero in urna ultrices accumsan. Donec sed odio eros.</p>

                <h3>Another subsection</h3>

                <p>Donec viverra mi quis quam pulvinar at malesuada arcu rhoncus. Cum soclis natoque penatibus et manis dis parturient montes, nascetur ridiculus mus. In rutrum accumsan ultricies. Mauris vitae nisi at sem facilisis semper ac in est.</p>

                <p>Vivamus fermentum semper porta. Nunc diam velit, adipscing ut tristique vitae sagittis vel odio. Maecenas convallis ullamcorper ultricied. Curabitur ornare, ligula semper consectetur sagittis, nisi diam iaculis velit, is fringille sem nunc vet mi.</p>
            </article>

            <!-- the aside content can also be nested within the main content -->
            <aside>
                <h2>Related</h2>

                <ul>
                    <li><a href="#">Oh I do like to be beside the seaside</a></li>
                    <li><a href="#">Oh I do like to be beside the sea</a></li>
                    <li><a href="#">Although in the North of England</a></li>
                    <li><a href="#">It never stops raining</a></li>
                    <li><a href="#">Oh well...</a></li>
                </ul>
            </aside>

        </main>

        <!-- And here is our main footer that is used across all the pages of our website -->

        <footer>
            <p>©Copyright 2050 by nobody. All rights reversed.</p>
        </footer>

    </body>
</html>
```

## 折叠面板

```html
<details>
    <summary>System Requirements</summary>
    <p>Requires a computer running an operating system. The computer
    must have some memory and ideally some kind of long-term storage.
    An input device as well as some form of output device is
    recommended.</p>
</details>

<style>
    details {
        font: 16px "Open Sans", Calibri, sans-serif;
        width: 620px;
    }

    details > summary {
        padding: 2px 6px;
        width: 15em;
        background-color: #ddd;
        border: none;
        box-shadow: 3px 3px 4px black;
        cursor: pointer;
        list-style: none;  /*去掉折叠的小部件*/
    }

    details > summary::-webkit-details-marker {  /*Chrome去掉折叠的小部件*/
        display: none;
    }

    details > p {
        border-radius: 0 0 10px 10px;
        background-color: #ddd;
        padding: 2px 6px;
        margin: 0;
        box-shadow: 3px 3px 4px black;
    }
</style>
```



# 表格

表格应该用于展示数据，而不是用于布局。

简单的表格：

```html
<table>
  <tr>
    <td>John</td>
    <td>Doe</td>
  </tr>
  <tr>
    <td>Jane</td>
    <td>Doe</td>
  </tr>
</table>
```

带表头、表体和表尾的表格：

```html
<table>
  <thead>
    <tr>
      <th>Header content 1</th>
      <th>Header content 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Body content 1</td>
      <td>Body content 2</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Footer content 1</td>
      <td>Footer content 2</td>
    </tr>
  </tfoot>
</table>
```

`<tbody>`是必须的。实际上，浏览器会自动给前面简单表格加上`<tbody>`。

带表头、表体和表尾的表格的好处是可以更好地控制表格的布局和样式。例如，在一个长表格的情况下，你可以在每个打印的页面上重复表头和页脚；还可以让表格主体显示在一个页面上，并通过上下滚动来获得内容。

## 行标题和列标题

```html
<table>
   <caption>2016 年 8 月售出的物品</caption>
   <tbody>
      <tr>
         <td></td>
         <td></td>
         <th colspan="3" scope="colgroup">衣服</th>
         <th colspan="2" scope="colgroup">配件</th>
      </tr>
      <tr>
         <td></td>
         <td></td>
         <th scope="col">裤子</th>
         <th scope="col">裙子</th>
         <th scope="col">连衣裙</th>
         <th scope="col">手链</th>
         <th scope="col">戒指</th>
      </tr>
      <tr>
         <th rowspan="3" scope="rowgroup">比利时</th>
         <th scope="row">安特卫普</th>
         <td>56</td>
         <td>22</td>
         <td>43</td>
         <td>72</td>
         <td>23</td>
      </tr>
      <tr>
         <th scope="row">绅士</th>
         <td>46</td>
         <td>18</td>
         <td>50</td>
         <td>61</td>
         <td>15</td>
      </tr>
      <tr>
         <th scope="row">布鲁塞尔</th>
         <td>51</td>
         <td>27</td>
         <td>38</td>
         <td>69</td>
         <td>28</td>
      </tr>
      <tr>
         <th rowspan="2" scope="rowgroup">荷兰人</th>
         <th scope="row">阿姆斯特丹</th>
         <td>89</td>
         <td>34</td>
         <td>69</td>
         <td>85</td>
         <td>38</td>
      </tr>
      <tr>
         <th scope="row">乌得勒支</th>
         <td>80</td>
         <td>12</td>
         <td>43</td>
         <td>36</td>
         <td>19</td>
      </tr>
   </tbody>
</table>
```

### `scope`属性

[`scope`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/th#attr-scope)属性可以添加到`<th>`元素中，以准确告诉屏幕阅读器标题是哪些单元格的标题（即是所在行的标题还是列的标题）。

`scope`还有两个可能的值 ——`colgroup`和`rowgroup`，它们用于多个子标题的标题，表示标题适用多行或多列，而不是只适用单行或单列。

### `id`和`header`属性

`scope`属性的替代方法是使用[`id`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes#attr-id)和[`headers`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/td#attr-headers)属性来创建标题和单元格之间的关联。它们的使用方式如下：

1. 为每个`<th>`元素添加一个唯一的`id`。
2. 为每个`<td>`元素添加一个`headers`属性。`headers`属性包含作为该单元格标题的所有`<th>`元素的`id`列表（包括行和列标题），`id`之间以空格分隔。

```html
<thead>
   <tr>
      <th id="purchase">Purchase</th>
      <th id="location">Location</th>
      <th id="date">Date</th>
      <th id="evaluation">Evaluation</th>
      <th id="cost">Cost (€)</th>
   </tr>
</thead>
<tbody>
   <tr>
      <th id="haircut">Haircut</th>
      <td headers="location haircut">Hairdresser</td>
      <td headers="date haircut">12/09</td>
      <td headers="evaluation haircut">Great idea</td>
      <td headers="cost haircut">30</td>
   </tr>

   ...

</tbody>
```



## 跨行跨列

```html
<table>
   <tr>
      <th colspan="2">Animals</th>
   </tr>
   <tr>
      <th colspan="2">Hippopotamus</th>
   </tr>
   <tr>
      <th rowspan="2">Horse</th>
      <td>Mare</td>
   </tr>
   <tr>
      <td>Stallion</td>
   </tr>
   <tr>
      <th colspan="2">Crocodile</th>
   </tr>
   <tr>
      <th rowspan="2">Chicken</th>
      <td>Hen</td>
   </tr>
   <tr>
      <td>Rooster</td>
   </tr>
</table>
```

## 列和列组

**[`<col>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/col)**和**[`<colgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/colgroup)**元素可以在一个地方为一整列数据定义样式。

不使用列和列组：（需要为列中的每个单元）

```html
<table>
  <tr>
    <th>Data 1</th>
    <th style="background-color: yellow">Data 2</th>
  </tr>
  <tr>
    <td>Calcutta</td>
    <td style="background-color: yellow">Orange</td>
  </tr>
  <tr>
    <td>Robots</td>
    <td style="background-color: yellow">Jazz</td>
  </tr>
</table>
```

使用列和列组：

```html
<table>
  <colgroup>
    <col>
    <col style="background-color: yellow">
  </colgroup>
  <tr>
    <th>Data 1</th>
    <th>Data 2</th>
  </tr>
  <tr>
    <td>Calcutta</td>
    <td>Orange</td>
  </tr>
  <tr>
    <td>Robots</td>
    <td>Jazz</td>
  </tr>
</table>
```

> 注意我们没有为第一列设置样式，但我们仍然必须包含一个空白`<col>`元素进行占位，否则样式将只应用于第一列。

**在列（`<col>`）中设置的样式仅限于`border`，`background`，`width`，和`visibility`少数几个属性**。要设置其他属性，您仍然必须为每个`<td>`或`<th>`列设置样式，或者使用复杂的选择器，例如[`:nth-child`](https://developer.mozilla.org/en-US/docs/Web/CSS/:nth-child)。

除了使用`style`定义样式外，也可以使用`class`：

```html
<table>
    <caption>Superheros and sidekicks</caption>
    <colgroup>
        <col>
        <col span="2" class="batman">
        <col span="2" class="flash">
    </colgroup>
    <tr>
        <td> </td>
        <th scope="col">Batman</th>
        <th scope="col">Robin</th>
        <th scope="col">The Flash</th>
        <th scope="col">Kid Flash</th>
    </tr>
    <tr>
        <th scope="row">Skill</th>
        <td>Smarts</td>
        <td>Dex, acrobat</td>
        <td>Super speed</td>
        <td>Super speed</td>
    </tr>
</table>
```

如果我们想将样式信息应用于两列，我们可以只包含一个`<col>`带有 span 属性的元素，如下所示：

```html
<colgroup>
  <col style="background-color: yellow" span="2">
</colgroup>
```

## 表格标题

```html
<table>
  <caption>Dinosaurs in the Jurassic period</caption>

  ...
</table>
```

`<caption>`元素应该紧跟`<table>`开始标签之后。

## 嵌套表

```html
<table id="table1">
  <tr>
    <th>title1</th>
    <th>title2</th>
    <th>title3</th>
  </tr>
  <tr>
    <td id="nested">
      <table id="table2">
        <tr>
          <td>cell1</td>
          <td>cell2</td>
          <td>cell3</td>
        </tr>
      </table>
    </td>
    <td>cell2</td>
    <td>cell3</td>
  </tr>
  <tr>
    <td>cell4</td>
    <td>cell5</td>
    <td>cell6</td>
  </tr>
</table>
```



# 表单

# 多媒体

## 图像

在 Web 上，使用两种类型的图像：

- **光栅图像**是使用像素网格定义的——光栅图像文件包含的信息准确地显示了每个像素的放置位置以及它应该是什么颜色。流行的网络光栅格式包括位图（`.bmp`）、PNG（`.png`）、JPEG（`.jpg`）和 GIF（`.gif`）。
- **矢量图像**是使用算法定义的——矢量图像文件包含形状和路径定义，计算机可以使用这些定义来确定图像在屏幕上呈现时的外观。[SVG](https://developer.mozilla.org/en-US/docs/Glossary/SVG)格式允许我们创建功能强大的矢量图形在Web上使用。

### `<img>`元素

为了在网页上放置一个简单的图像，我们使用[`<img>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img)元素。

```html
<img src="dinosaur.jpg">
```

> **注意**：搜索引擎也会读取图像文件名并将它们计入 SEO。因此，你应该给你的图像一个描述性的文件名。
>
> **注意**：注意：元素`<img>`和 `<video>`有时被称为[替换元素](https://developer.mozilla.org/en-US/docs/Web/CSS/Replaced_element)。这是因为元素的内容和大小是由外部资源（如图像或视频文件）定义的，而不是由元素本身的内容定义的。

#### 替代文字

属性`alt`是图像的文本描述，用于在图像无法看到/显示或由于互联网连接缓慢而需要很长时间渲染时显示替代文本。

```html
<img src="images/dinosaur.jpg"
     alt="The head and torso of a dinosaur skeleton;
          it has a large head with long sharp teeth">
```

#### 宽度和高度

您可以使用`width`和`height`属性来指定图像的宽度和高度。

```html
<img src="images/dinosaur.jpg"
     alt="The head and torso of a dinosaur skeleton;
          it has a large head with long sharp teeth"
     width="400"
     height="341">
```

通常不应使用 HTML 属性更改图像的大小，它有可能会实图像失真。如果确实需要更改图像的大小，也应改用[CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS)。

#### 图像标题

`title`属性提供了鼠标悬停的工具提示：

```html
<img src="images/dinosaur.jpg"
     alt="The head and torso of a dinosaur skeleton;
          it has a large head with long sharp teeth"
     width="400"
     height="341"
     title="A T-Rex on display in the Manchester University Museum">
```

#### 图像说明

HTML5[`<figure>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figure)和[`<figcaption>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figcaption)元素为图形提供语义容器，并将图形清晰地链接到它的说明文字。

```html
<figure>
  <img src="images/dinosaur.jpg"
       alt="The head and torso of a dinosaur skeleton;
            it has a large head with long sharp teeth"
       width="400"
       height="341">

  <figcaption>A T-Rex on display in the Manchester University Museum.</figcaption>
</figure>
```

> `<figure>`和`<figcaption>`不仅仅用于图像，也可用于代码片段、音频、视频、方程、表格或独立的内容单元。

#### 背景图像

您还可以使用 CSS 将图像嵌入网页作为背景图像。

CSS的`background-*`属性用于控制背景图像，比 HTML 图像更容易定位和控制：

```css
p {
  background-image: url("images/dinosaur.jpg");
}
```

CSS背景图像与HTML图像的选择：如果图像有意义，您应该使用 HTML 图像。如果图像纯粹是装饰，则应使用 CSS 背景图像。

### SVG（矢量图形）

[**可缩放矢量图形 (SVG)**](https://developer.mozilla.org/en-US/docs/Web/SVG)是一种基于[XML](https://developer.mozilla.org/en-US/docs/Glossary/XML)的标记语言，用于描述基于二维的矢量图像。

SVG优点：

- 图形放大缩小不会失真。
- 矢量图像中的文本仍然可以访问（这也有利于您的[SEO](https://developer.mozilla.org/en-US/docs/Glossary/SEO)）。
- SVG 非常适合样式/脚本编写，因为图像的每个组件都是一个元素，可以通过 CSS 设置样式或通过 JavaScript 编写脚本。

SVG缺点：

- SVG 会很快变得复杂，这意味着文件大小会增加；复杂的 SVG 也会在浏览器中花费大量的处理时间。
- SVG 可能比光栅图像更难创建，具体取决于您尝试创建的图像类型。
- 旧浏览器不支持 SVG，因此如果您需要在您的网站上支持旧版本的 Internet Explorer，则可能不适合（从 IE9 开始支持 SVG。）

由于上述原因，光栅图形可以说更适合复杂的精度图像，例如照片。

下面的代码创建了一个圆和一个矩形：

```svg
<svg version="1.1"
     baseProfile="full"
     width="300" height="200"
     xmlns="http://www.w3.org/2000/svg">
  <rect width="100%" height="100%" fill="black" />
  <circle cx="150" cy="100" r="90" fill="blue" />
</svg>
```

> 为了创建 SVG 图像，大多数人使用矢量图形编辑器，如[Inkscape](https://inkscape.org/en/)或[Illustrator](https://en.wikipedia.org/wiki/Adobe_Illustrator)。

#### 将 SVG 添加到您的页面

##### 快捷方式：img元素

```html
<img
    src="equilateral.svg"
    alt="triangle with all three sides equal"
    height="87"
    width="100" />
```

> 如果您的 SVG 没有固有的纵横比，则需要一个`height`或一个`width`属性。

优点：

- 快速、熟悉的图像语法以及`alt`属性中可用的内置文本等效项。

- 您可以通过`<img>`在`<a>`元素内部嵌套来轻松地将图像变成超链接。
- SVG 文件可以由浏览器缓存，从而为使用将来加载的图像的任何页面提供更快的加载时间。

缺点：

- 您无法使用 JavaScript 操作图像。
- 如果要使用 CSS 控制 SVG 内容，则必须在 SVG 代码中包含内联 CSS 样式。（从 SVG 文件调用的外部样式表无效。）
- 您不能使用 CSS 伪类（如`:focus`）重新设置图像的样式。

对于不支持 SVG 的浏览器（IE 8 及以下，Android 2.3 及以下），您可以从`src`属性中引用 PNG 或 JPG，并使用[`srcset`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset)属性（只有最近的浏览器识别）来引用 SVG。在这种情况下，只有支持的浏览器才会加载 SVG——旧浏览器会加载 PNG：

```html
<img src="equilateral.png" alt="triangle with equal sides" srcset="equilateral.svg">
```

您还可以使用 SVG 作为 CSS 背景图像，如下所示。在下面的代码中，较旧的浏览器将坚持使用它们理解的 PNG，而较新的浏览器将加载 SVG：

```css
background: url("fallback.png") no-repeat center;
background-image: url("image.svg");
background-size: contain;
```

与上述`<img>`方法一样，使用 CSS 背景图像插入 SVG 意味着无法使用 JavaScript 操作 SVG，并且也受到相同的 CSS 限制。

##### 在 HTML 中包含 SVG 代码

**内联 SVG**使用`<svg>`元素：

```html
<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
  <style>
    div {
      color: white;
      font: 18px serif;
      height: 100%;
      overflow: auto;
    }
  </style>

  <polygon points="5,5 195,10 185,185 10,195" />

  <!-- Common use case: embed HTML text into SVG -->
  <foreignObject x="20" y="20" width="160" height="160">
    <!--
      In the context of SVG embedded in an HTML document, the XHTML
      namespace could be omitted, but it is mandatory in the
      context of an SVG document
    -->
    <div xmlns="http://www.w3.org/1999/xhtml">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit.
      Sed mollis mollis mi ut ultricies. Nullam magna ipsum,
      porta vel dui convallis, rutrum imperdiet eros. Aliquam
      erat volutpat.
    </div>
  </foreignObject>
</svg>
```

优点：

- 将 SVG 内联可以节省 HTTP 请求，因此可以减少加载时间。
- 您可以将`class` 和`id`分配给 SVG 元素并使用 CSS 设置它们的样式（无论是在 SVG 内还是在您为 HTML 文档放置 CSS 样式规则的任何位置）。事实上，您可以使用任何[SVG 表示属性](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute#presentation_attributes)作为 CSS 属性。
- 内联 SVG 是唯一可以让您在 SVG 图像（甚至在常规样式表中）使用 CSS 交互（如`:focus`）和 CSS 动画的方法。
- 您可以通过将 SVG 标记包装在一个[`<a>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a)元素中来将其制作为超链接。

缺点：

- 此方法仅适用于仅在一处使用 SVG 的情况。
- 额外的 SVG 代码会增加 HTML 文件的大小。
- 浏览器无法缓存内联 SVG，因此在加载包含图像的第一页后，其他包含该图像的页面不会加载得更快。
- 您可以在[`<foreignObject>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/foreignObject)元素中包含回退，但支持 SVG 的浏览器仍会下载任何回退图像。您需要权衡：只是为了支持过时的浏览器，额外的开销是否真的值得。

##### 通过`<iframe>`嵌入SVG

```html
<iframe src="triangle.svg" width="500" height="500" sandbox>
    <img src="triangle.png" alt="Triangle with three unequal sides" />
</iframe>
```

缺点：

- `iframe`如您所见确实具有回退机制，但浏览器仅在完全不支持`iframe`时才显示回退机制。
- 此外，除非 SVG 与您当前的网页[同源](https://developer.mozilla.org/en-US/docs/Glossary/Origin)，否则您无法在主网页上使用 JavaScript 来操作 SVG。

### 响应式图片

#### 分辨率切换

##### 不同尺寸

在不同的设备（视口大小不同）中，显示同一图片的不同尺寸版本（保持图像的内容或纵横比不变）。

```html
<img srcset="elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
     sizes="(max-width: 600px) 480px,
            800px"
     src="elva-fairy-800w.jpg"
     alt="Elva dressed as a fairy">
```

**[`srcset`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset)**定义我们将允许浏览器选择的图像集，以及每个图像尺寸（用**宽度描述符**表示，以`w`结尾）。

**[`sizes`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes)**定义一组媒体条件（例如屏幕宽度）和插槽大小，以指示当某些媒体条件为真时最好选择的图像尺寸。

有了这些属性，浏览器将：

1. 看它的设备宽度。
2. 找出`sizes`列表中的哪个媒体条件是第一个为真的。
3. 查看提供给该媒体查询的插槽宽度。
4. 加载`srcset`列表中引用的与插槽大小相同的图像，如果没有，则加载第一个大于所选插槽大小的图像。

> **注意**：在[`<head>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head)上面链接的示例中，您会发现以下行 `<meta name="viewport" content="width=device-width">`：这会强制移动浏览器采用其实际视口宽度来加载网页（某些移动浏览器会谎报其视口宽度，而是以更大的视口宽度加载页面）然后缩小加载的页面，这对响应式图像或设计不是很有帮助）。

槽宽度，可以提供绝对长度 ( `px`, `em`) 或相对于视口的长度 ( `vw`)，但不能提供百分比。

##### 相同尺寸，不同分辨率

只是分辨率不同，但仍保持图像的内容或纵横比不变。这实际上是根据显示密度提供不同图像源的场景（即视网膜图像）。

```html
<img srcset="elva-fairy-320w.jpg,
             elva-fairy-480w.jpg 1.5x,
             elva-fairy-640w.jpg 2x"
     src="elva-fairy-640w.jpg"
     alt="Elva dressed as a fairy">

```

这里不需要`sizes`属性。

假设有如下CSS样式：

```css
img {
  width: 320px;
}
```

上面定义是**CSS像素**。

`srcset`属性中形如`…x`的是**像素密度描述符**。例如，`elva-fairy-640w.jpg 2x`表示：如果每个 CSS 像素代表两个**设备像素**或更多的高分辨率，则`elva-fairy-640w.jpg`图像将被加载。当像素密度描述符为`1x`时，可以省略。

#### 艺术指导

艺术指导问题是指根据屏幕设备显示图片的不同裁剪版本（需要更改图像的内容或纵横比）。[`<picture>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture)元素可以让我们实现只是这种解决方案。

```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
  <source media="(min-width: 800px)" srcset="elva-800w.jpg">
  <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva">
</picture>
```

浏览器会显示第一个`media`属性的媒体条件返回`true`的图像。当没有媒体条件返回 true 或者浏览器不支持`<picture>`元素时，显示`<img>`元素（必须在所有`<source>`之后）指定的图像。

`srcset`属性仍然可以指定多个不同分辨率的图片。

```html
<picture>
  <source srcset="logo-768.png 768w, logo-768-1.5x.png 1.5x">
  <source srcset="logo-480.png, logo-480-2x.png 2x">
  <img src="logo-320.png" alt="logo">
</picture>
```

> **注意**：您应该只在艺术指导场景中使用该`media`属性；当您使用 时`media`，不要同时在`sizes`属性中提供媒体条件。

还可以使用`type`属性提供 MIME 类型，以便浏览器可以立即拒绝不支持的文件类型：

```html
<picture>
  <source type="image/svg+xml" srcset="pyramid.svg">
  <source type="image/webp" srcset="pyramid.webp">
  <img src="pyramid.png" alt="regular pyramid built from four equilateral triangles">
</picture>
```



### 画布

## 视频

[`<video>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video)元素允许您非常轻松地嵌入视频。

```html
<video src="rabbit320.webm" controls>
  <p>Your browser doesn't support HTML5 video. Here is a <a href="rabbit320.webm">link to the video</a> instead.</p>
</video>
```

[`controls`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-controls)属性：如果存在此属性，浏览器将提供控件以允许用户控制视频播放，包括音量、搜索和暂停/恢复播放。

`<video>`元素的内容：称为**回退内容**- 如果访问页面的浏览器不支持该`<video>`元素，则会显示该内容，从而允许我们为旧浏览器提供回退。这可以是你喜欢的任何东西,通常我们提供了视频文件的直接链接，让用户至少可以通过某种方式访问它，而不管他们使用的是什么浏览器。

### 使用多种源格式提高兼容性

由于不同的浏览器支持不同的视频（和音频）格式，为了最大限度地提高您的网站或应用程序在用户浏览器上运行的可能性，您可能需要以多种格式提供您使用的每个媒体文件。

```html
<video controls>
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>Your browser doesn't support HTML5 video. Here is a <a href="rabbit320.mp4">link to the video</a> instead.</p>
</video>
```

不使用`src`属性，而是使用[`<source>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source)元素来为视频提供多种格式的媒体文件。浏览器将遍历`<source>`元素并播放它具有编解码器支持的第一个元素。

每个`<source>`元素也有一个[`type`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source#attr-type)属性。这是可选的，但建议您包括它。该`type`属性包含指定文件的MIME 类型，浏览器可以使用`type`来立即跳过他们不理解的视频。如果没有设置`type`属性，浏览器将加载并尝试播放每个文件，直到找到一个有效的文件，这显然需要时间并且是对资源的不必要使用。

### 其他功能

```html
<video controls width="400" height="400"
       autoplay loop muted preload="auto"
       poster="poster.png">
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>Your browser doesn't support HTML video. Here is a <a href="rabbit320.mp4">link to the video</a> instead.</p>
</video>
```

[`width`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-width) 和 [`height`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-height)：控制视频大小，也可以使用CSS来控制视频大小。

[`autoplay`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-autoplay)：使音频或视频在页面加载时立即开始播放。

[`loop`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-loop)：循环播放。

[`muted`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-muted)：使媒体播放时默认关闭声音。

[`poster`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-poster)：在播放视频之前显示的图像的 URL。它旨在用于启动画面或广告屏幕。

[`preload`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attr-preload)：用于缓冲大文件；它可以采用以下三个值之一：

- `"none"` 不缓冲文件
- `"auto"` 缓冲媒体文件
- `"metadata"` 仅缓冲文件的元数据

### 字幕

我们可以使用[WebVTT](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)文件格式和[`<track>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/track)元素给视频或音频加字幕。

```html
<video controls>
    <source src="example.mp4" type="video/mp4">
    <source src="example.webm" type="video/webm">
    <track kind="subtitles" src="subtitles_es.vtt" srclang="es" label="Spanish">
</video>
```

`<track>`应该放在`<audio>`或内`<video>`，但在所有`<source>`元素之后。

[`kind`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/track#attr-kind)属性指示如何使用文本轨道。如果省略，则默认类型为`subtitles`；如果该属性包含无效值，它将使用`metadata`。允许使用以下关键字：

- `subtitles`：翻译字幕。
- `captions`：将视频或音频中的对话写成文本。
- `descriptions`：音视频内容的文字说明。
- `chapters`：章节标题。
- `metadata`：脚本使用的轨道，对用户不可见。

[`srclang`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/track#attr-srclang)属性告诉浏览器用什么语言编写字幕。它必须是有效的[BCP 47](https://r12a.github.io/app-subtags/)语言标签。

subtitles_es.vtt：

```
WEBVTT

1
00:00:22.230 --> 00:00:24.606
This is the first subtitle.

2
00:00:30.739 --> 00:00:34.074
This is the second.

  ...
```



## 音频

`<audio>`元素的工作方式与该`<video>`元素类似，但有一些小的差异：

- 不支持`width`/`height`属性
- 也不支持`poster`属性

```html
<audio controls>
  <source src="viper.mp3" type="audio/mp3">
  <source src="viper.ogg" type="audio/ogg">
  <p>Your browser doesn't support HTML5 audio. Here is a <a href="viper.mp3">link to the audio</a> instead.</p>
</audio>
```



## 嵌入内容

### 嵌入网页

[`<iframe>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe)元素旨在允许您将其他 Web 文档嵌入到当前文档中。这非常适合将第三方内容合并到您的网站中，您可能无法直接控制并且不想实现自己的版本——例如在线视频、评论系统、在线地图、广告横幅等。

```html
<head>
  <style> iframe { border: none } </style>
</head>
<body>
  <iframe src="https://developer.mozilla.org/en-US/docs/Glossary"
          width="100%" height="500" allowfullscreen sandbox>
    <p>
      <a href="/en-US/docs/Glossary">
         Fallback link for browsers that don't support iframes
      </a>
    </p>
  </iframe>
</body>
```

[`border: none`](https://developer.mozilla.org/en-US/docs/Web/CSS/border)：去掉`<iframe>`的边框。

`allowfullscreen`：如果设置，`<iframe>`则可以使用[Fullscreen API](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API)将其置于全屏模式。

`<iframe>`元素的内容是回退内容，如果浏览器不支持`<iframe>`，则会显示该回退内容。

> **注意**：为了提高速度，最好在主要内容加载完成后用 JavaScript设置 `<iframe>` 的`src`属性。

#### 安全问题

[点击劫持](https://en.wikipedia.org/wiki/Clickjacking)是一种常见的 iframe 攻击，黑客在您的文档中嵌入一个不可见的 iframe（或将您的文档嵌入到他们自己的恶意网站中）并使用它来捕获用户的交互。这是误导用户或窃取敏感数据的常见方式。

##### 使用HTTPS

[HTTPS](https://developer.mozilla.org/en-US/docs/Glossary/https)是[HTTP](https://developer.mozilla.org/en-US/docs/Glossary/HTTP)的加密版本。您应该尽可能使用 HTTPS 为您的网站提供服务：

1. HTTPS 减少了远程内容在传输过程中被篡改的可能性，
2. HTTPS 可防止嵌入的内容访问您的父文档中的内容，反之亦然。

要为自己的站点设置 HTTPS 支持，[Let's Encrypt](https://letsencrypt.org/)提供了可用于自动创建和安装必要证书的工具和说明。

##### 应该始终使用`sandbox`属性

[`sandbox`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#attr-sandbox)属性会对 *frame* 中的内容应用额外的限制。该属性的值可以为空（应用所有限制），也可以为空格分隔的标记以解除指定限制。一个重要的注意事项是，*永远不要同时*将`allow-scripts`和`allow-same-origin`都添加到您的`sandbox`属性中——这允许嵌入的内容可以绕过阻止站点执行脚本[的同源策略](https://developer.mozilla.org/en-US/docs/Glossary/Same-origin_policy)，并使用 JavaScript 完全关闭沙箱。

##### 配置 CSP 指令

[CSP](https://developer.mozilla.org/en-US/docs/Glossary/CSP)代表**[内容安全策略，](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)**并提供[一组 HTTP 标头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)（当网页从 Web 服务器提供服务时与网页一起发送的元数据）旨在提高 HTML 文档的安全性。当涉及到保护`<iframe>`时，您可以*[配置您的服务器以发送适当的`X-Frame-Options` 标头。](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)*这可以防止其他网站将您的内容嵌入到他们的网页中（这会导致[点击劫持](https://en.wikipedia.org/wiki/Clickjacking)和许多其他攻击）。

### `<embed>` 和 `<object>` 元素

[`<embed>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/embed)和[`<object>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/object)元素是通用的、可嵌入多种类型外部内容（包括像Java Applet和Flash插件技术、PDF，以及视频、SVG 和图像等）的嵌入工具，通常需要浏览器插件的支持。

它们是一项遗留技术。因为，Applet和Flash已经过时，PDF更偏向于使用链接，图像和视频有它们自己的元素。保留它主要是为了以防万一您在某些情况下（如 Intranet 或企业项目）需要它们。

```html
<embed src="whoosh.swf" quality="medium"
       bgcolor="#ffffff" width="550" height="400"
       name="whoosh" align="middle" allowScriptAccess="sameDomain"
       allowFullScreen="false" type="application/x-shockwave-flash"
       pluginspage="http://www.macromedia.com/go/getflashplayer">

<object data="mypdf.pdf" type="application/pdf"
        width="800" height="1200">
  <p>You don't have a PDF plugin, but you can
    <a href="mypdf.pdf">download the PDF file.
    </a>
  </p>
</object>
```



# HTML API

## 地理定位

## 拖放

## Web存储

## 应用缓存

## Web Worker

## Server-Sent 事件

# HTML验证

通过[标记验证服务](https://validator.w3.org/)运行您的 HTML 页面- 由 W3C 创建和维护。这个网页将一个 HTML 文档作为输入，然后给你一个报告，告诉你你的 HTML 有什么问题。

