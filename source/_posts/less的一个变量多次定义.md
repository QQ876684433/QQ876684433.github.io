---
title: less的一个变量多次定义
date: 2019-07-14 22:46:30
categories:
- 前端
tags:
- less
---

# 一个变量多次定义

首先**一个变量不需要在使用之前就定义**，而且也**可以在不同地方重复定义同一个变量**，下面两个编译的结果一样：

```less
.lazy-eval{
    width: @var;
}

@var:@a;
@a:9%;
```

```less
.lazy-eval{
    width: @var;
    @a:9%;
}

@var:@a;
@a:100%;

// 定义在块级作用域里的@a(9%)覆盖了全局作用域里的@a(100%)
```

编译出来的css是：

```css
.lazy-eval {
  width: 9%;
}
```

官网里的原话是：

> When defining a variable twice, the last definition of the variable is used, **searching from the current scope upwards**. This is similar to css itself where the last property inside a definition is used to determine the value.

更经典的例子是：

```less
@var: 0;
.class {
  @var: 1;
  .brass {
    @var: 2;
    three: @var;
    @var: 3;
  }
  one: @var;
}
```

编译结果：

```css
.class {
  one: 1;
}
.class .brass {
  three: 3;
}
```

可以看出，three首先被赋值`@var: 2;`，然后被下面定义的`@var: 3;`覆盖；而one的作用域内看不到`@var: 2;`和`@var: 3;`，因此最后被赋值为1