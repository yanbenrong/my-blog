---
title: VueRouter避坑经验
description: VueRouter避坑经验
date: 2023-04-04
tags:
  - VueRouter
---

## script-setup 模式下获取路由参数

由于在 script-setup 模式下，没有 this 可以使用，就不能直接通过 `this.$router`或 `this.$route` 来获取路由参数和跳转路由。

当我们需要获取路由参数时，就可以使用 `vue-router`提供的 `useRoute`方法来获取，使用如下：

```html
// A.vue

<script setup lang="ts">
  import { ref, onMounted } from 'vue'
  import router from '@/router'

  import { useRoute } from 'vue-router'

  let detailId = ref<string>('')

  onMounted(() => {
    const route = useRoute()
    detailId.value = route.params.id as string // 获取参数
  })
</script>
```

如果要做路由跳转，就可以使用 `useRouter` 方法的返回值去跳转：

```js
const router = useRouter()
router.push({
  name: 'search',
  query: {
    /**/
  }
})
```
