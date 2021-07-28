---
title: css
date: 2020-07-25 17:41:06
tags:
---

HTML 用于定义内容的结构和语义，而 [CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS)（级联样式表）用于设置网页的样式和布局。

# CSS规则

![CSS 选择器](css/selector.gif)

CSS规则以[选择器](https://developer.mozilla.org/en-US/docs/Glossary/CSS_Selector)和声明块组成。

选择器指向您需要设置样式的 HTML 元素。

声明块用花括号括起来，其中包含一条或多条用分号分隔的声明。

每条声明都包含一个 CSS [属性](https://developer.mozilla.org/en-US/docs/Glossary/property/CSS)名称和一个值，以冒号分隔。

CSS 样式表可以包含许多这样的规则，一个接一个地编写。

```css
h1 {
    color: red;
    font-size: 5em;
}

p {
    color: black;
}
```



## CSS选择器

### 元素选择器

元素选择器根据元素名称来选择 HTML 元素。

```css
p {
  text-align: center;
  color: red;
}
```

### ID选择器

id 选择器使用 HTML 元素的 id 属性来选择特定元素。id 选择器用于选择一个唯一的元素！

```css
#para1 {
  text-align: center;
  color: red;
}
```

### 类选择器

类选择器选择有特定 class 属性的 HTML 元素。

```css
.center {
  text-align: center;
  color: red;
}
```

### 通用选择器

通用选择器（`*`）选择页面上的所有的 HTML 元素。

```css
* {
  text-align: center;
  color: blue;
}
```

### 分组选择器

分组选择器选取所有具有相同样式定义的 HTML 元素（逗号分隔）。

```css
p, li {
    color: green;
}
```

### 后代选择器

后代选择器（descendant selector）又称为包含选择器。

后代选择器可以选择作为某元素后代的元素。



# 应用CSS

## 在文档的头部链接 CSS

```html
<link rel="stylesheet" href="styles.css">
```

