---
title: pinia持久化存储
date: 2023-04-01
tags:
  - pinia
  - vite
  - vue3
---

### 安装插件

> npm i pinia-plugin-persist --save

### store 文件夹创建 index 文件

```js
import { createPinia } from 'pinia'
//引入pinia的持久化存储插件
import piniaPluginPersist from 'pinia-plugin-persist'
const pinia = createPinia()
//使用插件
pinia.use(piniaPluginPersist)
export default pinia
```

### store/user.js 模块开启缓存

```js
export const useUserStore = defineStore({
  id: 'user',

  state: () => {
    return {
      name: '张三'
    }
  },

  // 开启数据缓存
  persist: {
    enabled: true
  }
})
```

### 模块文件下添加 缓存配置

```js
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    accessToken: ''
  }),
  actions: {},
  getters: {},
  // 开启数据缓存
  persist: {
    enabled: true,
    strategies: [
      {
        key: 'attendance_user', // 数据默认存在 sessionStorage 里，并且会以 store 的 id 作为 key。
        storage: localStorage // 缓存模式 本地缓存
        //paths: ['age']
      }
    ]
  }
})
```
