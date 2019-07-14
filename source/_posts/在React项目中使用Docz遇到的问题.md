---
title: 在React项目中使用Docz遇到的问题
date: 2019-07-14 22:53:48
categories:
- 项目
tags:
- React
- 项目
---

# 在React项目中使用Docz遇到的问题

（1）按照官网的Getting Start添加依赖后执行`yarnpkg add docz`，出现的问题如下：

```js
Failed to compile.

./node_modules/docz/dist/index.esm.js 23:38
Module parse failed: Unexpected token (23:38)
Failed to compile.

./node_modules/docz/dist/index.esm.js 23:38
Module parse failed: Unexpected token (23:38)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { MDXProvider } from '@mdx-js/react';
| 
> const BasePlayground = loadable(() => import('./Playground.esm.js'));
| const Playground = props => typeof window !== 'undefined' ? createElement(Suspense, {
|   fallback: null

```

Github上面的相关issue是：https://github.com/pedronauck/docz/issues/596

最关键的是这一个：

![](https://i.loli.net/2019/07/05/5d1f4b5aed0f933839.png)

他给出的解决方案是对webpack降级：

```cmd
npm i -D webpack@4.28.4

```

问题解决！

![](https://i.loli.net/2019/07/05/5d1f4ba8678ff64652.png)

然而，事情远远没有那么简单，当再次运行`yarn start`启动项目时，爆炸了：

![](https://i.loli.net/2019/07/07/5d21d9ed878d544757.png)

显然是docz和creact-react-app的webpack版本冲突了，我又在github搜罗了一番，解决方案是给docz单独指定一个webpack版本：

![](https://i.loli.net/2019/07/07/5d21dacbda01361621.png)

启动成功，舒服了