---
title: pinia持久化存储
date: 2023-04-01
tags:
  - pinia
  - vite
  - vue3
---

### 安装插件

> pinia-plugin-persist

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
        key: 'attendance_user',
        storage: localStorage // 缓存模式 本地缓存
        //paths: ['age']
      }
    ]
  }
})
```
