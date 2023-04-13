---
title: vue篇
description: vue篇
date: 2022-6-17
tags:
  - vue
  - 面试题
---

## v-if 和 v-show 怎么理解

1. 基本概念

- v-if 如果条件不成立 则不会渲染当前指令所在节点的 dom 元素
- v-show 切换当前 dom 显示与隐藏 通过样式控制

2. 效果

   - v-if vue2 编译
     ![x](../public/vue2.png)
     ![x](../public/vue3.png)

   - v-show vue2 编译
     ![x](../public/vue4.png)
     ![x](../public/vue5.png)

     ::: tip 注意
     v-show 内置指令 源码

     绑定时会先缓存 dom 的 display 属性值，条件满足时会 赋予原始属性值，不满足时赋予 none
     ![x](../public/vue6.png)
     :::

3. 如何选择？

- v-if 会阻断内部代码执行，如果不成立 就不会执行 内部逻辑
- 不会频繁更新的 就是用 v-if

## v-if 和 v-show 的优先级 哪个更高？

`v-if和v-show避免在同一个标签中使用。如果遇到需要同时 使用时，可以使用 结算属性的方式`

::: warning 避免这种写法

```html
<li v-for="i in arr" v-if="false"></li>
```

:::

- 在 vue2 解析时，v-for 优先级比 v-if 高，先解析 v-for 再解析 v-if。会导致先循环再对每一项进行判断，浪费性能。

- vue3 中 v-if 比 v-for 优先级高

> 可以同时使用 v-if 与 v-for vue3 会把 v-if 创建并提取到外层 template 节点上
