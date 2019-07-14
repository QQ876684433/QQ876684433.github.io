---
title: PureComponent简介
date: 2019-07-14 22:39:59
categories:
- 前端
tags:
- React
---

# PureComponent

> 参考：https://juejin.im/entry/5934c9bc570c35005b556e1a

## 为什么使用？

React15.3中新加了一个 `PureComponent` 类，顾名思义， `pure` 是纯的意思，`PureComponent` 也就是纯组件，取代其前身 `PureRenderMixin` ,`PureComponent` 是优化 `React` 应用程序最重要的方法之一，易于实施，只要把继承类从 `Component` 换成 `PureComponent` 即可，可以减少不必要的 `render` 操作的次数，从而提高性能，而且可以少写 `shouldComponentUpdate` 函数，节省了点代码。

## 原理

当组件更新时，如果组件的 `props` 和 `state` 都没发生改变，`render` 方法就不会触发，省去 `Virtual DOM` 的生成和比对过程，达到提升性能的目的。具体就是 `React` 自动帮我们做了一层浅比较：

```js
if (this._compositeType === CompositeTypes.PureClass) {
  shouldUpdate = !shallowEqual(prevProps, nextProps)
  || !shallowEqual(inst.state, nextState);
}
```

而 `shallowEqual` 又做了什么呢？会比较 `Object.keys(state | props)` 的长度是否一致，每一个 `key`是否两者都有，并且是否是一个引用，也就是只比较了第一层的值，确实很浅，所以深层的嵌套数据是对比不出来的。