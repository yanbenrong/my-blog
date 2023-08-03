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