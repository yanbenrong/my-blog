---
title: React18学习笔记
description: React18学习笔记
date: 2023-07-21
tags:
  - React18学习笔记
---

# React18学习笔记

## class组件中this指向

由于js中this的特殊性，事件处理函数中的this并不会指向当前的组件，这就需要自行对this进行指向修正。

- 将事件处理函数修改为箭头函数
- 将事件绑定修改为箭头函数
- 使用bind方法强制绑定this的指向

## class组件 setState

*setState*，它对状态的改变，**可能**是异步的。

> 如果改变状态的代码处于某个 *HTML* 元素的事件中，则其是异步的，否则是同步

如果在事件处理函数里面想拿到 *setState* 执行后的数据，可以提前使用一个变量来存储计算结果，或者使用 *setState* 的第二个参数，它是一个函数，这个函数会在 *state* 更新后被调用。

最佳实践：

1. 把所有的 *setState* 当作是异步的

2. 永远不要信任 *setState* 调用之后的状态

3. 如果要使用改变之后的状态，需要使用回调函数（*setState* 的第二个参数）

   ```js
   xxx(){
       this.setState({num: this.state.num + 1},()=>{
           // 回调中就可以拿到修改后的状态
       })
   }
   ```

4. 如果新的状态要根据之前的状态进行运算，使用函数的方式改变状态（*setState* 的第一个函数）

   ```JS
   xxx(){
       this.setState((cur)=>({
           num: cur.num + 1
       }))
   }
   ```

*React* 会对异步的 *setState* 进行优化，将多次 *setState* 进行合并（将多次状态改变完成后，再统一对 *state* 进行改变，然后触发 *render*）

## 受控组件与非受控组件

非受控意思就是 **在需要时从元素中“提取”值**

受控意思是 **将表单中的控件和状态进行绑定，之后都是针对状态进行操作**

## useEffect

需要注意的是 useEffect第一个参数函数中的返回值 是执行清理工作的，

 该函数会在下一次渲染之后，但是在执行副作用操作之前执行。

```js
useEffect(()=>{
   // xxxx 
   return ()=>{
       //  该函数会在下一次渲染之后，但是在每次执行副作用操作之前执行。
   }
},[])
```

# React Router v6

官网地址 **<https://reactrouter.com/en/main>**

### 组件

- BrowserRouter：整个前端路由以 history 模式开始，包裹根组件
- HashRouter：整个前端路由以 hash 模式开始，包裹根组件
- Routes：类似于 v5 版本的 Switch，主要是提供一个上下文环境
- Route：在 Route 组件中书写你对应的路由，以及路由所对应的组件
  - path：匹配的路由
  - element：匹配上该路由时，要渲染的组件
- Navigate：导航组件，类似于 useNavigate 的返回值函数，只不过这是一个组件
- NavLink：类似于 Link，最终和 Link 一样，会被渲染为 a 标签，注意它和 Link 的区别，实际上就是当前链接，会有一个名为 active 的激活样式，所以一般用于做顶部或者左侧导航栏的跳转

### *Hooks*

- useLocation：获取到 location 对象，获取到 location 对象后，我们可以获取 state 属性，这往往是其他路由跳转过来的时候，会在 state 里面传递额外的数据
- useNavigate：调用之后会返回一个函数，通过该函数可以做跳转。
- useParams：获取动态参数

## 补充内容

### *useRoutes*

使用示例如下：

```
function Router(props) {
    return useRoutes([
        {
            path: "/home",
            element: <Home />,
        },
        {
            path: "/about",
            element: <About />,
        },
        {
            path: "/add",
            element: <AddOrEdit />,
        },
        {
            path: "/detail/:id",
            element: <Detail />,
        },
        {
            path: "/edit/:id",
            element: <AddOrEdit />,
        },
        {
            path: "/",
            element: <Navigate replace to="/home" />
        }
    ]);
}

export default Router;


```

**使用**

```js
import Router from "./router";

.......
<Router />
```

### 嵌套路由

直接在 useRoutes 进行 chilren 属性的配置即可，类似于 vue-router，children 对应的是一个数组，数组里面是一个一个路由对象

```
 {
   path: "/about",
     element: <About />,
     children : [
         {
           path : "email",
           element : <Email/>
         },
         {
           path : "tel",
           element : <Tel/>
         },
         {
           path : "",
           element: <Navigate replace to="email" />
         }
       ]
 },
```

之后，使用 Outlet 组件，该组件表示匹配上的子路由组件渲染的位置。

```js
import { Outlet } from "react-router-dom"


function About() {
    return (
        <div className="about container">
            <Outlet />
        </div>
    );
}

export default About;
```

# css模块化

 *CSS* 文件名包含 *module*，格式为 *xxx.module.css*，该 *CSS* 是一个局部的 *CSS* 样式文件，类似于 *vue* 组件里面的 **scoped**。

### 在className中 书写多个模块化类名

```js
方法一：
className={styles.firstClass + ' ' + styles.secondClass}
方法二：
className={[styles.firstClass, styles.secondClass].join(' ')}
```

# 配置请求代理转发

在 *src* 目录下面新建一个 *setupProxy* 的文件，在该文件中进行请求转发的配置

在使用的时候，还需要安装一个插件 *http-proxy-middleware*

# 渲染html内容

vue中使用的是 v-html，

react中可以使用 dangerouslySetInnerHTML。

```js
 <div dangerouslySetInnerHTML={{ __html: captcha }}></div>
```

# 修改打包后的目录

项目中会遇到静态资源以 *static*，又配置了请求转发**/static**，但是 *create-react-app*（基于 *webpack*）默认在打包应用的时候，也会将打包好的资源放置到 *static* 目录下，导致在加载打包好后的资源时，也会进行请求转发，从而报错。

我们需要做的是修改打包好后的目录。首先运行下面的命令：

```js
npm run eject
```

> 注意：弹射的时候要求 *git* 仓库不能有未提交的文件

弹射出来后，会多出来很多隐藏文件，我们就可以修改对应的配置，但是会有一个关于 *Babel* 的错误，最快的解决方案就是在 *package.json* 中删除如下的配置：

```js
"eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
},
```

接下来，在弹射出来的配置文件中，我们就可以修改 *webpack* 的打包配置：

*config/webpack.config.js* 的 *output* 对应的配置

```
 filename: isEnvProduction
        ? 'assets/js/[name].[contenthash:8].js'
        : isEnvDevelopment && 'assets/js/bundle.js',
      // There are also additional JS chunk files if you use code splitting.
      chunkFilename: isEnvProduction
        ? 'assets/js/[name].[contenthash:8].chunk.js'
        : isEnvDevelopment && 'assets/js/[name].chunk.js',
      assetModuleFilename: 'assets/media/[name].[hash][ext]',
```

# *redux* 中异步获取数据填充到状态仓库

一种方式，是通过 *action* 来派发一个 *reducer* 从而实现状态填充。

```
export const getStuListAsync = createAsyncThunk(
  "stu/getStuListAsync",
  async (_, thunkApi) => {
    // 发送 ajax 请求
    const response = await getStuListApi();
    // 派发 action
    thunkApi.dispatch(initStuList(response.data));
  }
);
```

也可以使用 *redux-toolkit* 官网所示例的方式：

```
export const getTypeList = createAsyncThunk(
    "type/getTypeList",
    async ()=>{
        const response = await getType();
        // 填充返回的数据到状态仓库
        return response.data;
    }
);

// ....

// 专门处理异步的 reducer
extraReducers : {
  // 这里就会有三种状态
  [getTypeList.fulfilled] : (state, { payload }) => {
    state.typeList = payload;
  }
}
```

# npm i @toast-ui/react-editor -S 安装报错

```js
npm ERR! Could not resolve dependency:
npm ERR! peer react@"^17.0.1" from @toast-ui/react-editor@3.2.3
```

类似于这种报错,是由于 该插件内部package.json中声明的对于react版本,依赖的是 react17版本, 但是目前版本为react18 所以安装依赖错误.

解决方法: 直接强制安装

```cmd
npm i @toast-ui/react-editor -S --force
```

# Dva

## 基本使用 示例

```js

// 定义model 示例：
import { deleteAdmin, getAdmin } from '../services/admin';

export default {
  namespace: 'admin',
  state: {
    adminList: [],
    adminInfo: null,
  },
  // 发起异步请求
  effects: {
    *_initAdminList(_, { call, put }) {
      const res = yield call(getAdmin);
      yield put({ type: 'initAdminList', payload: res.data });
    },
    *_deleteAdmin({ payload }, { call, put }) {
      yield call(deleteAdmin, payload.id);
      yield put({ type: '_initAdminList' });
    },
  },
  // 用来处理修改数据的逻辑（同步，不能请求后端
  reducers: {
    initAdminList(state, { payload }) {
      return {
        ...state,
        adminList: payload,
      };
    },
  },
};

// dispatch 派发action 更新 示例：
 const deleteClick = (id) => {
    dispatch({
      type: 'admin/_deleteAdmin',
      payload: {
        id,
      },
    });
  };

```

## dva使用redux-persist 数据持久化

在app.ts中 配置umi的运行时配置：

```js
import { persistReducer, persistStore } from 'redux-persist';
import storage from 'redux-persist/lib/storage';

const persistConfig = {
  key: 'root',
  storage,
};
const persistEnhancer =
  () =>
  (createStore: any) =>
  (reducer: any, initialState: any, enhancer: any) => {
    const store = createStore(
      persistReducer(persistConfig, reducer),
      initialState,
      enhancer,
    );
    const persist = persistStore(store);
    return { ...store, persist };
  };

  
// 运行时配置
export const dva = {
  config: {
    extraEnhancers: [persistEnhancer()],
  },
};
```
