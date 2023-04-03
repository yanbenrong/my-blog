---
title: pinia的使用
date: 2023-04-03
tags:
  - pinia
---

## 安装使用

1. 安装

```js
    yarn add pinia
    # or with npm
    npm install pinia
```

2. main.js 引入

```js
import { createPinia } from 'pinia'

app.use(createPinia())
```

3. 根目录新建 store/index.js 中写入

```js
import { defineStore } from 'pinia'

export const useStore = defineStore('storeId', {
  state: () => {
    return {
      counter: 0
    }
  },
  getters: {},
  actions: {}
})
```

4. 组件使用

```js
import { useStore } from '../store'
const store = useStore()
```

## State

1. Pinia 定义 state 数据

```js
import { defineStore } from 'pinia'

export const useStore = defineStore('storeId', {
  state: () => {
    return {
      counter: 0,
      name: 'Eduardo',
      isAdmin: true
    }
  },
  getters: {},
  actions: {}
})
```

2. 组件使用 pinia 的 state 数据

```vue
<template>
  <div>
    <h1>A组件</h1>
    {{ name }}
  </div>
</template>

<script setup>
  import { useStore } from '../store'
  const store = useStore()
  let { name } = store
</script>
```

3. 组件修改 pinia 的 state 数据

> 本身 pinia 可以直接修改 state 数据，无需像 vuex 一样通过 mutations 才可以修改，但是上面写的 let { name } = store;这种解构是不可以的，所以要换解构的方式。

```vue
<template>
  <div>
    <h1>A组件</h1>
    {{ name }}
    <button @click="btn">按钮</button>
  </div>
</template>
<script setup>
  import { storeToRefs } from 'pinia'
  import { useStore } from '../store'
  const store = useStore()
  let { name } = storeToRefs(store)
  const btn = () => {
    name.value = '123'
  }
</script>
```

4. 如果 state 数据需要批量更新

```js
import { defineStore } from 'pinia'

export const useStore = defineStore('storeId', {
  state: () => {
    return {
      counter: 0,
      name: 'Eduardo',
      arr: ['a', 'b', 'c']
    }
  },
  getters: {},
  actions: {}
})
```

```vue
<template>
  <div>
    <h1>A组件</h1>
    {{ name }}
    {{ counter }}
    {{ arr }}
    <button @click="btn">按钮</button>
  </div>
</template>
<script setup>
  import { storeToRefs } from 'pinia'
  import { useStore } from '../store'
  const store = useStore()
  let { name, counter, arr } = storeToRefs(store)
  const btn = () => {
    //批量更新
    store.$patch((state) => {
      state.counter++
      state.arr.push(4)
      state.name = '456'
    })
  }
</script>
```

::: tip
使用$patch 进行批量更新
:::

## actions

> actions 就比较简单了，写入方法，比如我们可以让 state 中的某一个值+=，而且传入参数

```js
import { defineStore } from 'pinia'

export const useStore = defineStore('storeId', {
  state: () => {
    return {
      counter: 0
    }
  },
  getters: {},
  actions: {
    changeCounter(val) {
      this.counter += val
    }
  }
})
```

```vue
<template>
  <div>
    <h1>A组件</h1>
    {{ counter }}
    <button @click="add">加10</button>
  </div>
</template>
<script setup>
  import { storeToRefs } from 'pinia'
  import { useStore } from '../store'
  const store = useStore()
  let { counter } = storeToRefs(store)
  const add = () => {
    store.changeCounter(10)
  }
</script>
```

## getters

> getters 和 vuex 的 getters 几乎类似，也是有缓存的机制

```js
import { defineStore } from 'pinia'

export const useStore = defineStore('storeId', {
  state: () => {
    return {
      counter: 0
    }
  },
  getters: {
    counterPar() {
      console.log(111)
      return this.counter + 100
    }
  },
  actions: {}
})
```

```vue
<template>
  <div>
    {{ counterPar }}
    {{ counterPar }}
    {{ counterPar }}
    <h1>A组件</h1>
    {{ counter }}
  </div>
</template>
<script setup>
  import { storeToRefs } from 'pinia'
  import { useStore } from '../store'
  const store = useStore()
  let { counter, counterPar } = storeToRefs(store)
</script>
```
