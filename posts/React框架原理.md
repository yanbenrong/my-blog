---
title: React框架原理学习笔记
description: React框架原理笔记
date: 2023-08-03
tags:
  - React
---

#  React 和 Vue 描述页面的区别

> 面试题：React 和 Vue 是如何描述 UI 界面的？有一些什么样的区别？

**标准且浅显的回答：**

> React 中使用的是 JSX，Vue 中使用的是模板来描述界面

前端领域经过长期的发展，目前有两种主流的描述 UI 的方案：

- JSX
- 模板

## JSX 历史来源

JSX 最早起源于 React 团队在 React 中所提供的一种类似于 XML 的 ES 语法糖：

```
const element = <h1>Hello</h1>
```

经过 Babel 编译之后，就会变成：

```
// React v17 之前
var element = React.createElement("h1", null, "Hello");

// React v17 之后
var jsxRuntime = require("react/jsx-runtime");
var element = jsxRuntime.jsx("h1", {children: "Hello"});
```

无论是 17 之前还是 17 之后，执行了代码后会得到一个对象：

```
{
  "type": "h1",
  "key": null,
  "ref": null,
  "props": {
    "children": "Hello"
  },
  "_owner": null,
  "_store": {}
}
```

这个其实就是大名鼎鼎的虚拟 DOM。

React 团队认为，UI 本质上和逻辑是有耦合部分的：

- 在 UI 上面绑定事件
- 数据变化后通过 JS 去改变 UI 的样式或者结构

作为一个前端工程师，JS 是用得最多，所以 React 团队思考屏蔽 HTML，整个都用 JS 来描述 UI，因为这样做的话，可以让 UI 和逻辑配合得更加紧密，所以最终设计出来了类 XML 形式的 JS 语法糖

由于 JSX 是 JS 的语法糖（本质上就是 JS），因此可以非常灵活的和 JS 语法组合使用，例如：

- 可以在 if 或者 for 当中使用 jsx
- 可以将 jsx 赋值给变量
- 可以将 jsx 当作参数来传递，当然也可以在一个函数中返回一段 jsx

```
function App({isLoading}){
  if(isLoading){
    return <h1>loading...</h1>
  }
  return <h1>Hello World</h1>;
}
```

这种灵活性就使得 jsx 可以轻松的描述复杂的 UI，如果和逻辑配合，还可以描述出复杂 UI 的变化。

使得 React 社区的早期用户可以快速实现各种复杂的基础库，丰富社区生态。又由于生态的丰富，慢慢吸引了更多的人来参与社区的建设，从而源源不断的形成了一个正反馈。

## 模板的历史来源

模板的历史就要从后端说起。

在早期前后端未分离的时候，最流行的方案就是使用模板引擎，模板引擎可以看作是在正常的 HTML 上面进行挖坑（不同的模板引擎语法不一样），挖了坑之后，服务器端会将数据填充到挖了坑的模板里面，生成对应的 html 页面返回给客户端。

![image-20211103140631869](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2021-11-03-060632.png)

所以在那个时期前端人员的工作，主要是 html、css 和一些简单的 js 特效（轮播图、百叶窗...），写好的 html 是不能直接用的，需要和后端确定用的是哪一个模板引擎，接下来将自己写好的 html 按照对应模板引擎的语法进行挖坑

![image-20211103143319523](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2021-11-03-063319.png)

不同的后端技术对应的有不同的模板引擎，甚至同一种后端技术，也会对应很多种模板引擎，例如：

- *Java*（*JSP、Thymeleaf、Velocity、Freemarker*）
- *PHP*（*Smarty、Twig、HAML、Liquid、Mustache、Plates*）
- *Python*（*pyTenjin、Tornado.template、PyJade、Mako、Jinja2*）
- *node.js*（*Jade、Ejs、art-template、handlebars、mustache、swig、doT*）

下面列举几个模板引擎代码片段

twig 模板引擎

```
基本语法
{% for user in users %}
    * {{ user.name }}
{% else %}
    No users have been found.
{% endfor %}

指定布局文件
{% extends "layout.html" %}
定义展示块
{% block content %}
    Content of the page...
{% endblock %}
```

blade 模板引擎

```
<html>
    <head>
        <title>应用程序名称 - @yield('title')</title>
    </head>
    <body>
       @section('sidebar')
            这是 master 的侧边栏。
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

EJS 模板引擎

```
<h1>
    <%=title %>
</h1>
<ul>
    <% for (var i=0; i<supplies.length; i++) { %>
    <li>
        <a href='supplies/<%=supplies[i] %>'>
            <%= supplies[i] %>
        </a>
    </li>
    <% } %>
</ul>
```

这些模板引擎对应的模板语法就和 Vue 里面的模板非常的相似。

现在随着前后端分离开发的流行，已经没有再用模板引擎的模式了，后端开发人员只需要书写数据接口即可。但是如果让一个后端人员来开前端的代码，那么 Vue 的模板语法很明显对于后端开发人员来讲要更加亲切一些。

最后我们做一个总结，虽然现在前端存在两种方式：JSX 和模板的形式都可以描述 UI，但是出发点是不同

模板语法的出发点是，既然前端框架使用 HTML 来描述 UI，那么我们就扩展 HTML，让 HTML 种能够描述一定程度的逻辑，也就是“从 UI 出发，扩展 UI，在 UI 中能够描述逻辑”。

JSX 的出发点，既然前端使用 JS 来描述逻辑，那么就扩展 JS，让 JS 也能描述 UI，也就是“从逻辑出发，扩展逻辑，描述 UI”。

这两者虽然都可以描述 UI，但是思路或者说方向是完全不同的，从而造成了整体框架架构上面也是不一样的。

## 真题解答

> 题目：React 和 Vue 是如何描述 UI 界面的？有一些什么样的区别？
>
> 参考答案
>
> 在 React 中，使用 JSX 来描述 UI。因为 React 团队认为UI 本质上与逻辑存在耦合的部分，作为前端工程师，JS 是用的最多的，如果同样使用 JS 来描述 UI，就可以让 UI 和逻辑配合的更密切。
>
> 使用 JS 来描述页面，可以更加灵活，主要体现在：
>
> - 可以在 if 语句和 for 循环中使用 JSX
> - 可以将 JSX 赋值给变量
> - 可以把 JSX 当作参数传入，以及在函数中返回 JSX
>
> 而模板语言的历史则需要从后端说起。早期在前后端未分离时代，后端有各种各样的模板引擎，其本质是扩展了 HTML，在 HTML 中加入逻辑相关的语法，之后在动态的填充数据进去。如果单看 Vue 中的模板语法，实际上和后端语言中的各种模板引擎是非常相似的。
>
> 总结起来就是：
>
> 模板语法的出发点是，既然前端框架使用 HTML 来描述 UI，那么就扩展 HTML 语法，使它能够描述逻辑，也就是“从 UI 出发，扩展 UI，在 UI 中能够描述逻辑”。
>
> 而 JSX 的出发点是，既然前端使用 JS 来描述逻辑，那么就扩展 JS 语法，让它能够描述 UI，也就是“从逻辑出发，扩展逻辑，描述 UI”。
>
> 虽然这两者都达到了同样的目的，但是对框架的实现产生了不同的影响。

#  前端框架的分类

> 面试题：现代前端框架不仅仅是 React、Vue，还出现了像 Svelte、Solid.js 之类的框架，你觉得这些新框架相比 React、Vue 有什么样的区别？

## 自变量和因变量

现代前端框架，有一个非常重要的特点，那就是基于状态的声明式渲染。如果要概括的话，可以使用一个公式：

> UI = f（state）

- state：当前视图的一个状态
- f：框架内部的一个运行机制
- UI：宿主环境的视图描述

这里和初中的一个数学代数知识非常相似：

```
2x + 1 = y
```

x 的变化会导致 y 的变化，x 就被称之为自变量，y 就被称之为因变量。类比上面 UI 的公式，state 就是自变量，state 的变化会导致最终计算出来的 UI 发生变化，UI 在这里就是因变量。

目前在 React 中有很多 Hook，例如：

```
const [x, setX] = useState(0);
```

比如上面的代码，我们就是定义了一个自变量

```
function App(){
  const [x, setX] = useState(0);
  return <div onClick={()=>setX(x+1)}>{x}</div>
}
```

上面的 useState 这个 hook 可以看作是定义了一个自变量，自变量一变化，就会到导致依赖它的因变量发生变化，在上面的例子中，返回的 jsx 所描述的 UI 就是因变量。

因变量又可以分为两类：

- 没有副作用的因变量
- 有副作用的因变量

**没有副作用的因变量**

在 React 中，useMemo 就是定义一个没有副作用的因变量

```
const y = useMemo(() => x * 2 + 1, [x]);
```

在上面的代码中，我们使用 useMemo 定义了一个没有副作用的因变量 y，y 的值取决于 x 的值，x 的值一变化，y 的值也会跟着变化

**有副作用的因变量**

在 React 中，可以使用 useEffect 来定义一个有副作用的因变量

```
useEffect(() => document.title = x, [x]);
```

上面的代码依赖于自变量 x 的变化，当 x 发生变化的时候，会修改页面的标题，这就是一个副作用操作。

那么接下来，我们来总结一下：自变量的变化，会导致三种情况的因变量发生改变：

- 自变量的变化，导致 UI 因变量变化

```
function Counter(){
  const [num, setNum] = useState(0);
  return (
    <div onClick={()=>setNum(num+1)}>{num}</div>
  );
}
```

- 自变量的变化，导致无副作用的因变量发生变化

```
function Counter(){
  const [num, setNum] = useState(0);
  const fiexedNum = useMemo(()=>num.toFiexed(2), [num]);
  return (
    <div onClick={()=>setNum(num+1)}>{fiexedNum}</div>
  );
}
```

- 自变量的变化，导致有副作用的因变量发生变化

```
function Counter(){
  const [num, setNum] = useState(0);
  useEffect(()=>document.title=num, [num]);
  return (
    <div onClick={()=>setNum(num+1)}>{num}</div>
  );
}
```

## 框架的分类

上面我们介绍了自变量和因变量，state 实际上就是自变量，自变量的变化直接或者间接的改变了 UI，上面的公式实际上还可以分为两个步骤：

- 根据自变量 state 计算出 UI 的变化
- 根据 UI 的变化执行具体的宿主环境的 API

以前端工程师最熟悉的浏览器为例，那么第二个步骤就是执行 DOM 相关 API，对于这个步骤来讲，不同的框架实际上实现基本是相同的，这个步骤不能作为框架分类的依据，差别主要体现在步骤一上面，这个（步骤一）也是针对目前各大框架的一个分类的依据。

接下来我们来看一个应用的示例：

该应用由三个组件组成

![image-20230222161811030](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-22-081811.png)

A 组件是整个应用的根组件，在这个根组件中，有一个自变量 a，a 的变化会导致 UI 的重新渲染。

![image-20230222162329389](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-22-082329.png)

上图表示在 A 组件中引入了一个因变量 b，A 组件中的自变量 a 的改变会导致因变量 b 的改变，而这个因变量 b 又作为 props 传递到了子组件 B 当中。

B 组件中也有一个自变量 c，在该组件中还接收从父组件 A 传递过来的 props b，最终在 UI 中渲染 b + c

![image-20230222163203653](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-22-083204.png)

在组件 C 中，接收从根组件 A 传递过来的数据 a，从而 a 变成 C 组件的一个自变量。

接下来我们来总结一下，各个组件中所包含的自变量：

- A 组件
  - 自变量 a
  - a 的因变量 b
- B 组件
  - 从 A 组件传递过来的自变量 b
  - 自变量 c
- C 组件
  - 从 A 组件传递过来的自变量 a

理清楚自变量之后，我们就可以从三个维度去整理自变量和不同维度之间的关系。

**自变量与 UI 的对应关系**

从 UI 层面去考虑的话，自变量的变化会导致哪些 UI 发生变化？

- a 变化导致 A 的 UI 中的 {a} 变化
- a 变化导致因变量 b 变化，导致 B 的 UI 中的 {b+c} 变化
- a 变换导致 C 的 UI 中的 {a} 变化
- a 变化导致 C 的 UI 中的 {a.toFixed(2)} 变化
- c 变化导致 B 的 UI 中的 {b+c} 变化

总共我们梳理出来的 UI 变化路径有 5 条，接下来我们要做的事情就是根据梳理出来的变化路径执行具体的 DOM 操作即可。

**自变量与组件的对应关系**

从组件的层面去考虑的话，自变量的变化会导致哪些组件发生变化呢？

- a 变化导致 A 组件 UI 变化
- a 变化导致 b 变化，从而导致 B 组件的UI 变化
- a 变化导致组件 C 的UI 变化
- c 变化导致组件 B 的 UI 变化

相较于上面的自变量与 UI 的对应关系，当我们考虑自变量与组件之间的关系时，梳理出来的路径从 5 条变成了 4 条。虽然路径减少了，但是在运行的时候，需要进行额外的操作，就是确定某一个组件发生变化时，组件内部的 UI 需要发生变化的部分。例如，通过路径 4 只能明确 B 组件发生了变化，但是具体发生了什么变化，还需要组件内部进行进一步的确定。

**自变量与应用的对应关系**

最后我们考虑自变量和应用之间的关系，那么路径就变成了：

- a 变化导致应用中发生 UI 变化
- c 变化导致应用中发生 UI 变化

整体路径从 4 条减少为了 2 条，虽然路径减少了，但是要做的额外的工作更多了。比如 a 的变化会导致应用中的 UI 发生变化，那么究竟是哪一部分的 UI ？这些需要额外的进行确定。

最后我们可以总结一下，前端框架需要关注自变量和 x（UI、组件、应用） 的对应关系，随着 x 的抽象层级不断下降，自变量到 UI 变化的路径条数就会增多。路径越多，则意味着前端框架在运行时消耗在“寻找自变量与 UI 对应关系”上面的时间越少。

根据上面的特点，我们就可以针对现代前端框架分为三大类：

- 元素级框架
- 组件级框架
- 应用级框架

以常见的前端框架为例，React 属于应用级框架，Vue 属于组件级的框架，而新的 Svelte、Solid.js 属于元素级框架。

## 真题解答

> 题目：现代前端框架不仅仅是 React、Vue，还出现了像 Svelte、Solid.js 之类的框架，你觉得这些新框架相比 React、Vue 有什么样的区别？
>
> 参考答案：
>
> 所有的现代前端框架，都有一个非常重要的特点，那就是“基于状态的声明式渲染”。概括成一个公式的话，那就是 UI = f（state）
>
> 这里有一点类似于初中数学中自变量与因变量之间的关系。例如在上面的公式中，state 就是一个自变量，state 的变化会导致 UI 这个因变量发生变化。
>
> 不同的框架，在根据自变量（state）的变化计算出 UI 的变化这一步骤有所区别，自变量和 x（应用、组件、UI）的对应关系，随着 x 抽象的层级不断下降，“自变量到 UI 变化”的路径则不断增多。路径越多，则意味着前端框架在运行时消耗在寻找“自变量与 UI 的对应关系”上的时间越少。
>
> 以“与自变量建立对应关系的抽象层级”可以作为其分类的依据，按照这个标准，前端框架可以分为以下三类：
>
> - 元素级框架
> - 组件级框架
> - 应用级框架
>
> 以常见的前端框架为例，React 属于应用级框架，Vue 属于组件级框架，Svelte、Solid.js 属于元素级框架。

#  虚拟 DOM

> 面试题：什么是虚拟DOM？其优点有哪些？

**标准且浅显的答案**

> 虚拟dom本质上就是一个普通的 JS 对象，用于描述视图的界面结构

虚拟 DOM 最早是由 React 团队提出来的，因此 React 团队在对虚拟 DOM 的定义上面有绝对的话语权。

> [https://react.docschina.org/docs/faq-internals.html](https://gitee.com/link?target=https%3A%2F%2Freact.docschina.org%2Fdocs%2Ffaq-internals.html)

**Virtual DOM 是一种编程概念。**在这个概念里， UI 以一种理想化的，或者说“虚拟的”表现形式被保存于内存中。

也就是说，只要我们有一种方式，能够将真实 DOM 的层次结构描述出来，那么这就是一个虚拟 DOM。

![image-20230223114000816](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-23-034001.png)

在 React 中，React 团队使用的是 JS 对象来对 DOM 结构进行一个描述。但是很多人会直接把 JS 对象和虚拟 DOM 划等号，这种理解是不太准确的，比较片面的。

虚拟 DOM 和 JS 对象之间的关系：**前者是一种思想，后者是一种思想的具体实现。**

## 为什么需要虚拟 DOM

使用虚拟 DOM 主要有两个方面的优势：

- 相较于 DOM 的体积优势和速度优势
- 多平台的渲染抽象能力

**相较于 DOM 的体积优势和速度优势**

首先我们需要明确一个点，JS 层面的计算速度要比 DOM 层面的计算要快：

- DOM 对象最终要被浏览器渲染出来之前，浏览器会有很多工作要做（浏览器的渲染原理）
- DOM 对象上面的属性也非常非常多

```
const div = document.createElement("div");
for(let i in div){console.log(i + " ")}
```

操作 JS 对象的时间和操作 DOM 对象的时间是完全不一样的。

JS 层面的计算速度要高于 DOM 层面的计算速度。

此时有一个问题：虽然使用了 JS 对象来描述 UI，但是最终不还是要用原生 DOM API 去操作 DOM 么？

虚拟 DOM 在第一次渲染页面的时候，并没有什么优势，速度肯定比直接操作原生 DOM API 要慢一些，虚拟 DOM 真正体现优势是在更新阶段。

根据 React 团队的研究，在渲染页面时，相比使用原生 DOM API，开发人员更加倾向于使用 innerHTML

```
let newP = document.createElement("p");
let newContent = document.createTextNode("this is a test");
newP.appendChild(newContent);
document.body.appendChild(newP);
document.body.innerHTML = `
	<p>
		this is a test
	</p>
`;
```

因此在使用 innerHTML 的时候，就涉及到了两个层面的计算：

- JS 层面：解析字符串
- DOM 层面：创建对应的 DOM 节点

接下来我们加入虚拟 DOM 来进行对比：

|              | innerHTML           | 虚拟 DOM            |
| ------------ | ------------------- | ------------------- |
| JS 层面计算  | 解析字符串          | 创建 JS 对象        |
| DOM 层面计算 | 创建对应的 DOM 节点 | 创建对应的 DOM 节点 |

虚拟 DOM 真正发挥威力的时候，是在更新阶段

innerHTML 进行更新的时候，要全部重新赋值，这意味着之前创建的 DOM 节点需要全部销毁掉，然后重新进行创建

但是虚拟 DOM 只需要更新必要的 DOM 节点即可

|              | innerHTML               | 虚拟 DOM            |
| ------------ | ----------------------- | ------------------- |
| JS 层面计算  | 解析字符串              | 创建 JS 对象        |
| DOM 层面计算 | 销毁原来所有的 DOM 节点 | 修改必要的 DOM 节点 |
| DOM 层面计算 | 创建对应的 DOM 节点     |                     |

**多平台的渲染抽象能力**

UI = f（state）这个公式进一步进行拆分可以拆分成两步：

- 根据自变量的变化计算出 UI
- 根据 UI 变化执行具体的宿主环境的 API

虚拟 DOM 只是多真实 UI 的一个描述，回头根据不同的宿主环境，可以执行不同的渲染代码：

- 浏览器、Node.js 宿主环境使用 ReactDOM 包
- Native 宿主环境使用 ReactNative 包
- Canvas、SVG 或者 VML（IE8）宿主环境使用 ReactArt 包
- ReactTest 包用于渲染出 JS 对象，可以很方便地测试“不隶属于任何宿主环境的通用功能”

## React 中的虚拟DOM

在 React 中通过 JSX 来描述 UI，JSX 最终会被转为一个叫做 createElement 方法的调用，调用该方法后就会得到虚拟 DOM 对象。

经过 Babel 编译后结果如下：

![image-20221226155735808](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-12-26-075736.png)

在源码中 createElement 方法如下：

```js
/**
 *
 * @param {*} type 元素类型 h1
 * @param {*} config 属性对象 {id : "aa"}
 * @param {*} children 子元素 hello
 * @returns
 * <h1 id="aa">hello</h1>
 */
export function createElement(type, config, children) {
  let propName;

  const props = {};

  let key = null;
  let ref = null;
  let self = null;
  let source = null;

  // 说明有属性
  if (config != null) {
    // ...
    for (propName in config) {
      if (
        hasOwnProperty.call(config, propName) &&
        !RESERVED_PROPS.hasOwnProperty(propName)
      ) {
        props[propName] = config[propName];
      }
    }
  }
  // 经历了上面的 if 之后，所有的属性都放到了 props 对象上面
  // props ==> {id : "aa"}

  // children 可以有多个参数，这些参数被转移到新分配的 props 对象上
  // 如果是多个子元素，对应的是一个数组
  const childrenLength = arguments.length - 2;
  if (childrenLength === 1) {
    props.children = children;
  } else if (childrenLength > 1) {
    const childArray = Array(childrenLength);
    for (let i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];
    }
    // ...
    props.children = childArray;
  }

  // 添加默认的 props
  if (type && type.defaultProps) {
    const defaultProps = type.defaultProps;
    for (propName in defaultProps) {
      if (props[propName] === undefined) {
        props[propName] = defaultProps[propName];
      }
    }
  }
  // ...
  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props
  );
}

const ReactElement = function (type, key, ref, self, source, owner, props) {
    // 该对象就是最终向外部返回的 vdom（也就是用来描述 DOM 层次结构的 JS 对象）
  const element = {
    // 让我们能够唯一地将其标识为 React 元素
    $$typeof: REACT_ELEMENT_TYPE,

    // 元素的内置属性
    type: type,
    key: key,
    ref: ref,
    props: props,

    // 记录负责创建此元素的组件。
    _owner: owner,
  };
  // ...
  return element;
};
```

在上面的代码中，最终返回的 element 对象就是我们所说的虚拟 DOM 对象。在官方文档中，官方更倾向于将这个对象称之为 React 元素。

## 真题解答

> 题目：什么是虚拟DOM？其优点有哪些？
>
> 参考答案：
>
> 虚拟 DOM 最初是由 React 团队所提出的概念，这是一种编程的思想，指的是针对真实 UI DOM 的一种描述能力。
>
> 在 React 中，使用了 JS 对象来描述真实的 DOM 结构。虚拟DOM和 JS 对象之间的关系：前者是一种思想，后者是这种思想的具体实现。
>
> 使用虚拟 DOM 有如下的优点：
>
> - 相较于 DOM 的体积和速度优势
> - 多平台渲染的抽象能力
>
> **相较于 DOM 的体积和速度优势**
>
> - JS 层面的计算的速度，要比 DOM 层面的计算快得多
>   - DOM 对象最终要被浏览器显示出来之前，浏览器会有很多工作要做（浏览器渲染原理）
>   - DOM 上面的属性也是非常多的
> - 虚拟 DOM 发挥优势的时机主要体现在更新的时候，相比较 innerHTML 要将已有的 DOM 节点全部销毁，虚拟 DOM 能够做到针对 DOM 节点做最小程度的修改
>
> **多平台渲染的抽象能力**
>
> - 浏览器、Node.js 宿主环境使用 ReactDOM 包
> - Native 宿主环境使用 ReactNative 包
> - Canvas、SVG 或者 VML（IE8）宿主环境使用 ReactArt 包
> - ReactTest 包用于渲染出 JS 对象，可以很方便地测试“不隶属于任何宿主环境的通用功能”
>
> 在 React 中，通过 JSX 来描述 UI，JSX 仅仅是一个语法糖，会被 Babel 编译为 createElement 方法的调用。该方法调用之后会返回一个 JS 对象，该对象就是虚拟 DOM 对象，官方更倾向于称之为一个 React 元素。

# 图解 diff 算法

> 面试题：React 中的 diff 算法有没有了解过？具体的流程是怎么样的？React 为什么不采用 Vue 的双端对比算法？

Render 阶段会生成 Fiber Tree，所谓的 diff 实际上就是发生在这个阶段，这里的 diff **指的是 current FiberNode 和 JSX 对象之间进行对比，然后生成新的的 wip FiberNode。**

> 除了 React 以外，其他使用到了虚拟 DOM 的前端框架也会有类似的流程，比如 Vue 里面将这个流程称之为 patch。

diff 算法本身是有性能上面的消耗，在 React 文档中有提到，即便采用最前沿的算法，如果要完整的对比两棵树，那么算法的复杂度都会达到 O(n^3)，n 代表的是元素的数量，如果 n 为 1000，要执行的计算量会达到十亿量级的级别。

因此，为了降低算法的复杂度，React 为 diff 算法设置了 3 个限制：

- 限制一：只对同级别元素进行 diff，如果一个 DOM 元素在前后两次更新中跨越了层级，那么 React 不会尝试复用它
- 限制二：两个不同类型的元素会产生不同的树。比如元素从 div 变成了 p，那么 React 会直接销毁 div 以及子孙元素，新建 p 以及 p 对应的子孙元素
- 限制三：开发者可以通过 key 来暗示哪些子元素能够保持稳定

更新前：

```jsx
<div>
  <p key="one">one</p>
  <h3 key="two">two</h3>
</div>
```

更新后

```jsx
<div>
  <h3 key="two">two</h3>
  <p key="one">one</p>
</div>
```

如果没有 key，那么 React 就会认为 div 的第一个子元素从 p 变成了 h3，第二个子元素从 h3 变成了 p，因此 React 就会采用限制二的规则。

但是如果使用了 key，那么此时的 DOM 元素是可以复用的，只不过前后交换了位置而已。



接下来我们回头再来看限制一，对同级元素进行 diff，究竟是如何进行 diff ？整个 diff 的流程可以分为两大类：

- 更新后只有一个元素，此时就会根据 newChild 创建对应的 wip FiberNode，对应的流程就是单节点 diff
- 更新后有多个元素，此时就会遍历 newChild 创建对应的 wip FiberNode 已经它的兄弟元素，此时对应的流程就是多节点 diff



## 单节点 diff

单节点指的是新节点为单一节点，但是旧节点的数量是不一定

单节点 diff 是否能够复用遵循以下的流程：

- 判断 key 是否相同
  - 如果更新前后没有设置 key，那么 key 就是 null，也是属于相同的情况
  - 如果 key 相同，那么就会进入到步骤二
  - 如果 key 不同，就不需要进入步骤，无需判断 type，结果直接为不能复用（如果有兄弟节点还会去遍历兄弟节点）
- 如果 key 相同，再判断 type 是否相同
  - 如果 type 相同，那么就复用
  - 如果 type 不同，无法复用（并且兄弟节点也一并标记为删除）

更新前

```jsx
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
</ul>
```

更新后

```jsx
<ul>
  <p>1</p>
</ul>
```

这里因为没有设置 key，所以会被设为 key 是相同的，接下来就会进入到 type 的判断，此时发现 type 不同，因此不能够复用。

既然这里唯一的可能性都已经不能够复用，会直接标记兄弟 FiberNode 为删除状态。

> 如果上面的例子中，key 不同只能代表当前的 FiberNode 无法复用，因此还需要去遍历兄弟的 FiberNode

下面我们再来看一些示例

更新前

```jsx
<div>one</div>
```

更新后

```jsx
<p>one</p>
```

没有设置 key，那么可以认为默认 key 就是 null，更新前后两个 key 是相同的，接下来就查看 type，发现 type 不同，因此不能复用。



更新前

```jsx
<div key="one">one</div>
```

更新后

```jsx
<div key="two">one</div>
```

更新前后 key 不同，不需要再判断 type，结果为不能复用



更新前

```jsx
<div key="one">one</div>
```

更新后

```jsx
<p key="two">one</p>
```

更新前后 key 不同，不需要再判断 type，结果为不能复用



更新前

```jsx
<div key="one">one</div>
```

更新后

```jsx
<div key="one">two</div>
```

首先判断 key 相同，接下来判断 type 发现也是相同，这个 FiberNode 就能够复用，children 是一个文本节点，之后将文本节点更新即可。



## 多节点 diff

所谓多节点 diff，指的是新节点有多个。

React 团队发现，在日常开发中，对节点的更新操作的情况往往要多余对节点“新增、删除、移动”，因此在进行多节点 diff 的时候，React 会进行两轮遍历：

- 第一轮遍历会尝试逐个的复用节点
- 第二轮遍历处理上一轮遍历中没有处理完的节点



### 第一轮遍历

第一轮遍历会从前往后依次进行遍历，存在三种情况：

- 如果新旧子节点的key 和 type 都相同，说明可以复用
- 如果新旧子节点的 key 相同，但是 type 不相同，这个时候就会根据 ReactElement 来生成一个全新的 fiber，旧的 fiber 被放入到 deletions 数组里面，回头统一删除。但是注意，此时遍历并不会终止
- 如果新旧子节点的 key 和 type 都不相同，结束遍历



**示例一**

更新前

```jsx
<div>
	<div key="a">a</div>
  <div key="b">b</div>
  <div key="c">c</div>
  <div key="d">d</div>
</div>
```

更新后

```jsx
<div>
	<div key="a">a</div>
  <div key="b">b</div>
  <div key="e">e</div>
  <div key="d">d</div>
</div>
```

首先遍历到 div.key.a，发现该 FiberNode 能够复用

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-032654.png" alt="image-20230228112653938" style="zoom:50%;" />

继续往后面走，发现 div.key.b 也能够复用

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-075146.png" alt="image-20230228155145634" style="zoom:50%;" />

接下来继续往后面走，div.key.e，这个时候发现 key 不一样，因此第一轮遍历就结束了

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-075345.png" alt="image-20230228155345381" style="zoom:50%;" />



**示例二**

更新前

```jsx
<div>
	<div key="a">a</div>
  <div key="b">b</div>
  <div key="c">c</div>
  <div key="d">d</div>
</div>
```

更新后

```jsx
<div>
	<div key="a">a</div>
  <div key="b">b</div>
  <p key="c">c</p>
  <div key="d">d</div>
</div>
```

首先和上面的一样，div.key.a 和 div.key.b 这两个 FiberNode 可以进行复用，接下来到了第三个节点，此时会发现 key 是相同的，但是 type 不相同，此时就会将对应的旧的 FiberNode 放入到一个叫 deletions 的数组里面，回头统一进行删除，根据新的 React 元素创建一个新的 FiberNode，但是此时的遍历是不会结束的

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-075011.png" alt="image-20230228155011306" style="zoom:50%;" />

接下来继续往后面进行遍历，遍历什么时候结束呢？

- 到末尾了，也就是说整个遍历完了
- 或者是和示例一相同，可以 key 不同

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-075725.png" alt="image-20230228155725123" style="zoom:50%;" />



### 第二轮遍历

如果第一轮遍历被提前终止了，那么意味着有新的 React 元素或者旧的 FiberNode 没有遍历完，此时就会采用第二轮遍历

第二轮遍历会处理这么三种情况：

- 只剩下旧子节点：将旧的子节点添加到 deletions 数组里面直接删除掉（删除的情况）

- 只剩下新的 JSX 元素：根据 ReactElement 元素来创建 FiberNode 节点（新增的情况）

- 新旧子节点都有剩余：会将剩余的 FiberNode 节点放入一个 map 里面，遍历剩余的新的 JSX 元素，然后从 map 中去寻找能够复用的 FiberNode 节点，如果能够找到，就拿来复用。（移动的情况）

  如果不能找到，就新增呗。然后如果剩余的 JSX 元素都遍历完了，map 结构中还有剩余的 Fiber 节点，就将这些 Fiber 节点添加到 deletions 数组里面，之后统一做删除操作



**只剩下旧子节点**

更新前

```jsx
<div>
  <div key="a">a</div>
  <div key="b">b</div>
  <div key="c">c</div>
  <div key="d">d</div>
</div>
```

更新后

```jsx
<div>
  <div key="a">a</div>
  <div key="b">b</div>
</div>
```

遍历前面两个节点，发现能够复用，此时就会复用前面的节点，对于 React 元素来讲，遍历完前面两个就已经遍历结束了，因此剩下的FiberNode就会被放入到 deletions 数组里面，之后统一进行删除

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-080358.png" alt="image-20230228160357510" style="zoom:50%;" />



**只剩下新的 JSX 元素**

更新前

```jsx
<div>
  <div key="a">a</div>
  <div key="b">b</div>
</div>
```

更新后

```jsx
<div>
  <div key="a">a</div>
  <div key="b">b</div>
  <div key="c">c</div>
  <div key="d">d</div>
</div>
```

根据新的 React 元素新增对应的 FiberNode 即可。

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-080558.png" alt="image-20230228160557549" style="zoom:50%;" />



**新旧子节点都有剩余**

更新前

```jsx
<div>
  <div key="a">a</div>
  <div key="b">b</div>
  <div key="c">c</div>
  <div key="d">d</div>
</div>
```

更新后

```jsx
<div>
  <div key="a">a</div>
  <div key="c">b</div>
  <div key="b">b</div>
  <div key="e">e</div>
</div>
```

首先会将剩余的旧的 FiberNode 放入到一个 map 里面

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-081414.png" alt="image-20230228161414334" style="zoom:50%;" />

接下来会继续去遍历剩下的 JSX 对象数组，遍历的同时，从 map 里面去找有没有能够复用

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-081859.png" alt="image-20230228161859070" style="zoom:50%;" />

如果在 map 里面没有找到，那就会新增这个 FiberNode，如果整个 JSX 对象数组遍历完成后，map 里面还有剩余的 FiberNode，说明这些 FiberNode 是无法进行复用，直接放入到 deletions 数组里面，后期统一进行删除。

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-082152.png" alt="image-20230228162152734" style="zoom:50%;" />



## 双端对比算法

所谓双端，指的是在新旧子节点的数组中，各用两个指针指向头尾的节点，在遍历的过程中，头尾两个指针同时向中间靠拢。

因此在新子节点数组中，会有两个指针，newStartIndex 和 newEndIndex 分别指向新子节点数组的头和尾。在旧子节点数组中，也会有两个指针，oldStartIndex 和 oldEndIndex 分别指向旧子节点数组的头和尾。

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-28-085007.png" alt="image-20230228165007200" style="zoom:50%;" />

每遍历到一个节点，就尝试进行双端比较：「新前 vs 旧前」、「新后 vs 旧后」、「新后 vs 旧前」、「新前 vs 旧后」，如果匹配成功，更新双端的指针。比如，新旧子节点通过「新前 vs 旧后」匹配成功，那么 newStartIndex += 1，oldEndIndex -= 1。

如果新旧子节点通过「新后 vs 旧前」匹配成功，还需要将「旧前」对应的 DOM 节点插入到「旧后」对应的 DOM 节点之前。如果新旧子节点通过「新前 vs 旧后」匹配成功，还需要将「旧后」对应的 DOM 节点插入到「旧前」对应的 DOM 节点之前。



实际上在 React 的源码中，解释了为什么不使用双端 diff

```js
function reconcileChildrenArray(
returnFiber: Fiber,
 currentFirstChild: Fiber | null,
 newChildren: Array<*>,
 expirationTime: ExpirationTime,
): Fiber | null {
    // This algorithm can't optimize by searching from boths ends since we
    // don't have backpointers on fibers. I'm trying to see how far we can get
    // with that model. If it ends up not being worth the tradeoffs, we can
    // add it later.

    // Even with a two ended optimization, we'd want to optimize for the case
    // where there are few changes and brute force the comparison instead of
    // going for the Map. It'd like to explore hitting that path first in
    // forward-only mode and only go for the Map once we notice that we need
    // lots of look ahead. This doesn't handle reversal as well as two ended
    // search but that's unusual. Besides, for the two ended optimization to
    // work on Iterables, we'd need to copy the whole set.

    // In this first iteration, we'll just live with hitting the bad case
    // (adding everything to a Map) in for every insert/move.

    // If you change this code, also update reconcileChildrenIterator() which
    // uses the same algorithm.
｝
```

将上面的注视翻译成中文如下：

>由于双端 diff 需要向前查找节点，但每个 FiberNode 节点上都没有反向指针，即前一个 FiberNode 通过 sibling 属性指向后一个 FiberNode，只能从前往后遍历，而不能反过来，因此该算法无法通过双端搜索来进行优化。
>
>React 想看下现在用这种方式能走多远，如果这种方式不理想，以后再考虑实现双端 diff。React 认为对于列表反转和需要进行双端搜索的场景是少见的，所以在这一版的实现中，先不对 bad case 做额外的优化。



## 真题解答

> 题目：React 中的 diff 算法有没有了解过？具体的流程是怎么样的？React 为什么不采用 Vue 的双端对比算法？
>
> 参考答案：
>
> diff 计算发生在更新阶段，当第一次渲染完成后，就会产生 Fiber 树，再次渲染的时候（更新），就会拿新的 JSX 对象（vdom）和旧的 FiberNode 节点进行一个对比，再决定如何来产生新的 FiberNode，它的目标是尽可能的复用已有的 Fiber 节点。这个就是 diff 算法。
>
> 在 React 中整个 diff 分为单节点 diff 和多节点 diff。
>
> 所谓单节点是指新的节点为单一节点，但是旧节点的数量是不一定的。
>
> 单节点 diff 是否能够复用遵循如下的顺序：
>
> 1. 判断 key 是否相同
>
>    - 如果更新前后均未设置 key，则 key 均为 null，也属于相同的情况
>
>    - 如果 key 相同，进入步骤二
>    - 如果 key 不同，则无需判断 type，结果为不能复用（有兄弟节点还会去遍历兄弟节点）
>
> 2. 如果 key 相同，再判断 type 是否相同
>
>    - 如果 type 相同，那么就复用
>    - 如果 type 不同，则无法复用（并且兄弟节点也一并标记为删除）
>
> 多节点 diff 会分为两轮遍历：
>
> 第一轮遍历会从前往后进行遍历，存在以下三种情况：
>
> - 如果新旧子节点的key 和 type 都相同，说明可以复用
> - 如果新旧子节点的 key 相同，但是 type 不相同，这个时候就会根据 ReactElement 来生成一个全新的 fiber，旧的 fiber 被放入到 deletions 数组里面，回头统一删除。但是注意，此时遍历并不会终止
> - 如果新旧子节点的 key 和 type 都不相同，结束遍历
>
> 如果第一轮遍历被提前终止了，那么意味着还有新的 JSX 元素或者旧的 FiberNode 没有被遍历，因此会采用第二轮遍历去处理。
>
> 第二轮遍历会遇到三种情况：
>
> - 只剩下旧子节点：将旧的子节点添加到 deletions 数组里面直接删除掉（删除的情况）
>
> - 只剩下新的 JSX 元素：根据 ReactElement 元素来创建 FiberNode 节点（新增的情况）
>
> - 新旧子节点都有剩余：会将剩余的 FiberNode 节点放入一个 map 里面，遍历剩余的新的 JSX 元素，然后从 map 中去寻找能够复用的 FiberNode 节点，如果能够找到，就拿来复用。（移动的情况）
>
>   如果不能找到，就新增呗。然后如果剩余的 JSX 元素都遍历完了，map 结构中还有剩余的 Fiber 节点，就将这些 Fiber 节点添加到 deletions 数组里面，之后统一做删除操作
>
> 整个 diff 算法最最核心的就是两个字“复用”。
>
> React 不使用双端 diff 的原因：
>
> 由于双端 diff 需要向前查找节点，但每个 FiberNode 节点上都没有反向指针，即前一个 FiberNode 通过 sibling 属性指向后一个 FiberNode，只能从前往后遍历，而不能反过来，因此该算法无法通过双端搜索来进行优化。
>
> React 想看下现在用这种方式能走多远，如果这种方式不理想，以后再考虑实现双端 diff。React 认为对于列表反转和需要进行双端搜索的场景是少见的，所以在这一版的实现中，先不对 bad case 做额外的优化。

#  React 整体架构

> 面试题：是否了解过 React 的架构？新的 Fiber 架构相较于之前的 Stack 架构有什么优势？

**标准且浅显的回答**

> Stack 架构在进行虚拟 DOM 树比较的时候，采用的是递归，计算会消耗大量的时间，新的 Fiber 架构采用的是链表，可以实现时间切片，防止 JS 的计算占用过多的时间从而导致浏览器出现丢帧的现象。

React v15以及之前的架构称之为 Stack 架构，从 v16 开始，React 重构了整体的架构，新的架构被称之为 Fiber 架构，新的架构相比旧架构有一个最大的特点就是能够实现时间切片。

- 旧架构的问题？
- 新架构的解决思路

## 旧架构的问题

> React 是用 JavaScript 构建快速响应的大型 Web 应用程序的首选方式

有哪些情况会导致我们的 Web 应用无法快速响应？

总结起来，实际上有两大类场景会限制快速响应：

- 当你需要执行大量计算或者设备本身的性能不足的时候，页面就会出现掉帧、卡顿的现象，这个本质上是来自于 CPU 的瓶颈
- 进行 I/O 的时候，需要等待数据返回后再进行后续操作，等待的过程中无法快速响应，这种情况实际上是来自于 I/O 的瓶颈

### CPU 瓶颈

平时我们在浏览网页的时候，这张网页实际上是由浏览器绘制出来的，就像一个画家画画一样

![draw](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-23-063619.jpg)

平时我们所浏览的网页，里面往往会有一些动起来的东西，比如轮播图、百叶窗之类的，本质其实就是浏览器不停的在进行绘制。

目前，大多数设备的刷新频率为 60 FPS，意味着 1秒钟需要绘制 60 次，1000ms / 60 = 16.66ms，也就是说浏览器每隔 16.66ms 就需要绘制一帧。

浏览器在绘制一帧画面的时候，实际上还有很多的事情要做：

![image-20221227140043781](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-12-27-060044.png)

上图中的任务被称之为“渲染流水线”，每次执行流水线的时候，大致是需要如上的一些步骤，但是并不是说每一次所有的任务都需要全部执行：

- 当通过 JS 或者 CSS 修改 DOM 元素的几何属性（比如长度、宽度）时，会**触发完整的渲染流水线**，这种情况称之为重排（回流）
- 当修改的属性不涉及几何属性（比如字体、颜色）时，会省略掉流水线中的 Layout、Layer 过程，这种情况称之为重绘
- 当修改“不涉及重排、重绘的属性（比如 transform 属性）”时，会省略流水线中 Layout、Layer、Print 过程，仅执行合成线程的绘制工作，这种情况称之为合成

按照性能高低进行排序的话：合成 > 重绘 > 重排

前面说过，浏览器绘制的频率是 16.66ms 一帧，但是执行 JS 与渲染流水线实际上是在同一个线程上面执行，也就意味着如果 JS 执行的时间过长，不能够及时的渲染下一帧，也就意味着页面掉帧，表现出来的现象就是页面卡顿。

在 Reactv16 之前就存在这个问题，JS 代码执行的时间过长。在 React 中，需要去计算整颗虚拟 DOM 树，虽然说是 JS 层面的计算，相比直接操作 DOM，节省了很多时间，但是每次重新去计算整颗虚拟 DOM 树，会造成每一帧的 JS 代码的执行时间过长，从而导致动画、还有一些实时更新得不到及时的响应，造成卡顿的视觉效果。

假设有如下的 DOM 层次结构：

![image-20230223152638127](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-23-072638.png)

那么转换成虚拟 DOM 对象结构大致如下：

```
{
  type : "div",
  props : {
    id : "test",
    children : [
      {
        type : "h1",
        props : {
          children : "This is a title"
        }
      }
      {
        type : "p",
        props : {
          children : "This is a paragraph"
        }
      },{
        type : "ul",
        props : {
          children : [{
            type : "li",
            props : {
              children : "apple"
            }
          },{
            type : "li",
            props : {
              children : "banana"
            }
          },{
            type : "li",
            props : {
              children : "pear"
            }
          }]
        }
      }
    ]
  }
}
```

在 React v16 版本之前，进行两颗虚拟 DOM 树的对比的时候，需要涉及到遍历上面的结构，这个时候只能使用递归，而且这种递归是不能够打断的，一条路走到黑，从而造成了 JS 执行时间过长。

![image-20221227150133112](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-12-27-070133.png)

这样的架构模式，官方就称之为 Stack 架构模式，因为采用的是递归，会不停的开启新的函数栈。

### I/O瓶颈

对于前端开发来讲，最主要的 I/O 瓶颈就是网络延迟。

网络延迟是一种客观存在的现象，那么如何减少这种现象对用户的影响呢？React 团队给出的答案是：将人机交互的研究成果整合到 UI 中。

用户对卡顿的感知是不一样的，输入框哪怕只有轻微的延迟，用户也会认为很卡，假设是加载一个列表，哪怕 loading 好几秒，用户也不会觉得卡顿。

对于 React 来讲，所有的操作都是来自于自变量的变化导致的重新渲染，我们只需要针对不同的操作赋予不同的优先级即可。

具体来说，主要包含以下三个点：

- 为不同操作造成的“自变量变化”赋予不同的优先级
- 所有优先级统一调度，优先处理“最高优先级的更新”
- 如果更新正在进行（进入虚拟 DOM 相关工作），此时有“更高优先级的更新”产生的话，中段当前的更新，优先处理高优先级更新

要实现上面的这三个点，就需要 React 底层能实现：

- 用于调度优先级的调度器
- 调度器对应的调度算法
- 支持可中断的虚拟 DOM 的实现

所以不管是解决 CPU 的瓶颈还是 I/O 的瓶颈，底层的诉求都是需要实现 time slice

## 新架构的解决思路

### 解决 CPU 瓶颈

从 React v16 开始，官方团队正式引用了 Fiber 的概念，这是一种通过链表来描述 UI 的方式，本质上你也可以看作是一种虚拟 DOM 的实现。

> 与其将 “Virtual DOM” 视为一种技术，不如说它是一种模式，人们提到它时经常是要表达不同的东西。在 React 的世界里，术语 “Virtual DOM” 通常与 [React 元素 ](https://gitee.com/link?target=https%3A%2F%2Freact.docschina.org%2Fdocs%2Frendering-elements.html)关联在一起，因为它们都是代表了用户界面的对象。而 React 也使用一个名为 “fibers” 的内部对象来存放组件树的附加信息。上述二者也被认为是 React 中 “Virtual DOM” 实现的一部分。

Fiber 本质上也是一个对象，但是和之前 React 元素不同的地方在于对象之间使用链表的结构串联起来，child 指向子元素，sibling 指向兄弟元素，return 指向父元素。

如下图：

![image-20230224112508425](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-032509.png)

使用链表这种结构，有一个最大的好处就是在进行整颗树的对比（reconcile）计算时，这个过程是可以被打断。

在发现一帧时间已经不够，不能够再继续执行 JS，需要渲染下一帧的时候，这个时候就会打断 JS 的执行，优先渲染下一帧。渲染完成后再接着回来完成上一次没有执行完的 JS 计算。

![image-20221227150225918](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-12-27-070226.png)

官方还提供了一个 Stack 架构和 Fiber 架构的对比示例：[https://claudiopro.github.io/react-fiber-vs-stack-demo/](https://gitee.com/link?target=https%3A%2F%2Fclaudiopro.github.io%2Freact-fiber-vs-stack-demo%2F)

下面是 React 源码中创建 Fiber 对象的相关代码：

```
const createFiber = function (tag, pendingProps, key, mode) {
  // 创建 fiber 节点的实例对象
  return new FiberNode(tag, pendingProps, key, mode);
};

function FiberNode(tag, pendingProps, key, mode) {
  // Instance
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null; // 映射真实 DOM

  // Fiber
  // 上下、前后 fiber 通过链表的形式进行关联
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;

  this.ref = null;
  this.refCleanup = null;
  // 和 hook 相关
  this.pendingProps = pendingProps;
  this.memoizedProps = null;
  this.updateQueue = null;
  this.memoizedState = null;
  this.dependencies = null;

  this.mode = mode;

  // Effects
  this.flags = NoFlags;
  this.subtreeFlags = NoFlags;
  this.deletions = null;

  this.lanes = NoLanes;
  this.childLanes = NoLanes;

  this.alternate = null;
  // ...
}
```

### 解决 I/O 瓶颈

从 React v16 开始引入了 Scheduler（调度器），用来调度任务的优先级。

UI = f（state）：

- 根据自变量的变化计算出 UI
- 根据 UI 变化执行具体的宿主环境的 API

React v16之前：

- Reconciler（协调器）：vdom 的实现，根据自变量的变化计算出 UI 的变化
- Renderer（渲染器）：负责将 UI 的变化渲染到宿主环境

从 React v16 开始，多了一个组件：

- Scheduler（调度器）：调度任务的优先级，高优先级的任务会优先进入到 Reconciler
- Reconciler（协调器）：vdom 的实现，根据自变量的变化计算出 UI 的变化
- Renderer（渲染器）：负责将 UI 的变化渲染到宿主环境

新架构中，Reconciler 的更新流程也从之前的递归变成了“可中断的循环过程”。

```
function workLoopConcurrent{
  // 如果还有任务，并且时间切片还有剩余的时间
  while(workInProgress !== null && !shouldYield()){
    performUnitOfWork(workInProgress);
  }
}

function shouldYield(){
  // 当前时间是否大于过期时间
  // 其中 deadline = getCurrentTime() + yieldInterval
  // yieldInterval 为调度器预设的时间间隔，默认为 5ms
  return getCurrentTime() >= deadline;
}
```

每次循环都会调用 shouldYield 判断当前的时间切片是否有足够的剩余时间，如果没有足够的剩余时间，就暂停 reconciler 的执行，将主线程还给渲染流水线，进行下一帧的渲染操作，渲染工作完成后，再等待下一个宏任务进行后续代码的执行。

## 真题解答

> 题目：是否了解过 React 的架构？新的 Fiber 架构相较于之前的 Stack 架构有什么优势？
>
> 参考答案：
>
> React v15及其之前的架构：
>
> - Reconciler（协调器）：VDOM 的实现，负责根据自变量变化计算出 UI 变化
> - Renderer（渲染器）：负责将 UI 变化渲染到宿主环境中
>
> 这种架构称之为 Stack 架构，在 Reconciler 中，mount 的组件会调用 mountComponent，update 的组件会调用 updateComponent，这两个方法都会递归更新子组件，更新流程一旦开始，中途无法中断。
>
> 但是随着应用规模的逐渐增大，之前的架构模式无法再满足“快速响应”这一需求，主要受限于如下两个方面：
>
> - CPU 瓶颈：由于 VDOM 在进行差异比较时，采用的是递归的方式，JS 计算会消耗大量的时间，从而导致动画、还有一些需要实时更新的内容产生视觉上的卡顿。
> - I/O 瓶颈：由于各种基于“自变量”变化而产生的更新任务没有优先级的概念，因此在某些更新任务（例如文本框的输入）有稍微的延迟，对于用户来讲也是非常敏感的，会让用户产生卡顿的感觉。
>
> 新的架构称之为 Fiber 架构：
>
> - Scheduler（调度器）：调度任务的优先级，高优先级任务会优先进入到 Reconciler
> - Reconciler（协调器）：VDOM 的实现，负责根据自变量变化计算出 UI 变化
> - Renderer（渲染器）：负责将 UI 变化渲染到宿主环境中
>
> 首先引入了 Fiber 的概念，通过一个对象来描述一个 DOM 节点，但是和之前方案不同的地方在于，每个 Fiber 对象之间通过链表的方式来进行串联。通过 child 来指向子元素，通过 sibling 指向兄弟元素，通过 return 来指向父元素。
>
> 在新架构中，Reconciler 中的更新流程从递归变为了“可中断的循环过程”。每次循环都会调用 shouldYield 判断当前的 TimeSlice 是否有剩余时间，没有剩余时间则暂停更新流程，将主线程还给渲染流水线，等待下一个宏任务再继续执行。这样就解决了 CPU 的瓶颈问题。
>
> 另外在新架构中还引入了 Scheduler 调度器，用来调度任务的优先级，从而解决了 I/O 的瓶颈问题。

#  React 渲染流程

> 面试题：是否了解过 React 的整体渲染流程？里面主要有哪些阶段？

现代前端框架都可以总结为一个公式：

> UI = f（state）

上面的公式还可以进行一个拆分：

- 根据自变量（state）的变化计算出 UI 的变化
- 根据 UI 变化执行具体的宿主环境的 API

对应的公式：

```
const state = reconcile(update); // 通过 reconciler 计算出最新的状态
const UI = commit(state); // 根据上一步计算出来的 state 渲染出 UI
```

对应到 React 里面就两大阶段：

- render 阶段：调合虚拟 DOM，计算出最终要渲染出来的虚拟 DOM
- commit 阶段：根据上一步计算出来的虚拟 DOM，渲染具体的 UI

每个阶段对应不同的组件：

![image-20230223181848783](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-23-101849.png)

- 调度器（Scheduer）：调度任务，为任务排序优先级，让优先级高的任务先进入到 Reconciler
- 协调器（Reconciler）：生成 Fiber 对象，收集副作用，找出哪些节点发生了变化，打上不同的 flags，著名的 diff 算法也是在这个组件中执行的。
- 渲染器（Renderer）：根据协调器计算出来的虚拟 DOM 同步的渲染节点到视图上。

接下来我们来看一个例子：

```
export default () => {
  const [count, updateCount] = useState(0);
  return (
    <ul>
    	<button onClick={() => updateCount(count + 1)}>乘以{count}</button>
      <li>{1 * count}</li>
      <li>{2 * count}</li>
      <li>{3 * count}</li>
    </ul>
  );
}
```

当用户点击按钮时，首先是由 Scheduler 进行任务的协调，render 阶段（虚线框内）的工作流程是可以随时被以下原因中断：

- 有其他更高优先级的任务需要执行
- 当前的 time slice 没有剩余的时间
- 发生了其他错误

注意上面 render 阶段的工作是在内存里面进行的，不会更新宿主环境 UI，因此这个阶段即使工作流程反复被中断，用户也不会看到“更新不完整的UI”。

当 Scheduler 调度完成后，将任务交给 Reconciler，Reconciler 就需要计算出新的 UI，最后就由 Renderer **同步**进行渲染更新操作。

如下图所示：

![image-20230223183449668](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-23-103449.png)

## 调度器

在 React v16 版本之前，采用的是 Stack 架构，所有任务只能同步进行，无法被打断，这就导致浏览器可能会出现丢帧的现象，表现出卡顿。React 为了解决这个问题，从 v16 版本开始从架构上面进行了两大更新：

- 引入 Fiber
- 新增了 Scheduler

Scheduler 在浏览器的原生 API 中实际上是有类似的实现的，这个 API 就是 requestIdleCallback

> MDN：[https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback](https://gitee.com/link?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow%2FrequestIdleCallback)

虽然每一帧绘制的时间约为 16.66ms，但是如果屏幕没有刷新，那么浏览器会安排长度为 50ms 左右的空闲时间。

为什么是50ms？

根据研究报告表明，用户操作之后，100ms以内的响应给用户的感觉都是瞬间发生，也就是说不会感受到延迟感，因此将空闲时间设置为 50，浏览器依然还剩下 50ms 可以处理用户的操作响应，不会让用户感到延迟。

虽然浏览器有类似的 API，但是 React 团队并没有使用该 API，因为该 API 存在兼容性问题。因此 React 团队自己实现了一套这样的机制，这个就是调度器 Scheduler。

后期 React 团队打算单独发行这个 Scheduler，这意味着调度器不仅仅只能在 React 中使用，凡是有涉及到任务调度需求的项目都可以使用 Scheduler。

## 协调器

协调器是 render 阶段的第二阶段工作，类组件或者函数组件本身就是在这个阶段被调用的。

根据 Scheduler 调度结果的不同，协调器起点可能是不同的

- performSyncWorkOnRoot（同步更新流程）
- performConcurrentWorkOnRoot（并发更新流程）

```
// performSyncWorkOnRoot 会执行该方法
function workLoopSync(){
  while(workInProgress !== null){
    performUnitOfWork(workInProgress)
  }
}
// performConcurrentWorkOnRoot 会执行该方法
function workLoopConcurrent(){
  while(workInProgress !== null && !shouldYield()){
    performUnitOfWork(workInProgress)
  }
}
```

新的架构使用 Fiber（对象）来描述 DOM 结构，最终需要形成一颗 Fiber tree，这不过这棵树是通过链表的形式串联在一起的。

workInProgress 代表的是当前的 FiberNode。

performUnitOfWork 方法会创建下一个 FiberNode，并且还会将已创建的 FiberNode 连接起来（child、return、sibling），从而形成一个链表结构的 Fiber tree。

如果 workInProgress 为 null，说明已经没有下一个 FiberNode，也就是说明整颗 Fiber tree 树已经构建完毕。

上面两个方法唯一的区别就是是否调用了 shouldYield方法，该方法表明了是否可以中断。

performUnitOfWork在创建下一个 FiberNode 的时候，整体上的工作流程可以分为两大块：

- 递阶段
- 归阶段

**递阶段**

递阶段会从 HostRootFiber 开始向下以深度优先的原则进行遍历，遍历到的每一个 FiberNode 执行 beginWork 方法。该方法会根据传入的 FiberNode 创建下一级的 FiberNode，此时可能存在两种情况：

- 下一级只有一个元素，beginWork 方法会创建对应的 FiberNode，并于 workInProgress 连接

```
<ul>
  <li></li>
</ul>
```

这里就会创建 li 对应的 FiberNode，做出如下的连接：

```
LiFiber.return = UlFiber;
```

- 下一级有多个元素，这是 beginWork 方法会依次创建所有的子 FiberNode 并且通过 sibling 连接到一起，每个子 FiberNode 也会和 workInProgress 连接

```
<ul>
  <li></li>
  <li></li>
  <li></li>
</ul>
```

此时会创建 3 个 li 对应的 FiberNode，连接情况如下：

```
// 所有的子 Fiber 依次连接
Li0Fiber.sibling = Li1Fiber;
Li1Fiber.sibling = Li2Fiber;

// 子 Fiber 还需要和父 Fiber 连接
Li0Fiber.return = UlFiber;
Li1Fiber.return = UlFiber;
Li2Fiber.return = UlFiber;
```

由于采用的是深度优先的原则，因此无法再往下走的时候，会进入到归阶段。

**归阶段**

归阶段会调用 completeWork 方法来处理 FiberNode，做一些副作用的收集。

当某个 FiberNode 执行完了 completeWork 方法后，如果存在兄弟元素，就会进入到兄弟元素的递阶段，如果不存在兄弟元素，就会进入父 FiberNode 的归阶段。

```
function performUnitOfWork(fiberNode){
  // 省略 beginWork 
  if(fiberNode.child){
    performUnitOfWork(fiberNode.child);
  }
  // 省略 CompleteWork 
  if(fiberNode.sibling){
    performUnitOfWork(fiberNode.sibling);
  }
}
```

最后我们来看一张图：

![image-20230224111517826](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-031518.png)

## 渲染器

Renderer 工作的阶段被称之为 commit 阶段。该阶段会将各种副作用 commit 到宿主环境的 UI 中。

相较于之前的 render 阶段可以被打断，commit 阶段一旦开始就会**同步**执行直到完成渲染工作。

整个渲染器渲染过程中可以分为三个子阶段：

- BeforeMutation 阶段
- Mutation 阶段
- Layout 阶段

![image-20230302170353345](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-02-090354.png)

## 真题解答

> 题目：是否了解过 React 的整体渲染流程？里面主要有哪些阶段？
>
> 参考答案：
>
> React 整体的渲染流程可以分为两大阶段，分别是 render 阶段和 commit 阶段。
>
> render 阶段里面会经由调度器和协调器处理，此过程是在内存中运行，是异步可中断的。
>
> commit 阶段会由渲染器进行处理，根据副作用进行 UI 的更新，此过程是同步不可中断的，否则会造成 UI 和数据显示不一致。
>
> **调度器**
>
> 调度器的主要工作就是调度任务，让所有的任务有优先级的概念，这样的话紧急的任务可以优先执行。Scheduler 实际上在浏览器的 API 中是有原生实现的，这个 API 叫做 requestIdleCallback，但是由于兼容性问题，React 放弃了使用这个 API，而是自己实现了一套这样的机制，并且后期会把 Scheduler 这个包单独的进行发布，变成一个独立的包。这就意味 Scheduler 不仅仅是只能在 React 中使用，后面如果有其他的项目涉及到了任务调度的需求，都可以使用这个 Scheduler。
>
> **协调器**
>
> 协调器是 Render 的第二阶段工作。该阶段会采用深度优先的原则遍历并且创建一个一个的 FiberNode，并将其串联在一起，在遍历时分为了“递”与“归”两个阶段，其中在“递”阶段会执行 beginWork 方法，该方法会根据传入的 FiberNode 创建下一级 FiberNode。而“归”阶段则会执行 CompleteWork 方法，做一些副作用的收集
>
> **渲染器**
>
> 渲染器的工作主要就是将各种副作用（flags 表示）commit 到宿主环境的 UI 中。整个阶段可以分为三个子阶段，分别是 BeforeMutation 阶段、Mutation 阶段和 Layout 阶段。

#  Fiber双缓冲

> 面试题：谈一谈你对 React 中 Fiber 的理解以及什么是 Fiber 双缓冲？

## 对 Fiber 的理解

实际上，我们可以从三个维度来理解 Fiber：

- 是一种架构，称之为 Fiber 架构
- 是一种数据类型
- 动态的工作单元

**是一种架构，称之为 Fiber 架构**

在 React v16之前，使用的是 Stack Reconciler，因此那个时候的 React 架构被称之为 Stack 架构。从 React v16 开始，重构了整个架构，引入了 Fiber，因此新的架构也被称之为 Fiber 架构，Stack Reconciler 也变成了 Fiber Reconciler。各个FiberNode之间通过链表的形式串联起来：

```
function FiberNode(tag, pendingProps, key, mode) {
  // ...

  // 周围的 Fiber Node 通过链表的形式进行关联
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;

  // ...
}
```

**是一种数据类型**

Fiber 本质上也是一个对象，是在之前 React 元素基础上的一种升级版本。每个 FiberNode 对象里面会包含 React 元素的类型、周围链接的FiberNode以及 DOM 相关信息：

```
function FiberNode(tag, pendingProps, key, mode) {
  // 类型
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null; // 映射真实 DOM

  // ...
}
```

**动态的工作单元**

在每个 FiberNode 中，保存了本次更新中该 React 元素变化的数据，还有就是要执行的工作（增、删、更新）以及副作用的信息：

```
function FiberNode(tag, pendingProps, key, mode) {
  // ...

  // 副作用相关
  this.flags = NoFlags;
  this.subtreeFlags = NoFlags;
  this.deletions = null;
	// 与调度优先级有关  
  this.lanes = NoLanes;
  this.childLanes = NoLanes;

  // ...
}
```

> 为什么指向父 FiberNode 的字段叫做 return 而非 parent？
>
> 因为作为一个动态的工作单元，return 指代的是 FiberNode 执行完 completeWork 后返回的下一个 FiberNode，这里会有一个返回的动作，因此通过 return 来指代父 FiberNode

## Fiber 双缓冲

Fiber 架构中的双缓冲工作原理类似于显卡的工作原理。

显卡分为前缓冲区和后缓冲区。首先，前缓冲区会显示图像，之后，合成的新的图像会被写入到后缓冲区，一旦后缓冲区写入图像完毕，就会前后缓冲区进行一个互换，这种将数据保存在缓冲区再进行互换的技术，就被称之为双缓冲技术。

Fiber 架构同样用到了这个技术，在 Fiber 架构中，同时存在两颗 Fiber Tree，一颗是真实 UI 对应的 Fiber Tree，可以类比为显卡的前缓冲区，另外一颗是在内存中构建的 FiberTree，可以类比为显卡的后缓冲区。

在 React 源码中，很多方法都需要接收两颗 FiberTree：

```
function cloneChildFibers(current, workInProgress){
  // ...
}
```

current 指的就是前缓冲区的 FiberNode，workInProgress 指的就是后缓冲区的 FiberNode。

两个 FiberNode 会通过 alternate 属性相互指向：

```
current.alternate = workInProgress;
workInProgress.alternate = current;
```

接下来我们从首次渲染（mount）和更新（update）这两个阶段来看一下 FiberTree 的形成以及双缓存机制：

**mount 阶段**

首先最顶层有一个 FiberNode，称之为 FiberRootNode，该 FiberNode 会有一些自己的任务：

- Current Fiber Tree 与 Wip Fiber Tree 之间的切换
- 应用中的过期时间
- 应用的任务调度信息

现在假设有这么一个结构：

```
<body>
  <div id="root"></div>
</body>
function App(){
  const [num, add] = useState(0);
  return (
  	<p onClick={() => add(num + 1)}>{num}</p>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.createRoot(rootElement).render(<App />);
```

当执行 ReactDOM.createRoot 的时候，会创建如下的结构：

![image-20230224151515483](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-071516.png)

此时会有一个 HostRootFiber，FiberRootNode 通过 current 来指向 HostRootFiber。

接下来进入到 mount 流程，该流程会基于每个 React 元素以深度优先的原则依次生成 wip FiberNode，并且每一个 wipFiberNode 会连接起来，如下图所示：

![image-20230224152421236](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-072421.png)

生成的 wip FiberTree 里面的每一个 FiberNode 会和 current FiberTree 里面的 FiberNode进行关联，关联的方式就是通过 alternate。但是目前 currentFiberTree里面只有一个 HostRootFiber，因此就只有这个 HostRootFiber 进行了 alternate 的关联。

当 wip FiberTree生成完毕后，也就意味着 render 阶段完毕了，此时 FiberRootNode就会被传递给 Renderer（渲染器），接下来就是进行渲染工作。渲染工作完毕后，浏览器中就显示了对应的 UI，此时 FiberRootNode.current 就会指向这颗 wip Fiber Tree，曾经的 wip Fiber Tree 它就会变成 current FiberTree，完成了双缓存的工作：

![image-20230224152953358](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-072953.png)

**update 阶段**

点击 p 元素，会触发更新，这一操作就会开启 update 流程，此时就会生成一颗新的 wip Fiber Tree，流程和之前是一样的

![image-20230224153250126](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-073250.png)

新的 wip Fiber Tree 里面的每一个 FiberNode 和 current Fiber Tree 的每一个 FiberNode 通过 alternate 属性进行关联。

当 wip Fiber Tree 生成完毕后，就会经历和之前一样的流程，FiberRootNode 会被传递给 Renderer 进行渲染，此时宿主环境所渲染出来的真实 UI 对应的就是左边 wip Fiber Tree 所对应的 DOM 结构，FiberRootNode.current 就会指向左边这棵树，右边的树就再次成为了新的 wip Fiber Tree

![image-20230224153638862](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-02-24-073639.png)

这个就是 Fiber双缓存的工作原理。

另外值得一提的是，开发者是可以在一个页面创建多个应用的，比如：

```
ReactDOM.createRoot(rootElement1).render(<App1 />);
ReactDOM.createRoot(rootElement2).render(<App2 />);                                                                                 ReactDOM.createRoot(rootElement3).render(<App3 />);
```

在上面的代码中，我们创建了 3 个应用，此时就会存在 3 个 FiberRootNode，以及对应最多 6 棵 Fiber Tree 树。

## 真题解析

> 题目：谈一谈你对 React 中 Fiber 的理解以及什么是 Fiber 双缓冲？
>
> 参考答案：
>
> Fiber 可以从三个方面去理解：
>
> - **FiberNode 作为一种架构**：在 React v15 以及之前的版本中，Reconceiler 采用的是递归的方式，因此被称之为 Stack Reconciler，到了 React v16 版本之后，引入了 Fiber，Reconceiler 也从 Stack Reconciler 变为了 Fiber Reconceiler，各个 FiberNode 之间通过链表的形式串联了起来。
> - **FiberNode 作为一种数据类型**：Fiber 本质上也是一个对象，是之前虚拟 DOM 对象（React 元素，createElement 的返回值）的一种升级版本，每个 Fiber 对象里面会包含 React 元素的类型，周围链接的 FiberNode，DOM 相关信息。
> - **FiberNode 作为动态的工作单元**：在每个 FiberNode 中，保存了“本次更新中该 React 元素变化的数据、要执行的工作（增、删、改、更新Ref、副作用等）”等信息。
>
> 所谓 Fiber 双缓冲树，指的是在内存中构建两颗树，并直接在内存中进行替换的技术。在 React 中使用 Wip Fiber Tree 和 Current Fiber Tree 这两颗树来实现更新的逻辑。Wip Fiber Tree 在内存中完成更新，而 Current Fiber Tree 是最终要渲染的树，两颗树通过 alternate 指针相互指向，这样在下一次渲染的时候，直接复用 Wip Fiber Tree 作为下一次的渲染树，而上一次的渲染树又作为新的 Wip Fiber Tree，这样可以加快 DOM 节点的替换与更新

#  MessageChannel 以及为什么选择它

MessageChannel 接口本身是用来做消息通信的，允许我们创建一个消息通道，通过它的两个 MessagePort 来进行信息的发送和接收。

基本使用如下：

```
<div>
  <input type="text" id="content" placeholder="请输入消息">
</div>
<div>
  <button id="btn1">给 port1 发消息</button>
  <button id="btn2">给 port2 发消息</button>
</div>
const channel = new MessageChannel();
// 两个信息端口，这两个信息端口可以进行信息的通信
const port1 = channel.port1;
const port2 = channel.port2;
btn1.onclick = function(){
  // 给 port1 发消息
  // 那么这个信息就应该由 port2 来进行发送
  port2.postMessage(content.value);
}
// port1 需要监听发送给自己的消息
port1.onmessage = function(event){
  console.log(`port1 收到了来自 port2 的消息：${event.data}`);
}

btn2.onclick = function(){
  // 给 port2 发消息
  // 那么这个信息就应该由 port1 来进行发送
  port1.postMessage(content.value);
}
// port2 需要监听发送给自己的消息
port2.onmessage = function(event){
  console.log(`port2 收到了来自 port1 的消息：${event.data}`);
}
```

那么这个和 scheduler 有什么关系呢？

之前，我们有说过 scheduler 是用来调度任务，调度任务需要满足两个条件：

- JS 暂停，将主线程还给浏览器，让浏览器能够有序的重新渲染页面
- 暂停了的 JS（说明还没有执行完），需要再下一次接着来执行

那么这里自然而然就会想到事件循环，我们可以将没有执行完的 JS 放入到任务队列，下一次事件循环的时候再取出来执行。

那么，如何将没有执行完的任务放入到任务队列中呢？

那么这里就需要产生一个任务（宏任务），这里就可以使用 MessageChannel，因为 MessageChannel 能够产生宏任务。

**为什么不选择 setTimeout**

以前要创建一个宏任务，可以采用 setTimeout(fn, 0) 这种方式，但是 *react* 团队没有采用这种方式。

这是因为 setTimeout 在嵌套层级超过 5 层，timeout（延时）如果小于 4ms，那么则会设置为 4ms。

这个你可以参阅 *HTML* 规范：*[https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#dom-settimeout](https://gitee.com/link?target=https%3A%2F%2Fhtml.spec.whatwg.org%2Fmultipage%2Ftimers-and-user-prompts.html%23dom-settimeout)*

> If nesting level is greater than 5, and timeout is less than 4, then set timeout to 4.

可以写一个例子来进行验证：

```
let count = 0; // 计数器
let startTime = new Date(); // 获取当前的时间戳
console.log("start time:", 0, 0);
function fn(){
  setTimeout(function(){
    console.log("exec time:", ++count, new Date() - startTime);
    if(count === 50){
      return;
    }
    fn();
  },0)
}
fn();
```

执行结果部分截图如下：

![image-20221229111030112](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-12-29-031030.png)

正因为这个原因，所以 react 团队没有选择使用 setTimeout 来产生任务，因为 4ms 的时间的浪费还是不可忽视的。

**为什么没有选择 requestAnimationFrame**

这个也不合适，因为这个只能在重新渲染之前，才能够执行一次，而如果我们包装成一个任务，放入到任务队列中，那么只要没到重新渲染的时间，就可以一直从任务队列里面获取任务来执行。

而且 requestAnimationFrame 还会有一定的兼容性问题，safari 和 edge 浏览器是将 requestAnimationFrame 放到渲染之后执行的，chrome 和 firefox 是将 requestAnimationFrame 放到渲染之前执行的，所以这里存在不同的浏览器有不同的执行顺序的问题。

> 根据标准，应该是放在渲染之前。

**为什么没有选择包装成一个微任务？**

这是因为和微任务的执行机制有关系，微任务队列会在清空整个队列之后才会结束。那么微任务会在页面更新前一直执行，直到队列被清空，达不到将主线程还给浏览器的目的。

# Scheduler 调度普通任务

Scheduler 的核心源码位于 packages/scheduler/src/forks/Scheduler.js

## **scheduleCallback**

该函数的主要目的就是用调度任务，该方法的分析如下：

```js
let getCurrentTime = () => performance.now();

// 有两个队列分别存储普通任务和延时任务
// 里面采用了一种叫做小顶堆的算法，保证每次从队列里面取出来的都是优先级最高（时间即将过期）
var taskQueue = []; // 存放普通任务
var timerQueue = []; // 存放延时任务

var maxSigned31BitInt = 1073741823;

// Timeout 对应的值
var IMMEDIATE_PRIORITY_TIMEOUT = -1;
var USER_BLOCKING_PRIORITY_TIMEOUT = 250;
var NORMAL_PRIORITY_TIMEOUT = 5000;
var LOW_PRIORITY_TIMEOUT = 10000;
var IDLE_PRIORITY_TIMEOUT = maxSigned31BitInt;

/**
 *
 * @param {*} priorityLevel 优先级等级
 * @param {*} callback 具体要做的任务
 * @param {*} options { delay: number } 这是一个对象，该对象有 delay 属性，表示要延迟的时间
 * @returns
 */
function unstable_scheduleCallback(priorityLevel, callback, options) {
  // 获取当前的时间
  var currentTime = getCurrentTime();

  var startTime;
  // 整个这个 if.. else 就是在设置起始时间，如果有延时，起始时间需要添加上这个延时
  if (typeof options === "object" && options !== null) {
    var delay = options.delay;
    // 如果设置了延时时间，那么 startTime 就为当前时间 + 延时时间
    if (typeof delay === "number" && delay > 0) {
      startTime = currentTime + delay;
    } else {
      startTime = currentTime;
    }
  } else {
    startTime = currentTime;
  }

  var timeout;
  // 根据传入的优先级等级来设置不同的 timeout
  switch (priorityLevel) {
    case ImmediatePriority:
      timeout = IMMEDIATE_PRIORITY_TIMEOUT;
      break;
    case UserBlockingPriority:
      timeout = USER_BLOCKING_PRIORITY_TIMEOUT;
      break;
    case IdlePriority:
      timeout = IDLE_PRIORITY_TIMEOUT;
      break;
    case LowPriority:
      timeout = LOW_PRIORITY_TIMEOUT;
      break;
    case NormalPriority:
    default:
      timeout = NORMAL_PRIORITY_TIMEOUT;
      break;
  }
  // 接下来就计算出过期时间
  // 计算出来的时间有些比当前时间要早，绝大部分比当前的时间要晚一些
  var expirationTime = startTime + timeout;

  // 创建一个新的任务
  var newTask = {
    id: taskIdCounter++, // 任务 id
    callback, // 该任务具体要做的事情
    priorityLevel, // 任务的优先级别
    startTime, // 任务开始时间
    expirationTime, // 任务的过期时间
    sortIndex: -1, // 用于后面在小顶堆（这是一种算法，可以始终从任务队列中拿出最优先的任务）进行排序的索引
  };
  if (enableProfiling) {
    newTask.isQueued = false;
  }

  if (startTime > currentTime) {
    // This is a delayed task.
    // 说明这是一个延时任务
    newTask.sortIndex = startTime;
    // 将该任务推入到 timerQueue 的任务队列中
    push(timerQueue, newTask);
    if (peek(taskQueue) === null && newTask === peek(timerQueue)) {
      // 进入此 if，说明 taskQueue 里面的任务已经执行完毕了
      // 并且从 timerQueue 里面取出一个最新的任务又是当前任务
      // All tasks are delayed, and this is the task with the earliest delay.

      // 下面的 if.. else 就是一个开关
      if (isHostTimeoutScheduled) {
        // Cancel an existing timeout.
        cancelHostTimeout();
      } else {
        isHostTimeoutScheduled = true;
      }
      // Schedule a timeout.
      // 如果是延时任务，调用 requestHostTimeout 进行任务的调度
      requestHostTimeout(handleTimeout, startTime - currentTime);
    }
  } else {
    // 说明不是延时任务
    newTask.sortIndex = expirationTime; // 设置了 sortIndex 后，可以在任务队列里面进行一个排序
    // 推入到 taskQueue 任务队列
    push(taskQueue, newTask);
    if (enableProfiling) {
      markTaskStart(newTask, currentTime);
      newTask.isQueued = true;
    }
    // Schedule a host callback, if needed. If we're already performing work,
    // wait until the next time we yield.
    // 最终调用 requestHostCallback 进行任务的调度
    if (!isHostCallbackScheduled && !isPerformingWork) {
      isHostCallbackScheduled = true;
      requestHostCallback(flushWork);
    }
  }

  // 向外部返回任务
  return newTask;
}
```

该方法主要注意以下几个关键点：

- 关于任务队列有两个，一个 taskQueue，另一个是 timerQueue，taskQueue 存放普通任务，timerQueue 存放延时任务，任务队列内部用到了小顶堆的算法，保证始终放进去（push）的任务能够进行正常的排序，回头通过 peek 取出任务时，始终取出的是时间优先级最高的那个任务
- 根据传入的不同的 priorityLevel，会进行不同的 timeout 的设置，任务的 timeout 时间也就不一样了，有的比当前时间还要小，这个代表立即需要执行的，绝大部分的时间比当前时间大。

![image-20221229145930771](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-12-29-065931.png)

- 不同的任务，最终调用的函数不一样
  - 普通任务：requestHostCallback(flushWork)
  - 延时任务：requestHostTimeout(handleTimeout, startTime - currentTime);

## requestHostCallback 和 schedulePerformWorkUntilDeadline

```javascript
/**
 * 
 * @param {*} callback 是在调用的时候传入的 flushWork
 * requestHostCallback 这个函数没有做什么事情，主要就是调用 schedulePerformWorkUntilDeadline
 */
function requestHostCallback(callback) {
  scheduledHostCallback = callback;
  // scheduledHostCallback ---> flushWork
  if (!isMessageLoopRunning) {
    isMessageLoopRunning = true;
    schedulePerformWorkUntilDeadline(); // 实例化 MessageChannel 进行后面的调度
  }
}

let schedulePerformWorkUntilDeadline; // undefined
if (typeof localSetImmediate === 'function') {
  // Node.js and old IE.
  // https://github.com/facebook/react/issues/20756
  schedulePerformWorkUntilDeadline = () => {
    localSetImmediate(performWorkUntilDeadline);
  };
} else if (typeof MessageChannel !== 'undefined') {
  // 大多数情况下，使用的是 MessageChannel
  const channel = new MessageChannel();
  const port = channel.port2;
  channel.port1.onmessage = performWorkUntilDeadline;
  schedulePerformWorkUntilDeadline = () => {
    port.postMessage(null);
  };
} else {
  // setTimeout 进行兜底
  schedulePerformWorkUntilDeadline = () => {
    localSetTimeout(performWorkUntilDeadline, 0);
  };
}
```

- requestHostCallback 主要就是调用了 schedulePerformWorkUntilDeadline
- schedulePerformWorkUntilDeadline 一开始是 undefiend，根据不同的环境选择不同的生成宏任务的方式

## performWorkUntilDeadline

```js
let startTime = -1;
const performWorkUntilDeadline = () => {
  // scheduledHostCallback ---> flushWork
  if (scheduledHostCallback !== null) {
    // 获取当前的时间
    const currentTime = getCurrentTime();
    // Keep track of the start time so we can measure how long the main thread
    // has been blocked.
    // 这里的 startTime 并非 unstable_scheduleCallback 方法里面的 startTime
    // 而是一个全局变量，默认值为 -1
    // 用来测量任务的执行时间，从而能够知道主线程被阻塞了多久
    startTime = currentTime;
    const hasTimeRemaining = true; // 默认还有剩余时间

    // If a scheduler task throws, exit the current browser task so the
    // error can be observed.
    //
    // Intentionally not using a try-catch, since that makes some debugging
    // techniques harder. Instead, if `scheduledHostCallback` errors, then
    // `hasMoreWork` will remain true, and we'll continue the work loop.
    let hasMoreWork = true; // 默认还有需要做的任务
    try {
      // scheduledHostCallback ---> flushWork(true, 开始时间): boolean
      // 如果是 true，代表工作没做完
      // false 代表没有任务了
      hasMoreWork = scheduledHostCallback(hasTimeRemaining, currentTime);
    } finally {
      if (hasMoreWork) {
        // If there's more work, schedule the next message event at the end
        // of the preceding one.
        // 那么就使用 messageChannel 进行一个 message 事件的调度，就将任务放入到任务队列里面
        schedulePerformWorkUntilDeadline();
      } else {
        // 说明任务做完了
        isMessageLoopRunning = false;
        scheduledHostCallback = null; // scheduledHostCallback 之前为 flushWork，设置为 null
      }
    }
  } else {
    isMessageLoopRunning = false;
  }
  // Yielding to the browser will give it a chance to paint, so we can
  // reset this.
  needsPaint = false;
};
```

- 该方法实际上主要就是在调用 scheduledHostCallback（flushWork），调用之后，返回一个布尔值，根据这个布尔值来判断是否还有剩余的任务，如果还有，就是用 messageChannel 进行一个宏任务的包装，放入到任务队列里面

## flushWork 和 workLoop

```js
/**
 *
 * @param {*} hasTimeRemaining 是否有剩余的时间，一开始是 true
 * @param {*} initialTime 做这一个任务时开始执行的时间
 * @returns
 */
function flushWork(hasTimeRemaining, initialTime) {
  // ...
  try {
    if (enableProfiling) {
      try {
        // 核心实际上是这一句，调用 workLoop
        return workLoop(hasTimeRemaining, initialTime);
      } catch (error) {
        // ...
      }
    } else {
      // 核心实际上是这一句，调用 workLoop
      return workLoop(hasTimeRemaining, initialTime);
    }
  } finally {
    // ...
  }
}

/**
 *
 * @param {*} hasTimeRemaining 是否有剩余的时间，一开始是 true
 * @param {*} initialTime 做这一个任务时开始执行的时间
 * @returns
 */
function workLoop(hasTimeRemaining, initialTime) {
  let currentTime = initialTime;
  // 该方法实际上是用来遍历 timerQueue，判断是否有已经到期了的任务
  // 如果有，将这个任务放入到 taskQueue
  advanceTimers(currentTime);
  // 从 taskQueue 里面取一个任务出来
  currentTask = peek(taskQueue);
  while (
    currentTask !== null &&
    !(enableSchedulerDebugging && isSchedulerPaused)
  ) {
    if (
      currentTask.expirationTime > currentTime &&
      (!hasTimeRemaining || shouldYieldToHost())
    ) {
      // This currentTask hasn't expired, and we've reached the deadline.
      // currentTask.expirationTime > currentTime 表示任务还没有过期
      // hasTimeRemaining 代表是否有剩余时间
      // shouldYieldToHost 任务是否应该暂停，归还主线程
      // 那么我们就跳出 while
      break;
    }
    // 没有进入到上面的 if，说明这个任务到过期时间，并且有剩余时间来执行，没有到达需要浏览器渲染的时候
    // 那我们就执行该任务即可
    const callback = currentTask.callback; // 拿到这个任务
    if (typeof callback === "function") {
        // 说明当前的任务是一个函数，我们执行该任务

      currentTask.callback = null;
      currentPriorityLevel = currentTask.priorityLevel;
      const didUserCallbackTimeout = currentTask.expirationTime <= currentTime;
      if (enableProfiling) {
        markTaskRun(currentTask, currentTime);
      }
      // 任务的执行实际上就是在这一句
      const continuationCallback = callback(didUserCallbackTimeout);
      currentTime = getCurrentTime();
      if (typeof continuationCallback === "function") {
        // If a continuation is returned, immediately yield to the main thread
        // regardless of how much time is left in the current time slice.
        // $FlowFixMe[incompatible-use] found when upgrading Flow
        currentTask.callback = continuationCallback;
        if (enableProfiling) {
          // $FlowFixMe[incompatible-call] found when upgrading Flow
          markTaskYield(currentTask, currentTime);
        }
        advanceTimers(currentTime);
        return true;
      } else {
        if (enableProfiling) {
          // $FlowFixMe[incompatible-call] found when upgrading Flow
          markTaskCompleted(currentTask, currentTime);
          // $FlowFixMe[incompatible-use] found when upgrading Flow
          currentTask.isQueued = false;
        }
        if (currentTask === peek(taskQueue)) {
          pop(taskQueue);
        }
        advanceTimers(currentTime);
      }
    } else {
      // 直接弹出
      pop(taskQueue);
    }
    // 再从 taskQueue 里面拿一个任务出来
    currentTask = peek(taskQueue);
  }
  // Return whether there's additional work
  if (currentTask !== null) {
    // 如果不为空，代表还有更多的任务，那么回头外部的 hasMoreWork 拿到的就也是 true
    return true;
  } else {
    // taskQueue 这个队列是空了，那么我们就从 timerQueue 里面去看延时任务
    const firstTimer = peek(timerQueue);
    if (firstTimer !== null) {
      requestHostTimeout(handleTimeout, firstTimer.startTime - currentTime);
    }
    // 没有进入上面的 if，说明 timerQueue 里面的任务也完了，返回 false，回头外部的 hasMoreWork 拿到的就也是 false
    return false;
  }
}
```

- flushWork 主要就是在调用 workLoop
- workLoop 首先有一个 while 循环，该 while 循环保证了能够从任务队列中不停的取任务出来

```
 while (
    currentTask !== null &&
    !(enableSchedulerDebugging && isSchedulerPaused)
 ){
   // ...
 }
```

- 当然，不是说一直从任务队列里面取任务出来执行就完事儿，每次取出一个任务后，我们还需要一系列的判断

```
if (
      currentTask.expirationTime > currentTime &&
      (!hasTimeRemaining || shouldYieldToHost())
 ) {
  break;
}
```

- currentTask.expirationTime > currentTime 表示任务还没有过期
- hasTimeRemaining 代表是否有剩余时间
- shouldYieldToHost 任务是否应该暂停，归还主线程
- 如果进入 if，说明因为某些原因不能再执行任务，需要立即归还主线程，那么我们就跳出 while

## shouldYieldToHost

```js
function shouldYieldToHost() {
    // getCurrentTime 获取当前时间
    // startTime 是我们任务开始时的时间，一开始是 -1，之后任务开始时，将任务开始时的时间复值给了它
  const timeElapsed = getCurrentTime() - startTime;
  // frameInterval 默认设置的是 5ms
  if (timeElapsed < frameInterval) {
    // 主线程只被阻塞了一点点时间，远远没达到需要归还的时候
    return false;
  }
  // 如果没有进入上面的 if，说明主线程已经被阻塞了一段时间了
  // 需要归还主线程
  if (enableIsInputPending) {
    if (needsPaint) {
      // There's a pending paint (signaled by `requestPaint`). Yield now.
      return true;
    }
    if (timeElapsed < continuousInputInterval) {
      // We haven't blocked the thread for that long. Only yield if there's a
      // pending discrete input (e.g. click). It's OK if there's pending
      // continuous input (e.g. mouseover).
      if (isInputPending !== null) {
        return isInputPending();
      }
    } else if (timeElapsed < maxInterval) {
      // Yield if there's either a pending discrete or continuous input.
      if (isInputPending !== null) {
        return isInputPending(continuousOptions);
      }
    } else {
      // We've blocked the thread for a long time. Even if there's no pending
      // input, there may be some other scheduled work that we don't know about,
      // like a network event. Yield now.
      return true;
    }
  }

  // `isInputPending` isn't available. Yield now.
  return true;
}
```

- 首先计算 timeElapsed，然后判断是否超时，没有的话就返回 false，表示不需要归还，否则就返回 true，表示需要归还。
- frameInterval 默认设置的是 5ms

## advanceTimers

```js
function advanceTimers(currentTime) {
  // Check for tasks that are no longer delayed and add them to the queue.
  // 从 timerQueue 里面获取一个任务
  let timer = peek(timerQueue);
  // 遍历整个 timerQueue
  while (timer !== null) {
    if (timer.callback === null) {
      // 这个任务没有对应的要执行的 callback，直接从这个队列弹出
      pop(timerQueue);
    } else if (timer.startTime <= currentTime) {
      // 进入这个分支，说明当前的任务已经不再是延时任务
      // 我们需要将其转移到 taskQueue
      pop(timerQueue);
      timer.sortIndex = timer.expirationTime;
      push(taskQueue, timer); // 推入到 taskQueue
      // ...
    } else {
      return;
    }
    // 从 timerQueue 里面再取一个新的进行判断
    timer = peek(timerQueue);
  }
}
```

- 该方法就是遍历整个 timerQueue，查看是否有已经过期的方法，如果有，不是说直接执行，而是将这个过期的方法添加到 taskQueue 里面。



# Hooks原理

> 面试题：Hook是如何保存函数组件状态的？为什么不能在循环，条件或嵌套函数中调用 Hook ？



## Hook 内部介绍

在 React 中，针对 Hook 有三种策略，或者说三种类型的 dispatcher：

- HooksDispatcherOnMount：负责初始化工作，让函数组件的一些初始化信息挂载到 Fiber 上面

```js
/* 函数组件初始化用的 hooks */
const HooksDispatcherOnMount: Dispatcher = {
  readContext,
  ...
  useCallback: mountCallback,
  useEffect: mountEffect,
  useMemo: mountMemo,
  useReducer: mountReducer,
  useRef: mountRef,
  useState: mountState,
  ...
};
```

- HoosDispatcherOnUpdate：函数组件进行更新的时候，会执行该对象所对应的方法。此时 Fiber 上面已经存储了函数组件的相关信息，这些 Hook 需要做的就是去获取或者更新维护这些 FIber 的信息

```js
/* 函数组件更新用的 hooks */
const HooksDispatcherOnUpdate: Dispatcher = {
  readContext,
  ...
  useCallback: updateCallback,
  useContext: readContext,
  useEffect: updateEffect,
  useMemo: updateMemo,
  useReducer: updateReducer,
  useRef: updateRef,
  useState: updateState,
  ...
};
```

- ContextOnlyDispatcher:这个是和报错相关，防止开发者在函数组件外部调用 Hook

```js
/* 当hooks不是函数组件内部调用的时候，调用这个hooks对象下的hooks，所以报错。 */
export const ContextOnlyDispatcher: Dispatcher = {
  readContext,
  ...
  useCallback: throwInvalidHookError,
  useContext: throwInvalidHookError,
  useEffect: throwInvalidHookError,
  useMemo: throwInvalidHookError,
  useReducer: throwInvalidHookError,
  useRef: throwInvalidHookError,
  useState: throwInvalidHookError,
  ...
};
```

总结一下：

- mount 阶段：函数组件是进行初始化，那么此时调用的就是 mountXXX 对应的函数
- update 阶段：函数组件进行状态的更新，调用的就是 updateXXX 对应的函数
- 其他场景下（报错）：此时调用的就是 throwInvaildError



当 FC 进入到 render 流程的时候，首先会判断是初次渲染还是更新：

```js
if(current !== null && current.memoizedState !== null) {
  // 说明是 update
  ReactCurrentDispatcher.current = HooksDispatcherOnUpdate;
} else {
  // 说明是 mount
  ReactCurrentDispatcher.current = HooksDispatcherOnMount;
}
```

判断了是mount还是update之后，会给 ReactCurrentDispatcher.current 赋值对应的 dispatcher，因为赋值了不同的上下文对象，因此就可以根据不同上下文对象调用不同的方法。

假设有嵌套的 hook：

```js
useEffect(()=>{
  useState(0);
})
```

那么此时的上下文对象指向 ContextOnlyDispatcher，最终执行的就是 throwInvalidHookError，抛出错误。



接下来我们来看一下 hook 的一个数据结构

```js
const hook = {
  memoizedState: null,
  baseState: null,
  baseQueue: null,
  queue: null,
  next: null
}
```

这里需要注意 memoizedState 字段，因为在 FiberNode 上面也有这么一个字段，与 Hook 对象上面的 memoizedState 存储的东西是不一样的：

- FiberNode.memoizedState：保存的是 Hook 链表里面的第一个链表
- hook.memoizedState：某个 hook 自身的数据

不同类型的 hook，**hook.memoizedState 所存储的内容也是不同的**：

- useState：对于 const [state, updateState] = useState(initialState)，memoizedState 保存的是 state 的值
- useReducer：对于 const [state, dispatch] = useReducer(reducer, { } )，memoizedState 保存的是 state 的值
- useEffect：对于 useEffect( callback, [...deps] )，memoizedState 保存的是 callback、[...deps] 等数据
- useRef：对于 useRef(initialValue)，memoizedState 保存的是 { current: initialValue}
- useMemo：对于 useMemo( callback, [...deps] )，memoizedState 保存的是 [callback( )、[...deps]] 数据
- useCallback：对于 useCallback( callback, [...deps] )，memoizedState 保存的是 [callback、[...deps]] 数据

有些 Hook 不需要 memoizedState 保存自身数据，比如 useContext。



## Hook 的一个执行流程

当 FC 进入到 render 阶段时，会被 renderWithHooks 函数处理执行：

```js
export function renderWithHooks(current, workInProgress, Component, props, secondArg, nextRenderLanes) {
  renderLanes = nextRenderLanes;
  currentlyRenderingFiber = workInProgress;

  // 每一次执行函数组件之前，先清空状态 （用于存放hooks列表）
  workInProgress.memoizedState = null;
  // 清空状态（用于存放effect list）
  workInProgress.updateQueue = null;
  // ...

  // 判断组件是初始化流程还是更新流程
  // 如果初始化用 HooksDispatcherOnMount 对象
  // 如果更新用 HooksDispatcherOnUpdate 对象
  // 初始化对应的上下文对象，不同的上下文对象对应了一组不同的方法
  ReactCurrentDispatcher.current =
    current === null || current.memoizedState === null
      ? HooksDispatcherOnMount
      : HooksDispatcherOnUpdate;

  // 执行我们真正函数组件，所有的 hooks 将依次执行。
  let children = Component(props, secondArg);

  // ...

  // 判断环境
  finishRenderingHooks(current, workInProgress);
  return children;
}

function finishRenderingHooks(current, workInProgress) {
    // 防止 hooks 在函数组件外部调用，如果调用直接报错
    ReactCurrentDispatcher.current = ContextOnlyDispatcher;
    // ...
}

```

renderWithHooks 会被每次函数组件触发时（mount、update），该方法就会清空 workInProgress 的 memoizedState 以及 updateQueue，接下来判断该组件究竟是初始化还是更新，为 ReactCurrentDispatcher.current 赋值不同的上下文对象，之后调用

Component 方法来执行函数组件，组件里面所书写的 hook 就会依次执行。



接下来我们来以 useState 为例看一下整个 hook 的执行流程：

```jsx
function App(){
  const [count, setCount] = useState(0);
  return <div onClick={()=>setCount(count+1)}>{count}</div>
}
```

接下来就会根据你是 mount 还是 update 调用不同上下文里面所对应的方法。

mount 阶段调用的是 mountState，相关代码如下：

```js
function mountState(initialState) {
  // 1. 拿到 hook 对象
  const hook = mountWorkInProgressHook();
  if (typeof initialState === "function") {
    initialState = initialState();
  }
  
  // 2. 初始化hook的属性
  // 2.1 设置 hook.memoizedState/hook.baseState
  hook.memoizedState = hook.baseState = initialState;
  const queue = {
    pending: null,
    lanes: NoLanes,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: initialState,
  };
  // 2.2 设置 hook.queue
  hook.queue = queue;
  
  // 2.3 设置 hook.dispatch
  const dispatch = (queue.dispatch = dispatchSetState.bind(
    null,
    currentlyRenderingFiber,
    queue
  ));
  
  // 3. 返回[当前状态, dispatch函数]
  return [hook.memoizedState, dispatch];
}
```

上面在执行 mountState 的时候，首先调用了 mountWorkInProgressHook，该方法的作用就是创建一个 hook 对象，相关代码如下：

```js
function mountWorkInProgressHook() {
  const hook = {
    memoizedState: null, // Hook 自身维护的状态

    baseState: null,
    baseQueue: null,
    queue: null, // Hook 自身维护的更新队列

    next: null, // next 指向下一个 Hook
  };
  
  // 最终 hook 对象是要以链表形式串联起来，因此需要判断当前的 hook 是否是链表的第一个
  if (workInProgressHook === null) {
    // 如果当前组件的 Hook 链表为空，那么就将刚刚新建的 Hook 作为 Hook 链表的第一个节点（头结点） 
    // This is the first hook in the list
    currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
  } else {
    // 如果当前组件的 Hook 链表不为空，那么就将刚刚新建的 Hook 添加到 Hook 链表的末尾（作为尾结点）
    // Append to the end of the list
    workInProgressHook = workInProgressHook.next = hook;
  }
  return workInProgressHook;
}
```



假设现在我们有如下的一个组件：

```js
function App() {
  const [number, setNumber] = React.useState(0); // 第一个hook
  const [num, setNum] = React.useState(1); // 第二个hook
  const dom = React.useRef(null); // 第三个hook
  React.useEffect(() => {
    // 第四个hook
    console.log(dom.current);
  }, []);
  return (
    <div ref={dom}>
      <div onClick={() => setNumber(number + 1)}> {number} </div>
      <div onClick={() => setNum(num + 1)}> {num}</div>
    </div>
  );
}
```

当上面的函数组件第一次进行初始化后，就会形成一个 hook 的链表：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-03-031041.png" alt="image-20230303111041259" style="zoom:50%;" />

接下来我们来看一下更新，更新的时候会执行 updateXXX 对应的方法，相关的代码如下：

```js
function updateWorkInProgressHook() {
  let nextCurrentHook;
  if (currentHook === null) {
    // 从 alternate 上获取到 fiber 对象
    const current = currentlyRenderingFiber.alternate;
    if (current !== null) {
      // 拿到第一个 hook 对象
      nextCurrentHook = current.memoizedState;
    } else {
      nextCurrentHook = null;
    }
  } else {
    // 拿到下一个 hook
    nextCurrentHook = currentHook.next;
  }

  // 更新 workInProgressHook 的指向
  // 让 workInProgressHook 指向最新的 hook
  let nextWorkInProgressHook; // 下一个要进行工作的 hook
  if (workInProgressHook === null) {
    // 当前是第一个，直接从 fiber 上获取第一个 hook
    nextWorkInProgressHook = currentlyRenderingFiber.memoizedState;
  } else {
    // 取链表的下一个 hook
    nextWorkInProgressHook = workInProgressHook.next;
  }

  // nextWorkInProgressHook 指向的是当前要工作的 hook
  if (nextWorkInProgressHook !== null) {
    // There's already a work-in-progress. Reuse it.
    // 进行复用
    workInProgressHook = nextWorkInProgressHook; 
    nextWorkInProgressHook = workInProgressHook.next;

    currentHook = nextCurrentHook;
  } else {
    // Clone from the current hook.
    // 进行克隆
    if (nextCurrentHook === null) {
      const currentFiber = currentlyRenderingFiber.alternate;
      if (currentFiber === null) {
        // This is the initial render. This branch is reached when the component
        // suspends, resumes, then renders an additional hook.
        const newHook = {
          memoizedState: null,

          baseState: null,
          baseQueue: null,
          queue: null,

          next: null,
        };
        nextCurrentHook = newHook;
      } else {
        // This is an update. We should always have a current hook.
        throw new Error("Rendered more hooks than during the previous render.");
      }
    }

    currentHook = nextCurrentHook;

    const newHook = {
      memoizedState: currentHook.memoizedState,

      baseState: currentHook.baseState,
      baseQueue: currentHook.baseQueue,
      queue: currentHook.queue,

      next: null,
    };
    // 之后的操作和 mount 时候一样
    if (workInProgressHook === null) {
      // This is the first hook in the list.
      currentlyRenderingFiber.memoizedState = workInProgressHook = newHook;
    } else {
      // Append to the end of the list.
      workInProgressHook = workInProgressHook.next = newHook;
    }
  }
  return workInProgressHook;
}
```

在上面的源码中，有一个非常关键的信息：

```js
// ...
if (nextWorkInProgressHook !== null) {
    // There's already a work-in-progress. Reuse it.
    // 进行复用
    workInProgressHook = nextWorkInProgressHook; 
    nextWorkInProgressHook = workInProgressHook.next;
    
    currentHook = nextCurrentHook;
}
// ...
```

这里如果 nextWorkInProgressHook 不为 null，那么就会复用之前的 hook，这里其实也就解释了为什么 hook 不能放在条件或者循环语句里面

> 面试题：hook 为什么通常放在顶部，而且不能写在条件或者循环语句里面？
>
> 因为更新的过程中，如果通过 if 条件增加或者删除了 hook，那么在复用的时候，就会产生当前hook 的顺序和之前 hook 的顺序不一致的问题。

例如，我们将上面的代码进行修改：

```js
function App({ showNumber }) {
  let number, setNumber
  showNumber && ([ number,setNumber ] = React.useState(0)) // 第一个hooks
  const [num, setNum] = React.useState(1); // 第二个hook
  const dom = React.useRef(null); // 第三个hook
  React.useEffect(() => {
    // 第四个hook
    console.log(dom.current);
  }, []);
  return (
    <div ref={dom}>
      <div onClick={() => setNumber(number + 1)}> {number} </div>
      <div onClick={() => setNum(num + 1)}> {num}</div>
    </div>
  );
}
```

假设第一次父组件传递过来的 showNumber 为 true，此时就会渲染第一个 hook，第二次渲染的时候，假设父组件传递过来的是 false，那么第一个 hook 就不会执行，那么逻辑就会变得如下表所示：

| *hook* 链表顺序 | 第一次     | 第二次     |
| :-------------- | :--------- | :--------- |
| 第一个 *hook*   | *useState* | *useState* |
| 第二个 *hook*   | *useState* | *useRef*   |

那么此时在进行复用的时候就会报错：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-03-072611.png" alt="image-20230303152610928" style="zoom:50%;" />

第二次复用的时候，发现 hook 的类型不同， useState !==useRef，那么就会直接报错。因此开发的时候一定要注意 hook 顺序的一致性。

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-03-031320.jpg" alt="16717800284171" style="zoom: 33%;" />



## 真题解答

> 题目：Hook是如何保存函数组件状态的？为什么不能在循环，条件或嵌套函数中调用 Hook ？
>
> 首先 Hook 是一个对象，大致有如下的结构：
>
> ```js
> const hook = {
> memoizedState: null,
> baseState: null,
> baseQueue: null,
> queue: null,
> next: null
> }
> ```
>
> 不同类型的 hook，hook 的 memoizedState 中保存了不同的值，例如：
>
> - useState：对于 const [state, updateState] = useState(initialState)，memoizedState 保存的是 state 的值
> - useEffect：对于 useEffect( callback, [...deps] )，memoizedState 保存的是 callback、[...deps] 等数据
>
> 一个组件中的 hook 会以链表的形式串起来，FiberNode 的 memoizedState 中保存了 Hooks 链表中的第一个 Hook
>
> 在更新时，会复用之前的 Hook，如果通过 *if* 条件语句，增加或者删除 *hooks*，在复用 *hooks* 过程中，会产生复用 *hooks* 状态和当前 *hooks* 不一致的问题。

# useState和useReducer

> 面试题：useState 和 useReducer 有什么样的区别？



## 基本用法

useState 我们已经非常熟悉了，如下：

```jsx
function App(){
  const [num, setNum] = useState(0);
  
  return <div onClick={()=>setNum(num + 1)}>{num}</div>;
}
```

接下来我们来看一下 useReducer。如果你会 redux，那么 useReducer 对你来讲是非常熟悉的。

```js
const [state, dispatch] = useReducer(
  reducer,	
  initialArg,	
  init	
);
```

接下来我们来看一个计数器的例子：

```js
import { useReducer, useRef } from "react";

// 定义一个初始化的状态
const initialState = { count: 0 };

/**
 * reducer
 * @param {*} state 状态
 * @param {*} action 数据变化的描述对象
 */
function counter(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + action.payload };
    case "DECREMENT":
      return { count: state.count - action.payload };
    default:
      return state;
  }
}

function App() {
  // const [num, setNum] = useState(0);
  // 后期要修改值的时候，都是通过 dispatch 来进行修改
  const [state, dispatch] = useReducer(counter, initialState);
  const selRef = useRef();

  const increment = () => {
    // 做自增操作
    // 1. 你要增加多少？
    const num = selRef.current.value * 1;
    // setNum(num);
    dispatch({
      type: "INCREMENT",
      payload: num,
    });
  };

  const decrement = () => {
    const num = selRef.current.value * 1;
    dispatch({ type: "INCREMENT", payload: num });
  };

  const incrementIfOdd = () => {
    const num = selRef.current.value * 1;
    if (state.count % 2 !== 0) {
      dispatch({ type: "INCREMENT", payload: num });
    }
  };

  const incrementAsync = () => {
    const num = selRef.current.value * 1;
    setTimeout(() => {
      dispatch({ type: "INCREMENT", payload: num });
    }, 1000);
  };

  return (
    <div>
      <p>click {state.count} times</p>
      <select ref={selRef}>
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
      </select>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={incrementIfOdd}>increment if odd</button>
      <button onClick={incrementAsync}>increment async</button>
    </div>
  );
}

export default App;
```

useReducer 还接收第三个参数，第三个参数，是一个惰性初始化函数，简单理解就是可以做额外的初始化工作

```js
// 惰性初始化函数
function init(initialState){
  // 有些时候我们需要基于之前的初始化状态做一些操作，返回新的处理后的初始化值
  // 重新返回新的初始化状态
  return {
    count : initialState.count * 10
  }
}

// 接下来在使用 useReducer 的时候，这个函数就可以作为第三个参数传入
const [state, dispatch] = useReducer(counter, initialState, init);
```



## mount 阶段

**useState 的 mount 阶段**

```js
function mountState(initialState) {
  // 拿到 hook 对象
  const hook = mountWorkInProgressHook();
  // 如果传入的值是函数，则执行函数获取到初始值
  if (typeof initialState === "function") {
    initialState = initialState();
  }
  // 将初始化保存到 hook 对象的 memoizedState 和 baseState 上面
  hook.memoizedState = hook.baseState = initialState;
  const queue = {
    pending: null,
    lanes: NoLanes,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: initialState,
  };
  hook.queue = queue;
  // dispatch 就是用来修改状态的方法
  const dispatch = (queue.dispatch = dispatchSetState.bind(
    null,
    currentlyRenderingFiber,
    queue
  ));
  return [hook.memoizedState, dispatch];
}
```

**useReducer 的mount阶段**

```js
function mountReducer(reducer, initialArg, init) {
  // 创建 hook 对象
  const hook = mountWorkInProgressHook();
  let initialState;
  // 如果有 init 初始化函数，就执行该函数
  // 将执行的结果给 initialState
  if (init !== undefined) {
    initialState = init(initialArg);
  } else {
    initialState = initialArg;
  }
  // 将 initialState 初始值存储 hook 对象的 memoizedState 以及 baseState 上面
  hook.memoizedState = hook.baseState = initialState;
  // 创建 queue 对象
  const queue = {
    pending: null,
    lanes: NoLanes,
    dispatch: null,
    lastRenderedReducer: reducer,
    lastRenderedState: initialState,
  };
  hook.queue = queue;
  const dispatch = (queue.dispatch = dispatchReducerAction.bind(
    null,
    currentlyRenderingFiber,
    queue
  ));
  // 向外部返回初始值和 dispatch 修改方法
  return [hook.memoizedState, dispatch];
}
```

总结一下，mountState 和 mountReducer 的大体流程是一样的，但是有一个区别，mountState 的 queue 里面的 lastRenderedReducer 对应的是 basicStateReducer，而 mountReducer 的 queue 里面的 lastRenderedReducer 对应的是开发者自己传入的 reducer，这里说明了一个问题，useState 的本质就是 useReducer 的一个简化版，只不过在 useState 内部，会有一个内置的 reducer

basicStateReducer 对应的代码如下：

```js
function basicStateReducer(state, action) {
  return typeof action === "function" ? action(state) : action;
}
```



## update 阶段

**useState 的 update 阶段**

```js
function updateState(initialState) {
  return updateReducer(basicStateReducer, initialState);
}
```



**useReducer 的 update 阶段**

```js
function updateReducer(reducer, initialArg, init){
	// 获取对应的 hook
  const hook = updateWorkInProgressHook();
  // 拿到对应的更新队列
  const queue = hook.queue;
  
  queue.lastRenderedReducer = reducer;
  
  // 省略根据 update 链表计算新的 state 的逻辑
  // 这里有一套完整的关于 update 的计算流程
  
  const dispatch = queue.dispatch;
  
  return [hook.memoizedState, dispatch];
}
```



## 真题解答

>题目：useState 和 useReducer 有什么样的区别？
>
>参考答案：
>
>useState 本质上就是一个简易版的 useReducer。
>
>在 mount 阶段，两者之间的区别在于：
>
>- useState 的 lastRenderedReducer 为 basicStateReducer
>- useReducer 的 lastRenderedReducer 为传入的 reducer 参数
>
>所以，useState 可以视为 reducer 参数为 basicStateReducer 的 useReducer
>
>在 update 阶段，updateState 内部直接调用的就是 updateReducer，传入的 reducer 仍然是 basicStateReducer。

# useCallback和useMemo

> 面试题：useCallback 和 useMemo 的区别是什么？



## useCallback

useCallback 用法如下：

```js
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

使用 useCallback 最终会得到一个缓存的函数，该缓存函数会在 a 或者 b 依赖项发生变化时再更新。



**mount 阶段**

在 mount 阶段执行的就是 mountCallback，相关代码如下：

```js
function mountCallback(callback, deps) {
  // 首先还是创建一个 hook 对象
  const hook = mountWorkInProgressHook();
  // 依赖项
  const nextDeps = deps === undefined ? null : deps;
  // 把要缓存的函数和依赖数组存储到 hook 对象上
  hook.memoizedState = [callback, nextDeps];
  // 向外部返回缓存函数
  return callback;
}
```

在上面的代码中，首先会调用 mountWorkInProgressHook 得到一个 hook 对象，在 hook 对象的 memoizedState 上面保存 callback 以及依赖项目，最后向外部返回 callback



**update阶段**

update 调用的是 updateCallback，相关代码如下：

```js
function updateCallback(callback, deps) {
  // 拿到之前的 hook 对象
  const hook = updateWorkInProgressHook();
  // 新的依赖项
  const nextDeps = deps === undefined ? null : deps;
  // 之前的值，也就是 [callback, nextDeps]
  const prevState = hook.memoizedState;
  if (prevState !== null) {
    if (nextDeps !== null) {
      const prevDeps = prevState[1]; // 拿到之前的依赖项
      // 对比依赖项是否相同
      if (areHookInputsEqual(nextDeps, prevDeps)) {
        // 相同返回 callback
        return prevState[0];
      }
    }
  }
  // 否则重新缓存
  hook.memoizedState = [callback, nextDeps];
  return callback;
}
```

在组件更新阶段，首先拿到之前的 hook 对象，从之前的 hook 对象的 memoizedState 上面拿到之前的依赖项，和新传入的依赖项做一个对比，如果相同，则返回之前缓存的 callback，否则就重新缓存，返回新的 callback



## useMemo

用法如下：

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

使用 useMemo 缓存的是一个值，这个值会在 a 或者 b 发生变化的时候重新进行计算并缓存。



**mount 阶段**

mount 阶段调用的是 mountMemo，代码如下：

```js
function mountMemo(nextCreate, deps) {
  // 创建 hook 对象
  const hook = mountWorkInProgressHook();
  // 存储依赖项
  const nextDeps = deps === undefined ? null : deps;

  // ... 
  
  // 执行传入的函数，拿到返回值
  const nextValue = nextCreate();
  // 将函数返回值和依赖存储到 memoizedState 上面
  hook.memoizedState = [nextValue, nextDeps];
  // 返回函数计算得到的值
  return nextValue;
}
```

在 mount 阶段首先会调用 mountWorkInProgressHook 方法得到一个 hook 对象，之后执行传入的函数（第一个参数）得到计算值，将计算值和依赖项目存储到 hook 对象的 memoizedState 上面，最后向外部返回计算得到的值。



**update 阶段**

update 阶段调用的是 updateMemo，相关代码如下：

```js
function updateMemo(nextCreate, deps) {
  // 获取之前的 hook 对象
  const hook = updateWorkInProgressHook();
  // 新的依赖项
  const nextDeps = deps === undefined ? null : deps;
  // 获取之前的 memoizedState，也就是 [nextValue, nextDeps]
  const prevState = hook.memoizedState;
  if (prevState !== null) {
    // Assume these are defined. If they're not, areHookInputsEqual will warn.
    if (nextDeps !== null) {
      // 拿到之前的依赖项
      const prevDeps = prevState[1];
      // 比较和现在的依赖项是否相同
      if (areHookInputsEqual(nextDeps, prevDeps)) {
        // 如果相同，则返回之前的值
        return prevState[0];
      }
    }
  }
  // ...
  // 否则重新计算
  const nextValue = nextCreate();
  hook.memoizedState = [nextValue, nextDeps];
  return nextValue;
}
```

首先，仍然是从 updateWorkInProgressHook 上面拿到之前的 hook 对象，从而获取到之前的依赖项目，然后和新传入的依赖项目就行一个对比，如果依赖项目没有变化，则返回之前的计算值，否则就执行传入的函数重新进行计算，最后向外部返回新的计算值。



## 真题解答

> 题目：useCallback 和 useMemo 的区别是什么？
>
> 参考答案：
>
> 在 useCallback 内部，会将函数和依赖项一起缓存到 hook 对象的 memoizedState 属性上，在组件更新阶段，首先会拿到之前的 hook 对象，从之前的 hook 对象的 memoizedState 属性上获取到之前的依赖项目，对比依赖项目是否相同，如果相同返回之前的 callback，否则就重新缓存，然后返回新的 callback。
>
> 在 useMemo 内部，会将传入的函数执行并得到计算值，将计算值和依赖项目存储到 hook 对象的 memoizedState 上面，最后向外部返回计算得到的值。更新的时候首先是从 updateWorkInProgressHook 上拿到之前的 hook 对象，从而获取到之前的依赖值，和新传入的依赖进行一个对比，如果相同，就返回上一次的计算值，否则就重新调用传入的函数得到新的计算值并缓存，最后向外部返回新的计算值。

# useRef

> 面试题：useRef 是干什么的？ref 的工作流程是怎样的？什么叫做 ref 的失控？



## useRef 各个阶段

ref 是英语 reference（引用）的缩写，在 React 中，开发者可以通过 ref 保存一个对 DOM 的引用。事实上，任何需要被引用的数据，都可以保存在 ref 上。在 React 中，出现过 3 种 ref 引用模式：

- String 类型（已不推荐使用）
- 函数类型
- { current : T }



目前关于创建 ref，类组件推荐使用 createRef 方法，函数组件推荐使用 useRef

用法如下：

```js
const refContainer = useRef(initialValue);
```



**mount 阶段**

mount 阶段调用的是 mountRef，对应的代码如下：

```js
function mountRef(initialValue) {
  // 创建 hook 对象
  const hook = mountWorkInProgressHook();
  const ref = { current: initialValue };
  // hook 对象的 memoizedState 值为 { current: initialValue }
  hook.memoizedState = ref;
  return ref;
}
```

在 mount 阶段，首先调用 mountWorkInProgressHook 方法得到一个 hook 对象，该 hook 对象的 memoizedState 上面会缓存一个键为 current 的对象 { current: initialValue }，之后向外部返回该对象。



**update 阶段**

update 阶段调用的是 updateRef，相关代码如下：

```js
function updateRef(initialValue) {
  // 拿到当前的 hook 对象
  const hook = updateWorkInProgressHook();
  return hook.memoizedState;
}
```



除了 useRef 以外，createRef 也会创建相同数据结构的 ref：

```js
function createRef(){
  const refObject = {
    current: null
  }
  return refObject;
}
```



## ref 的工作流程

ref 创建之后，会挂在 HostComponent 或者 ClassComponent 上面，形成 ref props，例如：

```js
// HostComponent
<div ref={domRef}></div>
// ClassComponent
<App ref={comRef}/>
```

整个 ref 的工作流程分为两个阶段：

- render 阶段：标记 ref flag
- commit 阶段：根据所标记的 ref flag，执行 ref 相关的操作

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-03-091957.png" alt="image-20230303171956632" style="zoom:50%;" />

上图中，markRef 表示的就是标记 ref，相关代码如下：

```js
function markRef(current, workInProgress){
  const ref = workInProgress.ref;
  if((current === null && ref !== null) || (current !== null && current.ref !== ref)){
    // 标记 Reg tag
    workInProgress.flags != Ref;
  }
}
```

有两种情况会标记 ref：

- mount 阶段并且 ref props 不为空
- update 阶段并且 ref props 发生了变化



标记完 ref 之后，来到了 commit 阶段，会在 mutation 子阶段执行 ref 的删除操作，删除旧的 ref：

```js
function commitMutationOnEffectOnFiber(finishedWork, root){
  // ...
  if(flags & Ref){
    const current = finishedWork.alternate;
    if(current !== null){
      // 移除旧的 ref
      commitDetachRef(current);
    }
  }
  // ...
}
```

上面的代码中，commitDetachRef 方法要做的事情就是移除旧的 ref，相关代码如下：

```js
function commitDetachRef(current){
  const currentRef = current.ref;
  
  if(currentRef !== null){
    if(typeof currentRef === 'function'){
      // 函数类型 ref，执行并传入 null 作为参数
      currentRef(null);
    } else {
      // { current: T } 类型的 ref，重置 current 指向
      currentRef.current = null;
    }
  }
}
```

删除完成后，会在 Layout 子阶段重新赋值新的 ref，相关代码如下：

```js
function commitLayoutEffectOnFiber(finishedRoot, current, finishedWork, committedLanes){
  // 省略代码
  if(finishedWork.flags & Ref){
    commitAttachRef(finishedWork);
  }
}
```

对应的方法 commitAttachRef 就是用来重新赋值新 ref 的，相关代码如下：

```js
function commitAttachRef(finishedWork){
  const ref = finishedWork.ref;
  if(ref !== null){
    const instance = finishedWork.stateNode;
    let instanceToUse;
    switch(finishedWork.tag){
      case HostComponent:
        // HostComponent 需要获取对应的 DOM 元素
        instanceToUse = getPublicInstance(instance);
        break;
      default:
        // ClassComponent 使用 FiberNode.stateNode 保存实例
        instanceToUse = instance;
    }
    
    if(typeof ref === 'function'){
      // 函数类型，执行函数并将实例传入
      let retVal;
      retVal = ref(instanceToUse);
    } else {
      // { current: T } 类型则更新 current 指向
      ref.current = instanceToUse;
    }
  }
}
```



## ref 的失控

当我们使用 ref 保存对 DOM 的引用时，那么就有可能会造成 ref 的失控。

所谓 ref 的失控，开发者通过 ref 操作了 DOM，但是这一行为本来应该是由 React 接管的，两者产生了冲突，这种冲突我们就称之为 ref 的失控。

考虑下面这一段代码：

```js
function App(){
  const inputRef = useRef(null);
  
  useEffect(()=>{
    // 操作1
    inputRef.current.focus();
    
    // 操作2
    inputRef.current.getBoundingClientRect();
    
    // 操作3
    inputRef.current.style.width = '500px';
  }, []);
  
  return <input ref={inputRef}/>;
}
```

在上面的三个操作中，第三个操作是不推荐的。

React 作为一个视图层框架，接管了大部分和视图相关的操作，这样开发者可以专注于业务上面的开发逻辑。

上面的三个操作中，前面两个并没有被 React 接管，所以当产生这样的操作时，可以百分百确定是来自于开发者的操作。但是在操作三中，并不能确定该操作究竟是 React 的行为还是开发者的行为，甚至两者会产生冲突。

例如我们再聚一个例子：

```js
function App(){
  const [isShow, setShow] = useState(true);
  const ref = useRef(null);
  
  return (
    <div>
    	<button onClick={() => setShow(!isShow)}>React操作DOM</button>
      <button onClick={() => ref.current.remove()}>开发者DOM</button>
      {isShow && <p ref={ref}>Hello</p>}
    </div>
  );
}
```

上面的代码就是一个典型的 ref 失控的案例。第一个按钮通过 isShow 来控制 p 是否显示，这是 React 的行为，第二个按钮通过 ref 直接拿到了 p 的 DOM 对象，然后进行显隐操作，两者会产生冲突，上面的两个按钮，先点击任意一个，然后再点击另外一个就会报错。



## ref 失控的防治

ref 失控的本质：由于开发者通过 ref 操作了 DOM，而这一行为本来应该是由 React 来进行接管的，两者之间发生了冲突而导致的。

因此我们可以从下面两个方面来进行防治：

- 防：控制 ref 失控的影响范围，使 ref 的失控更加容易被定位
- 治：从 ref 引用的数据结构入手，尽力避免可能引起的失控操作



**防**

在上一章我们介绍过高阶组件，在高阶组件内部是无法将 ref 直接指向 DOM 的，我们需要进行 ref 的转发。可以通过 forwardRef API 进行一个 ref 的转发，将 ref 转发的这个操作，实际上就将 ref 失控的范围控制在了单个组件内，不会出现跨越组件的 ref 失控。

因为是手动的进行 ref 的转发，所以发生 ref 失控的时候，能够更加容易的进行错误的定位



**治**

之前我们介绍过 useImperativeHandle 这个 Hook，它可以在使用 ref 时向父组件传递自定义的引用值：

```js
const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    focus(){
      realInputRef.current.focus();
    }
  }));
  return <input {...props} ref={realInputRef} />
});
```

在上面的代码中，我们通过 useImperativeHandle 来定制 ref 所引用的内容，那么在外部开发者通过 ref 只能拿到：

```js
{
  focus(){
      realInputRef.current.focus();
  }
}
```





## 真题解答

> 题目：useRef 是干什么的？ref 的工作流程是怎样的？什么叫做 ref 的失控？
>
> 参考答案：
>
> useRef 的主要作用就是用来创建 ref 保存对 DOM 元素的引用。在 React 发展过程中，出现过三种 ref 相关的引用模式：
>
> - String 类型（已不推荐使用）
> - 函数类型
> - { current : T }
>
> 目前最为推荐的是在类组件中使用 createRef，函数组件中使用 useRef 来创建 Ref。
>
> 当开发者调用 useRef 来创建 ref 时，在 mount 阶段，会创建一个 hook 对象，该 hook 对象的 memoizedState 存储的是 { current: initialValue } 对象，之后向外部返回了这个对象。在 update 阶段就是从 hook 对象的 memoizedState 拿到 { current: initialValue } 对象。
>
> ref 内部的工作流程整体上可以分为两个阶段：
>
> - render 阶段：标记 Ref flag，对应的内部函数为 markRef
> - commit 阶段：根据 Ref flag，执行 ref 相关的操作，对应的相关函数有 commitDetachRef、commitAttachRef
>
> 所谓 ref 的失控，本质是由于开发者通过 ref 操作了 DOM，而这一行为本身是应该由 React 来进行接管的，所以两者之间发生了冲突导致的。
>
> ref 失控的防治主要体现在两个方面：
>
> - 防：控制 ref 失控影像的范围，使 ref 失控造成的影响更容易被定位，例如使用 forwardRef
> - 治：从 ref 引用的数据结构入手，尽力避免可能引起失控的操作，例如使用 useImperativeHandle

# Update

> 面试题：说一说 React 中的 updateQueue

在 React 中，有许多触发状态更新的方法，比如：

- ReactDOM.createRoot
- this.setState
- this.forceUpdate
- useState dispatcher
- useReducer dispatcher

虽然这些方法执行的场景会有所不同，但是都可以接入同样的更新流程，原因是因为它们使用同一种数据结构来表示更新，这种数据结构就是 Update。



## Update 数据结构

在 React 中，更新实际上是存在优先级的，其心智模型有一些类似于“代码版本管理工具”。

举个例子，假设现在我们在开发一个软件，当前软件处于正常的迭代中，拥有 A、B、C 三个正常需求，此时突然来了一个紧急的线上 Bug，整体流程如下：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-07-063812.png" alt="image-20230307143812188" style="zoom:50%;" />

为了修复线上 Bug D，你需要先完成需求 A、B、C，之后才能进行 D 的修复，这样的设计实际上是不合理的。

有了代码版本管理工具之后，有紧急线上 Bug 需要修复时，可以先暂存当前分支的修改，在 master 分支修复 Bug D 并紧急上线：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-07-064157.png" alt="image-20230307144156888" style="zoom:50%;" />

当 Bug 修复完毕后，再正常的来迭代 A、B、C 需求，之后的迭代会基于 D 这个版本：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-07-064357.png" alt="image-20230307144357498" style="zoom:50%;" />



并发更新的 React 也拥有相似的能力，不同的 update 是有不同的优先级，高优先级的 update 能够中断低优先级的 update，当高优先级的 update 完成更新之后，后续的低优先级更新会在高优先级 update 更新后的 state 的基础上再来进行更新。



接下来我们来看一下 Update 的一个数据结构。

在前面我们说了在 React 中，有不同的触发更新的方法，不同的方法实际上对应了不同的组件：

- ReactDOM.createRoot 对应 HostRoot
- this.setState 对应 ClassComponent
- this.forceUpdate 对应 ClassComponent
- useState dispatcher 对应 FunctionComponent
- useReducer dispatcher 对应 FunctionComponent

不同的组件类型，所对应的 Update 的数据结构是不同的。

HostRoot 和 ClassComponent 这一类组件所对应的 Update 数据结构如下：

```js
function createUpdate(eventTime, lane){
  const update = {
    eventTime,
    lane,
		// 区分触发更新的场景
    tag: UpdateState,
    payload: null,
    // UI 渲染后触发的回调函数
    callback: null,
    next: null,
  };
  return update;
}
```

在上面的 Update 数据结构中，tag 字段是用于区分触发更新的场景的，选项包括：

- ReplaceState：代表在 ClassComponent 生命周期函数中直接改变 this.state
- UpdateState：默认情况，通过 ReactDOM.createRoot 或者 this.setState 触发更新
- CaptureUpdate：代表发生错误的情况下在 ClassComponent 或 HostRoot 中触发更新（比如通过 getDerivedStateFormError 方法）
- ForceUpdate：代表通过 this.forceUpdate  触发更新

接下来来看一下 FunctionComponent 所对应的 Update 数据结构：

```js
const update = {
  lane,
  action,
  // 优化策略相关字段
  hasEagerState: false,
  eagerState: null,
  next: null
}
```

在上面的数据结构中，有 hasEagerState 和 eagerState 这两个字段，它们和后面要介绍的 React 内部的性能优化策略（eagerState 策略）相关。



在 Update 数据结构中，有三个问题是需要考虑：

- 更新所承载的更新内容是什么

对于HostRoot以及类组件来讲，承载更新内容的字段为 payload 字段

```js
// HostRoot
ReactDOM.createRoot(rootEle).render(<App/>);                                   
// 对应 update
{
	payload : {
    // HostRoot 对应的 jsx，也就是 <App/> 对应的 jsx
  	element                                  
  },
  // 省略其他字段
}

// ClassComponent 情况1
this.setState({num : 1})
// 对应 update
{
  payload : {
    num: 1
  },
  // 省略其他字段
}

// ClassComponent 情况2
this.setState({num : num => num + 1})
// 对应 update
{
  payload : {
    num: num => num + 1
  },
  // 省略其他字段
}
```

对于函数组件来讲，承载更新内容的字段为 action 字段

```js
// FC 使用 useState 情况1
updateNum(1);
// 对应 update
{
  action : 1,
  // 省略其他字段
}

// FC 使用 useState 情况2
updateNum(num => num + 1);
// 对应 update
{
  action : num => num + 1,
  // 省略其他字段
}
```

- 更新的紧急程度：紧急程度是由 lane 字段来表示的
- 更新之间的顺序：通过 next 字段来指向下一个 update，从而形成一个链表。



## UpdateQueue

上面所介绍的 update 是计算 state 的最小单位，updateQueue 是由 update 组成的一个链表，updateQueue 的数据结构如下：

```js
const updateQueue = {
  baseState: null,
  firstBaseUpdate: null,
  lastBaseUpdate: null,
  shared: {
    pending: null
  }
}
```

- baseState：参与计算的初始 state，update 基于该 state 计算新的 state，可以类比为心智模型中的 master 分支。
- firstBaseUpdate 与 lastBaseUpdate：表示更新前该 FiberNode 中已保存的 update，以链表的形式串联起来。链表头部为 firstBaseUpdate，链表尾部为 lastBaseUpdate。
- shared.pending：触发更新后，产生的 update 会保存在 shared.pending 中形成**单向环状链表**。计算 state 时，该环状链表会被拆分并拼接在 lastBaseUpdate 后面。



举例说明，例如当前有一个 FiberNode 刚经历完 commit 阶段的渲染，该 FiberNode 上面有两个“由于优先级低，导致在上一轮 render 阶段并没有被处理的 update”，假设这两个 update 分别名为 u0 和 u1

```js
fiber.updateQueue.firstBaseUpdate = u0;
fiber.updateQueue.lastBaseUpdate = u1;
u0.next = u1;
```

那么假设在当前的 FiberNode 上面我们又触发了两次更新，分别产生了两个 update（u2 和 u3），新产生的 update 就会形成一个环状链表，shared.pending 就会指向这个环状链表，如下图所示：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-07-085309.png" alt="image-20230307165309276" style="zoom:50%;" />

之后进入新的一轮 render，在该 FiberNode 的 beginWork 中，shared.pending 所指向的环状链表就会被拆分，拆分之后接入到 baseUpdate 链表后面：

<img src="https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2023-03-07-085521.png" alt="image-20230307165521236" style="zoom:50%;" />

接下来就会遍历 updateQueue.baseUpdate，基于 updateQueue.baseState 来计算每个符合优先级条件的 update（这个过程有点类似于 Array.prototype.reduce），最终计算出最新的 state，该 state 被称之为 memoizedState。



因此我们总结一下，整个 state 的计算流程可以分为两步：

- 将 shared.pending 所指向的环状链表进行拆分并且和 baseUpdate 进行拼接，形成新的链表
- 遍历连接后的链表，根据 wipRootRenderLanes 来选定优先级，基于符合优先级条件的 update 来计算 state



## 真题解答

> 题目：面试题：说一说 React 中的 updateQueue
>
> 参考答案：
>
> update 是计算 state 的最小单位，一条 updateQueue 代表由 update 所组成的链表，其中几个重要的属性列举如下：
>
> - baseState：参与计算的初始 state，update 基于该 state 计算新的 state，可以类比为心智模型中的 master 分支。
> - firstBaseUpdate 与 lastBaseUpdate：表示更新前该 FiberNode 中已保存的 update，以链表的形式串联起来。链表头部为 firstBaseUpdate，链表尾部为 lastBaseUpdate。
> - shared.pending：触发更新后，产生的 update 会保存在 shared.pending 中形成单向环状链表。计算 state 时，该环状链表会被拆分并拼接在 lastBaseUpdate 后面。
>
> 整个 state 的计算流程可以分为两步：
>
> - 将 shared.pending 所指向的环状链表进行拆分并且和 baseUpdate 进行拼接，形成新的链表
> - 遍历连接后的链表，根据 wipRootRenderLanes 来选定优先级，基于符合优先级条件的 update 来计算 state

# React 中的事件

> 面试题：简述一下 React 中的事件是如何处理的？

在 React 中，有一套自己的事件系统，如果说 React 中的 FiberTree 这个数据结构是用来描述 UI 的，那么 React 里面的事件系统就是用来描述 FiberTree 和 UI 之间的的交互的。

对于 ReactDOM 宿主环境，这套事件系统由两个部分：

- 合成事件对象

SyntheticEvent （合成事件对象）这个是对浏览器原生事件对象的一层封装，兼容了主流的浏览器，同时拥有和浏览器原生事件相同的 API，例如 stopPropagation 和 preventDefault。SyntheticEvent 存在的目的就是为了消除不同浏览器在事件对象上面的一个差异。



- 模拟实现事件传播机制

利用事件委托的原理，React 会基于 FiberTree 来实现了事件的捕获、目标以及冒泡的过程（就类似于原生 DOM 的事件传递过程），并且在自己实现的这一套事件传播机制中还加入了许多新的特性，比如：

- 不同的事件对应了不同的优先级
- 定制事件名
  - 比如在 React 中统一采用 onXXX 的驼峰写法来绑定事件
- 定制事件的行为
  - 例如 onChange 的默认行为与原生的 oninput 是相同



React 事件系统需要考虑到很多边界情况，因此代码量是非常大的，我们这里通过书写一个简易版的事件系统来学习 React 事件系统的原理。

假设，现在我们有如下这一段 JSX 代码：

```jsx
const jsx = (
  <div onClick={(e) => console.log("click div")}>
    <h3>你好</h3>
    <button
      onClick={(e) => {
        // e.stopPropagation();
        console.log("click button");
      }}
    >
      点击
    </button>
  </div>
);
```

在上面的代码中，我们为外层的 div 以及内部的 button 都绑定了点击事件，默认情况下，点击 button 会打印出 click button、click div，如果打开 e.stopPropagation( )，那么就会阻止事件冒泡，只打印出 click button。

可以看出，React 内部的事件系统实现了“模拟实现事件传播机制”。

接下来我们自己来写一套简易版事件系统，绑定事件的方式改为 bindXXXX



## 实现 SyntheticEvent

SyntheticEvent 指的是合成事件对象，在 React 中的 SyntheticEvent 会包含很多的属性和方法，这里我们出于演示的目的，我们只实现一个阻止冒泡

```js
/**
 * 合成事件对象类
 */
class SyntheticEvent {
  constructor(e) {
    // 保存原生的事件对象
    this.nativeEvent = e;
  }
  // 合成事件对象需要提供一个和原生 DOM 同名的阻止冒泡的方法
  stopPropagation() {
    // 当开发者调用 stopPropagation 方法，将该合成事件对象的 _stopPropagation 设置为 true
    this._stopPropagation = true;
    if (this.nativeEvent.stopPropagation) {
      // 调用原生事件对象的 stopPropagation 方法来阻止冒泡
      this.nativeEvent.stopPropagation();
    }
  }
}
```

在上面的代码中，我们创建了一个 SyntheticEvent 类，这个类可以用来创建合成事件对象。内部保存了原生的事件对象，还提供了一个和原生 DOM 的事件对象同名的阻止冒泡的方法。



## 实现事件的传播机制

对于可以冒泡的事件，整个事件的传播机制实现步骤如下：

- 在根元素绑定“事件类型对应的事件回调”，所有子孙元素触发该类事件时最终会委托给根元素的事件回调函数来进行处理
- 寻找触发事件的 DOM 元素，找到对应的 FiberNode
- 收集从当前的 FiberNode 到 HostRootFiber 之间所有注册了该事件的回调函数
- 反向遍历并执行一遍收集的所有的回调函数（模拟捕获阶段的实现）
- 正向遍历并执行一遍收集的所有的回调函数（模拟冒泡阶段的实现）

首先我们通过 addEvent 来给根元素绑定事件，目前是为了使用事件委托

```js
/**
 * 该方法用于给根元素绑定事件
 * @param {*} container 根元素
 * @param {*} type 事件类型
 */
export const addEvent = (container, type) => {
  container.addEventListener(type, (e) => {
    // 进行事件的派发
    dispatchEvent(e, type.toUpperCase());
  });
};
```

接下来在入口中通过调用 addEvent 来绑定事件，如下：

```js
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(jsx);
// 进行根元素的事件绑定，换句话说，就是使用我们自己的事件系统
addEvent(document.getElementById("root"), "click");
```

在 addEvent 里面，调用 dispatchEvent 做事件的派发：

```js
/**
 *
 * @param {*} e 原生的事件对象
 * @param {*} type 事件类型，已经全部转为了大写，比如这里传递过来的是 CLICK
 */
const dispatchEvent = (e, type) => {
  // 实例化一个合成事件对象
  const se = new SyntheticEvent(e);
  // 拿到触发事件的元素
  const ele = e.target;
  let fiber;
  // 通过 DOM 元素找到对应的 FiberNode
  for (let prop in ele) {
    if (prop.toLocaleLowerCase().includes("fiber")) {
      fiber = ele[prop];
    }
  }
  // 找到对应的 fiberNode 之后，接下来我们需要收集路径中该事件类型所对应的所有的回调函数
  const paths = collectPaths(type, fiber);
  // 模拟捕获的实现
  triggerEventFlow(paths, type + "CAPTURE", se);
  // 模拟冒泡的实现
  // 首先需要判断是否阻止了冒泡，如果没有，那么我们只需要将 paths 进行反向再遍历执行一次即可
  if(!se._stopPropagation){
    triggerEventFlow(paths.reverse(), type, se);
  }
};
```

dispatchEvent 方法对应有如下的步骤：

- 实例化一个合成事件对象
- 找到对应的 FiberNode
- 收集从当前的 FiberNode 一直往上所有的该事件类型的回调函数
- 模拟捕获的实现
- 模拟冒泡的实现



## 收集路径中对应的事件处理函数

```js
/**
 * 该方法用于收集路径中所有 type 类型的事件回调函数
 * @param {*} type 事件类型
 * @param {*} begin FiberNode
 * @returns
 * [{
 *  CLICK : function(){...}
 * },{
 *  CLICK : function(){...}
 * }]
 */
const collectPaths = (type, begin) => {
  const paths = []; // 存放收集到所有的事件回调函数
  // 如果不是 HostRootFiber，就一直往上遍历
  while (begin.tag !== 3) {
    const { memoizedProps, tag } = begin;
    // 如果 tag 对应的值为 5，说明是 DOM 元素对应的 FiberNode
    if (tag === 5) {
      const eventName = "bind" + type; // bindCLICK
      // 接下来我们来看当前的节点是否有绑定事件
      if (memoizedProps && Object.keys(memoizedProps).includes(eventName)) {
        // 如果进入该 if，说明当前这个节点绑定了对应类型的事件
        // 需要进行收集，收集到 paths 数组里面
        const pathNode = {};
        pathNode[type] = memoizedProps[eventName];
        paths.push(pathNode);
      }
      begin = begin.return;
    }
  }
  return paths;
};
```

实现的思路就是从当前的 FiberNode 一直向上遍历，直到 HostRootFiber，收集遍历过程中 FiberNode.memoizedProps 属性所保存的对应的事件处理函数。

最终返回的 paths 数组保存的结构大致如下：

```js
[{
   CLICK : function(){...}
  },{
   CLICK : function(){...}
}]
```



## 捕获和冒泡的实现

由于我们是从目标元素的 FiberNode 向上遍历的，因此收集到的顺序：

 [  目标元素的事件回调，某个祖先元素的事件回调，某个更上层的祖先元素的事件回调 ]

因此要模拟捕获阶段的实现，我们就需要从后往前进行遍历并执行：

```js
/**
 *
 * @param {*} paths 收集到的事件回调函数的数组
 * @param {*} type 事件类型
 * @param {*} se 合成事件对象
 */
const triggerEventFlow = (paths, type, se) => {
  // 挨着挨着遍历这个数组，执行回调函数即可
  // 模拟捕获阶段的实现，所以需要从后往前遍历数组并执行回调
  for (let i = paths.length; i--; ) {
    const pathNode = paths[i];
    const callback = pathNode[type];
    if (callback) {
      // 存在回调函数，执行该回调
      callback.call(null, se);
    }
    if (se._stopPropagation) {
      // 说明在当前的事件回调函数中，开发者阻止继续往上冒泡
      break;
    }
  }
};
```

在执行事件的回调的时候，每一次执行需要检验 _stopPropagation 属性是否为 true，如果为true，说明当前的事件回调函数中阻止了事件冒泡，因此我们应当停止后续的遍历。

如果是模拟冒泡阶段，只需要将 paths 进行反向再遍历一次并执行即可：

```js
// 模拟冒泡的实现
// 首先需要判断是否阻止了冒泡，如果没有，那么我们只需要将 paths 进行反向再遍历执行一次即可
if(!se._stopPropagation){
  triggerEventFlow(paths.reverse(), type, se);
}
```



至此，我们就实现了一个简易版的 React 事件系统。



## 真题解答

> 题目：简述一下 React 中的事件是如何处理的？
>
> 参考答案：
>
> 在 React 中，有一套自己的事件系统，如果说 React 用 FiberTree 这一数据结构是用来描述 UI 的话，那么事件系统则是基于 FiberTree 来描述和 UI 之间的交互。
>
> 对于 ReactDOM 宿主环境，这套事件系统由两个部分组成：
>
> （1）SyntheticEvent（合成事件对象）
>
> SyntheticEvent 是对浏览器原生事件对象的一层封装，兼容主流浏览器，同时拥有与浏览器原生事件相同的 API，例如 stopPropagation 和 preventDefault。SyntheticEvent 存在的目的是为了消除不同浏览器在 “事件对象” 间的差异。
>
> （2）模拟实现事件传播机制
>
> 利用事件委托的原理，React 基于 FiberTree 实现了事件的捕获、目标、冒泡的流程（类似于原生事件在 DOM 元素中传递的流程），并且在这套事件传播机制中加入了许多新的特性，例如：
>
> - 不同事件对应了不同的优先级
> - 定制事件名
>  - 例如事件统一采用如 “onXXX” 的驼峰写法
> - 定制事件行为
>  - 例如 onChange 的默认行为与原生 oninput 相同