---
title: css中常见的选择符
date: 2019-07-14 22:45:02
categories:
- 前端
tags:
- css
---

# css中常见的选择符

> 参考：https://yanhaijing.com/css/2014/01/04/the-30-css-selectors-you-must-memorize/
>
> 官网：https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Selectors#Simple_selectors

## 基本选择器

- Type（类型）选择器

  这种基本选择器会选择所有匹配给定元素名的元素。 

  **语法：**`elename` 

  **例子：**input 将会选择所有的 input 元素。

- Class（类）选择器

  这种基本选择器会基于类属性的值来选择元素。 

  **语法：** `.classname` 

  **例子：** `.index` 会匹配所有包含 `index 类的元素` (由类似于`class="index"`这样的属性定义的).

- ID选择器

  这种基本选择器会选择所有id属性与之匹配的元素。需要注意的是一个文档中每个id都应该是唯一的。 

  **语法：**`#idname` 

  **例子：**`#toc` 将会匹配所有id属性为 toc 的元素 (类似于这样的定义 `id="toc"`).

- 通用选择器

  这个基本选择器选择所有节点。它也常常和一个名词空间配合使用，用来选择该空间下的所有元素。 

  **语法：** `* ns|* *|*` 

  **例子：**`*` （通配符）将会选择所有元素。

- 属性选择器

  这个基本的选择器根据元素的属性来进行选择。（具体参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors） 

  **语法：**`[attr] [attr=value] [attr~=value] [attr|=value] [attr^=value] [attr$=value] [attr*=value]` 

  **例子：**`[autoplay]` 将会选择所有具有 autoplay 属性的元素（不论这个属性的值是什么）

## 组合选择器

- 紧邻兄弟选择器

  `'+'` 操作符选择相邻元素，即第二个节点**紧邻着第一个节点**，并且**拥有共同的父节点**。

  **语法:** `A + B`

  **例子:** `ul + li` 会匹配任何 ul 元素后紧邻的 li 元素。

- 一般兄弟选择器

  `'~'` 操作符选择兄弟元素，也就是说，第二个节点在第一个节点**后面的任意位置**，并且这俩**节点的父节点相同**。

  **语法:** `A ~ B`

  **例子:** `p ~ span` 将会匹配同一父元素下，p 元素后的所有 span 元素。

- 子选择器

  `'>'` 操作符选择第一个元素的**直接子节点**。

  **语法:** `A > B`

  **例子:** `ul > li` 将会匹配直接嵌套在 ul 元素内的所有 li 元素。

- 后代选择器

  `' '`  (空格) 操作符将选择第一个元素的**子代节点**。

  **语法:** `A B`

  **例子:** `div span` 将匹配 div 元素内所有的 span 元素。