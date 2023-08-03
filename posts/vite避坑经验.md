---
title: vite&vue3避坑经验
description: vite避坑经验
date: 2023-04-04
tags:
  - vite
  - vue3
---

## Vite 动态导入的使用问题

> [文档地址](https://cn.vitejs.dev/guide/features.html#glob-import)

在 webpack 中可以通过 `require.context` 动态导入文件：

```js
// https://webpack.js.org/guides/dependency-management/
require.context('./test', false, /\.test\.js$/)
```

在 Vite 中，我们可以使用这两个方法来动态导入文件：

- `import.meta.glob`

该方法匹配到的文件默认是`懒加载`，通过`动态导入`实现，`构建时会分离独立的 chunk`，是`异步导入`，返回的是 Promise，需要做异步操作，使用方式如下：

```js
const Components = import.meta.glob('../components/**/*.vue')

// 转译后：
const Components = {
  './components/a.vue': () => import('./components/a.vue'),
  './components/b.vue': () => import('./components/b.vue')
}
```

- `import.meta.globEager`

该方法是`直接导入所有模块`，并且是`同步导入`，返回结果直接通过 `for...in`循环就可以操作，使用方式如下

```js
const Components = import.meta.globEager('../components/**/*.vue')

// 转译后：
import * as __glob__0_0 from './components/a.vue'
import * as __glob__0_1 from './components/b.vue'
const modules = {
  './components/a.vue': __glob__0_0,
  './components/b.vue': __glob__0_1
}
```

如果仅仅使用异步导入 Vue3 组件，也可以直接使用 Vue3 defineAsyncComponent API 来加载：

```js
// https://v3.cn.vuejs.org/api/global-api.html#defineasynccomponent

import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => import('./components/AsyncComponent.vue'))

app.component('async-component', AsyncComp)
```

## Vite 配置 alias 类型别名

> [文档地址](https://cn.vitejs.dev/config/#resolve-alias)

`当项目比较复杂的时候，经常需要配置 alias 路径别名来简化一些代码：`

```js
import Home from '@/views/Home.vue'
```

在 Vite 中配置也很简单，只需要在 `vite.config.ts` 的 `resolve.alias`中配置即可：

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
const path = require('path')
// vite.config.ts
export default defineConfig({
  base: './',
  resolve: {
    alias: {
      '@': path.join(__dirname, './src')
    }
  }
  // 省略其他配置
})
```

如果使用的是 TypeScript 时，编辑器会提示路径不存在的警告 ⚠️，这时候可以在`tsconfig.json`中添加`compilerOptions.paths`的配置：

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

## vite 配置全局 scss

> [文档地址](https://cn.vitejs.dev/config/#css-preprocessoroptions)

当我们需要使用 scss 配置的主题变量（如· $primary·）、mixin 方法（如 ·@mixin lines·）等时，如：

```html
<script setup lang="ts"></script>
<template>
  <div class="container"></div>
</template>

<style scoped lang="scss">
  .container {
    color: $primary;
    @include lines;
  }
</style>
```

我们可以将 scss 主题配置文件，配置在 `vite.config.ts` 的 `css.preprocessorOptions.scss.additionalData`中

```ts
// vite.config.ts
export default defineConfig({
  base: './',
  css: {
    preprocessorOptions: {
      // 添加公共样式
      scss: {
        additionalData: '@import "./src/style/style.scss";'
      }
    }
  },
  plugins: [vue()]
  // 省略其他配置
})
```

如果不想使用 scss 配置文件，也可以直接写成 scss 代码：

```ts
export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: '$primary: #993300'
      }
    }
  }
})
```

## main.js中 App.vue找不到类型声明

![](https://img-blog.csdnimg.cn/8dc16ae200b0475fb616858d8c58da50.png)

解决办法：

1.在src文件夹找到 vite-env.d.ts 加入以下代码：

```js
declare module "*.vue" {
  import type { DefineComponent } from "vue";
 
  const vueComponent: DefineComponent<{}, {}, any>;
 
  export default vueComponent;
}
```

2.若无vite-env.d.ts文件，则在项目根目录创建 env.d.ts 文件（如果已有，则在文件中追加）以下代码：

```js
declare module "*.vue" {
  import type { DefineComponent } from "vue";
 
  const vueComponent: DefineComponent<{}, {}, any>;
 
  export default vueComponent;
}
```

## main.js中找不到@/router的声明

![](https://img-blog.csdnimg.cn/c6e70ffd91db43ffa598287976732fb6.png)

解决办法：

```js
declare module '*@/router' {
  import type { DefineComponent } from 'vue-router'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

## 修改样式-深度选择器

```css
:deep(.el-input__wrapper){
    .....
}
```

