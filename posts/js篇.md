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

::: warning
内部作用域能访问的外部,取决于函数定义的位置,和调用无关.
:::

::: tip
所有的全局变量,全局函数都会附加到全局对象中

 > 这称之为全局污染，又称之为全局暴露，或简称污染、暴露
 > 如果要避免污染，需要使用立即执行函数改变其作用域
 > 立即执行函数又称之为IIFE，它的全称是Immediately lnvoked Function Expression
 > IIFE通常用于强行改变作用域

:::

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

## Array 常用方法

| 方法    |   作用   |                    说明                    |
| ------- | :------: | :----------------------------------------: |
| forEach | 遍历数组 |       不返回数组，常用于遍历数组对象       |
| map     | 迭代数组 |      返回新数组，返回处理后的数组元素      |
| filter  | 过滤数组 | 返回新数组，返回的是筛选满足条件的数组元素 |
| reduce  |  累计器  |      返回累计处理的结果，经常用于求和      |

::: info 其他方法

- jion `数组元素拼接成字符串，返回字符串`
- find `查找元素，返回符合测试条件的第一个元素的值，如果没有符合条件的则返回undefined`
- every `检测数组中所有元素，如果所有元素都满足测试条件，则返回true，否则返回false`
- some `检测数组中所有元素，如果有元素满足测试条件，则返回true，否则返回false`
- concat `合并两个数组，生成新的数组`
- sort `对原数组进行排序`
- splice `删除或替换原数组`
- reverse `反转数组`
- findIndex `查找元素的索引值`
  :::

### reduce

`arr.reduce((上一次的值,当前值)=>{},初始值)`

::: warning reduce 执行过程

- 如果没有起始值，则上一次值以数组的第一个数组元素的值
- 每一次循环，把返回值给做为 下一次循环的上一次值
- 如果有起始值，则 起始值做为上一次值
  :::

```js
const arr = [1, 2, 4, 5]
// 没有初始值
let num = arr.reduce((prev, current) => {
  return prev + current
}) // 12
// 有初始值
let num = arr.reduce((prev, current) => {
  return prev + current
}, 10) // 22
```

如果遍历数组对象，计算其中属性的和，一定要加 `初始值`

```js
const arr = [
  { s: 10000, name: 'a' },
  { s: 11000, name: 'b' },
  { s: 12000, name: 'c' }
]

let total = arr.reduce((prev, cur) => {
  cur.s = cur.s * 1.3
  return prev + cur.s
}, 0)
```

### 伪数组 转化为 真数组

- Array.from(arr)

## string 常用方法

::: info 实例方法

- 实例属性 length 用来获取字符串的长度

- split(分隔符) 用来将字符串拆分成数组
- substring (需要截取的第一个字符的索引[,结束的索号]) 用于字符串截取 `结束索引号不包含 截取字符串内`
- startswith(检测字符串[，检测位置索引号]) 检测是否以某字符开头
- includes(搜索的字符审[，检测位置索引号]) 判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false
- toupperCase 用于将字母转换成大写
- toLowerCase 用于将就转换成小写
- indexof 检测是否包含某字符
- endswith 检测是否以某字符结尾
- replace 用于替换字符串，支持正则匹配
- match 用于查找字符串，支持正则匹配
  :::

## 原型链 - 查找规则

- 当访问一个对象的属性(包括方法)时，首先查找这个对象自身有没有该属性。

- 如果没有就查找它的原型 (也就是 \_ _proto_ \_ 指向的 prototype 原型对象)

- 如果还没有就查找原型对象的原型 (object 的原型对象)

- 依此类推一直找到 object 为止 (null)

- proto 对象原型的`意义就在于为对象成员查找机制提供一个方向，或者说一条路线`

- 可以使用 instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上

## 深浅拷贝

### 浅拷贝

- 拷贝对象之后，里面的属性值是简单数据类型直接拷贝值
- 如果属性值是引用数据类型则拷贝的是地址

### 深拷贝

- 拷贝的是 对象 而不是地址

::: tip 常见方法

- 递归函数 拷贝
- lodash/cloneDeep
- JSON.stringfy() 实现
  :::

## 手写防抖函数

### 核心思路

- 声明一个定时器变量
- 事件触发时先判断是否有定时器了，如果有定时器先清除以前的定时器
- 如果没有定时器则开启定时器，记得存到变量里面
- 在定时器里面调用要执行的函数

### 实现

```js
function debounce(fn, t) {
  let timer
  return function () {
    if (timer) clearTimeout(timer)
    timer = setTimeout(function () {
      fn()
    }, t)
  }
}
```

## 手写节流函数

### 核心思路

1. 声明一个定时器变量
2. 事件触发时都先判断是否有定时器了，`如果有定时器则不开启新定时器`
3. 如果没有定时器则开启定时器，记得存到变量里面

- 定时器里面调用执行的函数
- 定时器里面要把定时器清空

### 实现

#### 方式一

```js
function throttle(fn, t) {
  let timer = null
  return function () {
    if (!timer) {
      timer = setTimeout(function () {
        fn()
        timer = null
      }, t)
    }
  }
}
```

::: danger 注意
setTimeout 中是无法清除定时器的，因为定时器还在运作，即使使用了 clearTimeout timer 仍然是有值的 所以使用 null 赋值来清空
:::

#### 方式二

时间戳的方式

```js
function throttle(fn, t) {
  let time = undefined;
  return function () {
    if (!time || Date.now() - time >= t) {
      fn();
      time =  Date.now();
    }
  };
}

```
