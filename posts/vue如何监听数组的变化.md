---
title: vue 如何监听数组的变化
description: vue 如何监听数组的变化
date: 2022-6-17
tags:
  - vue
  - 面试题
---

## 实现数组劫持

- 数组考虑性能原因没有用 defineProperty 对数组的每一项进行拦截，而是选择重写数组( pysh,shift,pop,splice,unshift,sort,reverse) 方法
- 数组中如果是对象数据类型也会进行递归劫持

::: tip 注意
更改数组的索引，或数组的长度 vue 是监测不到的
:::

```js
// 代码实现 参考
// 大致思路
let newArrayProto = Object.create(Array.protoType)
let oldArrayProto = Array.protoType
;['push', 'shift', 'unshift', 'pop', 'reverse', 'sort', 'splice'].forEach((method) => {
  newArrayProto[method] = function (...args) {
    oldArrayProto[method].apply(this, args)
  }
})

// observer中
数组data.__proto__ = newArrayProto
```
