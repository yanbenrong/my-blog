---
title: vue3
description: vue3
date: 2023-04-04
tags:
  - vue3
---

> [文档地址](https://cn.vuejs.org/guide/introduction.html)

## 生命周期

`vue2与vue3生命周期变化`

| vue2          |      vue3       |                执行时间说明                |
| ------------- | :-------------: | :----------------------------------------: |
| beforeCreate  |      setup      |               组件创建前执行               |
| created       |      setup      |               组件创建后执行               |
| beforeMount   |  onBeforeMount  |          组件挂载到节点上之前执行          |
| mounted       |    onMounted    |             组件挂载完成后执行             |
| updated       | onBeforeUpdate  |              组件更新之前执行              |
| beforeUpdate  |    onUpdated    |            组件更新完成之后执行            |
| beforeDestroy | onBeforeUnmount |              组件卸载之前执行              |
| destroyed     |   onUnmounted   |             组件卸载完成后执行             |
| errorCaptured | onErrorCaptured | 当捕获一个来自子孙组件的异常时激活钩子函数 |

```js
// A.vue
<script setup lang="ts">
import { ref, onMounted } from "vue";
let count = ref<number>(0);

onMounted(() => {
  count.value = 1;
})
</script>
```

## script-setup 模式中父组件获取子组件的数据

`可以使用全局编译器宏的defineExpose宏，将子组件中需要暴露给父组件获取的参数，通过 {key: vlaue}方式作为参数即可，父组件通过模版 ref 方式获取子组件实例，就能获取到对应值：`

```vue
// 子组件
<script setup>
  let name = ref('pingan8787')
  defineExpose({ name }) // 显式暴露的数据，父组件才可以获取
</script>

// 父组件
<Chlid ref="child"></Chlid>
<script setup>
  let child = ref(null)
  child.value.name //获取子组件中 name 的值为 pingan8787
</script>
```

::: tip 注意

- 全局编译器宏只能在 script-setup 模式下使用
- script-setup 模式下，使用宏时无需 import 可以直接使用；
- script-setup 模式一共提供了 4 个宏，包括：defineProps、defineEmits、[defineExpose](https://cn.vuejs.org/api/sfc-script-setup.html#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8C%87%E4%BB%A4)、withDefaults。
  :::

## 为 props 提供默认值

> [definedProps 文档](https://link.juejin.cn/?target=https%3A%2F%2Fv3.cn.vuejs.org%2Fapi%2Fsfc-script-setup.html%23defineprops-%25E5%2592%258C-defineemits)

> [withDefaults 文档](https://link.juejin.cn/?target=https%3A%2F%2Fv3.cn.vuejs.org%2Fapi%2Fsfc-script-setup.html%23%25E4%25BB%2585%25E9%2599%2590-typescript-%25E7%259A%2584%25E5%258A%259F%25E8%2583%25BD)

`使用 defineProps宏可以用来定义组件的入参，使用如下：`

```vue
<script setup lang="ts">
  let props = defineProps<{
    schema: AttrsValueObject
    modelValue: any
  }>()
</script>
```

这里只定义 props 属性中的 schema 和 modelValue 两个属性的类型， defineProps 的这种声明的不足之处在于，它没有提供设置 props 默认值的方式。

其实我们可以通过 withDefaults 这个宏来实现：

```vue
<script setup lang="ts">
  let props = withDefaults(
    defineProps<{
      schema: AttrsValueObject
      modelValue: any
    }>(),
    {
      schema: [],
      modelValue: ''
    }
  )
</script>
```

## 配置全局自定义参数

在 Vue2.x 中我们可以通过`Vue.prototype`添加全局属性 property。但是在 Vue3.x 中需要将 `Vue.prototype` 替换为 `config.globalProperties` 配置：

```js
// Vue2.x
Vue.prototype.$api = axios
Vue.prototype.$eventBus = eventBus

// Vue3.x
const app = createApp({})
app.config.globalProperties.$api = axios
app.config.globalProperties.$eventBus = eventBus
```

使用时需要先通过 vue 提供的 `getCurrentInstance` 方法获取实例对象：

```vue
// A.vue
<script setup lang="ts">
  import { ref, onMounted, getCurrentInstance } from 'vue'

  onMounted(() => {
    const instance = <any>getCurrentInstance()
    const { $api, $eventBus } = instance.appContext.config.globalProperties
    // do something
  })
</script>
```

## v-model

- Vue2

```html
<ChildComponent v-model="pageTitle" />

<!-- 是以下的简写: -->
<ChildComponent :value="pageTitle" @input="pageTitle = $event" />
```

在子组件中，如果要对某一个属性进行双向数据绑定，只要通过 `this.$emit('update:myPropName', newValue)` 就能更新其`v-model`绑定的值。

- Vue3

```html
<ChildComponent v-model="pageTitle" />

<!-- 是以下的简写: -->

<ChildComponent :modelValue="pageTitle" @update:modelValue="pageTitle = $event" />
```

script-setup 模式下就不能使用 `this.$emit` 去派发更新事件，毕竟没有 `this`，这时候需要使用前面有介绍到的 `defineProps`、`defineEmits` 两个宏来实现：

```vue
// 子组件 child.vue // 文档：https://v3.cn.vuejs.org/api/sfc-script-setup.html#defineprops-%E5%92%8C-defineemits
<script setup lang="ts">
  import { ref, onMounted, watch } from 'vue'
  const emit = defineEmits(['update:modelValue']) // 定义需要派发的事件名称

  let curValue = ref('')
  let props = withDefaults(
    defineProps<{
      modelValue: string
    }>(),
    {
      modelValue: ''
    }
  )

  onMounted(() => {
    // 先将 v-model 传入的 modelValue 保存
    curValue.value = props.modelValue
  })

  watch(curValue, (newVal, oldVal) => {
    // 当 curValue 变化，则通过 emit 派发更新
    emit('update:modelValue', newVal)
  })
</script>

<template>
  <div></div>
</template>

<style lang="scss" scoped></style>
```

父组件使用:

```vue
// 父组件 father.vue

<script setup lang="ts">
  import { ref, onMounted, watch } from 'vue'
  let curValue = ref('')

  watch(curValue, (newVal, oldVal) => {
    console.log('[curValue 发生变化]', newVal)
  })
</script>

<template>
  <Child v-model="curValue"></Child>
</template>

<style lang="scss" scoped></style>
```
