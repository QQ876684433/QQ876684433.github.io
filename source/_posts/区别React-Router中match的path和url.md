---
title: 区别React-Router中match的path和url
date: 2019-07-14 22:41:21
categories:
- 前端
tags:
- React
- React-Router
---

# 区别React-Router中match的path和url

> 参考：https://blog.51cto.com/zhuxianzhong/2120738

官方描述如下：

- path - (string) The path pattern used to match. Useful for building nested \<Route>s
- url - (string) The matched portion of the URL. Useful for building nested \<Link>s

path用来标识路由匹配的URL部分。React Router使用了Path-to-RegExp库将路径字符串转为正则表达式。然后正则表达式会匹配当前路径。

**当路由路径和当前路径成功匹配，会生成一个对象match**。match对象有更多关于URL和path的信息。这些信息可以通过它的属性获取，如下所示：

- **match.url.返回URL中匹配部分的字符串。用于创建嵌套的\<Link>很有用。**
- **match.path.用于匹配路径模式。用来创建嵌套的\<Route>。**
- match.isExact.返回布尔值，如果准确（没有任何多余字符）匹配则返回true。
- match.params.返回一个对象包含Path-to-RegExp包从URL解析的键值对。