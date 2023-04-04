---
title: pinia避坑经验
description: pinia避坑经验
date: 2023-04-04
tags:
  - pinia
---

## store 解构的变量修改后没有更新

store 解构的变量修改后没有更新

```vue
// A.vue
<script setup lang="ts">
  import componentStore from '@/store/component'
  const componentStoreObj = componentStore()

  let { name } = componentStoreObj

  const changeName = () => {
    componentStoreObj.name = 'hello pingan8787'
  }
</script>

<template>
  <span @click="changeName">{{ name }}</span>
</template>
```

这时候点击按钮触发 `changeName` 事件后，视图上的 `name` 并没有变化。这是因为 store 是个 reactive 对象，当进行解构后，会破坏它的响应性。所以我们不能直接进行解构。

这种情况就可以使用 Pinia 提供 `storeToRefs` 工具方法，使用起来也很简单，只需要将需要解构的对象通过 `storeToRefs` 方法包裹，其他逻辑不变：

```vue
// A.vue
<script setup lang="ts">
  import componentStore from '@/store/component'
  import { storeToRefs } from 'pinia'
  const componentStoreObj = componentStore()

  let { name } = storeToRefs(componentStoreObj) // 使用 storeToRefs 包裹

  const changeName = () => {
    componentStoreObj.name = 'hello pingan8787'
  }
</script>

<template>
  <span @click="changeName">{{ name }}</span>
</template>
```

这样再修改其值，变更马上更新视图了。

## Pinia 修改数据状态的方式

按照官网给的方案，目前有三种方式修改：

1. 通过 `store.`属性名赋值修改单笔数据的状态；

```js
const changeName = () => {
  componentStoreObj.name = 'hello pingan8787'
}
```

2. 通过 `$patch`方法修改多笔数据的状态；

当我们需要修改多笔数据，可以这么写

```js
const changeName = () => {
  componentStoreObj.name = 'hello pingan8787'
  componentStoreObj.age = '18'
  componentStoreObj.addr = 'xiamen'
}
```

上面这么写也没什么问题，但是 Pinia 官网已经说明，使用 $patch 的效率会更高，性能更好，所以在修改躲避数据时，更推荐使用 $patch，使用方式也很简单：

```js
const changeName = () => {
  // 参数类型1：对象
  componentStoreObj.$patch({
    name: 'hello pingan8787',
    age: '18',
    addr: 'xiamen'
  })

  // 参数类型2：方法，该方法接收 store 中的 state 作为参数
  componentStoreObj.$patch((state) => {
    state.name = 'hello pingan8787'
    state.age = '18'
    state.addr = 'xiamen'
  })
}
```

3. 通过 `action`方法修改多笔数据的状态；

也可以在 store 中定义 `actions` 的一个方法来更新：

```js
// store.ts
import { defineStore } from 'pinia'

export default defineStore({
  id: 'testStore',
  state: () => {
    return {
      name: 'pingan8787',
      age: '10',
      addr: 'fujian'
    }
  },
  actions: {
    updateState() {
      this.name = 'hello pingan8787'
      this.age = '18'
      this.addr = 'xiamen'
    }
  }
})
```

使用时：

```js
const changeName = () => {
  componentStoreObj.updateState()
}
```
