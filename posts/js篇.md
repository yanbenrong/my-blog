---
title: js 篇
description: js 篇
date: 2023-04-02
tags:
  - javascript
  - 面试题
---

## 作用域链

> 作用域链本质上是底层的**变量查找机制**。

### 作用域链查找规则是怎样的？

- 在函数被执行时，会优先在当前函数作用域查找变量
- 如果当前作用域中没有查找到，会依次逐级查找父级作用域知道全局作用域。

## 垃圾回收机制

::: details 注意
JavaScript 的垃圾回收机制主要负责回收堆内存中的对象
栈内存的回收则是由 JavaScript 引擎自动管理的，不需要额外的垃圾回收机制来处理。栈内存主要用于存储基本数据类型和函数的执行上下文等数据。当函数执行完成或者当前执行上下文被弹出时，栈内存中的数据就会被自动释放，无需手动清理。
:::

### 引用计数

> ie 使用的引用计数算法，定义‘内存不在使用’，就是看一个对象是否有指向它的引用，如果没有引用就回收对象。

::: tip
现代浏览器已经不使用引用计数算法了
:::

#### 算法

1. 跟踪记录被引用的次数
2. 如果被引用了一次，那么就记录次数 1,多次引用会累加++
3. 如果减少一个引用就减 1 --
4. 如果引用次数是 0，则释放内存

### 标记清除

> 现代浏览器大多都是基于标记清除算法的改进算法 `例如分代垃圾回收、增量标记和清除等算法`

#### 核心

1. 标记清除算法将“不再使用的对象”定义为“无法达到的对象”。
2. 就是从根部（在 JS 中就是全局对象）出发定时扫描内存中的对象。凡是能从根部到达的对象，都是还需要使用的。
3. 那些无法由根部出发触及到的对象被标记为不再使用，稍后进行回收

   ::: tip 优点
   优点是可以有效地回收循环引用的对象，即使两个对象相互引用，只要它们都不再被引用，它们都可以被清
   除。
   :::

## 闭包

### 怎么理解闭包？

> 闭包 = 内层函数 + 外层函数的变量

### 闭包的作用

1. 封闭数据实现私有，外部也可以使用函数内部的变量。
2. 闭包很有用处，他允许将函数与其所操作的某些数据（环境）关联起来。

### 闭包可能导致的问题？

> 内存泄漏

## 箭头函数

> 目的： 更简短的函数写法，并且不绑定 this，语法比函数表达式更简洁。

> 注意： 箭头函数属于表达式函数，不存在函数提升。

### 基本语法

```js
// 基本语法
const fn = (x) => {
  console.log(x)
}
// 只有一个形参的时候可以省略小括号
const fn = (x) => {
  console.log(x)
}
// 只有一行代码的时候可以省略大括号
const fn = (x) => console.log(x)
// 只有一行代码的时候可以省略return
const fn = (x) => x + x
// 可以直接返回一个对象
const fn = (x) => ({ name: x })
```

### 箭头函数参数

> 箭头函数没有 arguements 动态参数，但是有 ...args 剩余参数

### 箭头函数 this

**_箭头函数不会创建自己的 this，只会从自己的作用域链的上一层沿用 this_**

```js
const fn = () => {
  console.log(this)
}
fn() // window
```

```js
const obj = {
  name: 'xxx',
  sayHi: () => {
    console.log(this)
  }
}

obj.sayHi() // window
```

```js
const obj = {
  name: 'xxx',
  sayHi: function () {
    const count = () => {
      console.log(this)
    }
    count()
  }
}
obj.sayHi() // obj
```

```js
const btn = document.querySelector('.btn')

btn.addEventlistene('click', function () {
  console.log(this) // dom 对象
})
btn.addEventlistene('click', () => {
  console.log(this) // window
})
```

## new 实例化 执行过程

::: info 过程

1. 创建新的空对象
2. 构造函数 this 指向新对象
3. 执行构造函数代码，修改 this 添加新属性
4. 返回新对象

:::

## Object 常用静态方法

- object.keys

  > Object.keys 静态方法获取对象中的所有属性

  ```js
  const o = { name: 'xxx', age: 18 }
  console.log(Object.keys(o)) // ['name','age']
  ```

  ::: tip 注意
  返回一个数组
  :::

- object.values

  > Object.values 静态方法获取对象中的所有属性

  ```js
  const o = { name: 'xxx', age: 18 }
  console.log(Object.values(o)) // ['xxx',18]
  ```

  ::: tip 注意
  返回一个数组
  :::

- object.assign

  > Object.assign 静态方法常用于对象的拷贝

  > 给对象添加新的属性

  > 第一个参数 目标对象 后面的参数 源对象

  ```js
  // 把o 拷贝给 obj
  const o = { name: 'xxx', age: 18 }
  const obj = {}
  Object.assign(obj, o) //  { name: 'xxx', age: 18 }

  // 给o 添加属性
  Object.assign(o, { gender: '男' })
  ```

::: tip 注意

1. Object.assign 方法实行的是浅拷贝，而不是深拷贝

```js
const obj1 = { a: { b: 1 } }
const obj2 = Object.assign({}, obj1)

obj1.a.b = 2
obj2.a.b // 2
```

2. 同名属性的替换

> 对于这种嵌套的对象，一旦遇到同名属性，Object.assign 的处理方法是替换，而不是添加。

```js
const target = { a: { b: 'c', d: 'e' } }
const source = { a: { b: 'hello' } }
Object.assign(target, source) // { a: { b: 'hello' } }
```

3. 数组的处理

> Object.assign 可以用来处理数组，但是会把数组视为对象。

```js
Object.assign([1, 2, 3], [4, 5]) // [4, 5, 3]
```

4. 取值函数的处理

> Object.assign 只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。

```js
const source = {
  get foo() {
    return 1
  }
}
const target = {}

Object.assign(target, source)
// { foo: 1 }
```

:::

## Array 常用静态方法

| 方法    |   作用   |                    说明                    |
| ------- | :------: | :----------------------------------------: |
| forEach | 遍历数组 |       不返回数组，常用于遍历数组对象       |
| map     | 迭代数组 |      返回新数组，返回处理后的数组元素      |
| filter  | 过滤数组 | 返回新数组，返回的是筛选满足条件的数组元素 |
| reduce  |  累计器  |      返回累计处理的结果，经常用于求和      |
