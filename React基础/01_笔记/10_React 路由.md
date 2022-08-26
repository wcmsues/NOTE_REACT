# 10_React 路由

> 📢 大家好，我是Milo
>
> 📢 这篇文章是学习 React 中 React 路由的学习笔记
>

## 引言

在我们之前写的页面当中，用我们的惯用思维去思考的话，可能会需要写很多的页面，例如做一个 tab 栏，我们可能会想每个选项都要对应一个 HTML 文件，这样会很麻烦，甚至不友好，我们把这种称为 MPA 也叫多页面应用。

## 🍕 一、什么是SPA？

### 1 SPA

而为了减少上述情况，我们还有另一种应用叫做 单页Web应用（single aage web application， SPA）

它比传统的 Web 应用程序更快，因为它们在 Web 浏览器本身而不是在服务器上执行逻辑。在初始页面加载后，**只有数据来回发送**，而不是整个 HTML，这会降低带宽。它们可以独立请求标记和数据，并直接在浏览器中呈现页面

### 2 总结

1. 单页Web应用（single aage web application， SPA）
2. 整个应用只有**一个完整的页面**
3. 点击页面中的链接**不会刷新**页面，只会做页面的**局部更新**
4. 数据都需要通过ajax请求获取，并在前端异步展现

## 🍔 二、什么是路由？

### 1 路由

路由是根据不同的 URL 地址展示不同的内容或页面

在 SPA 应用中，大部分页面结果不改变，只改变部分内容的使用

1. 一个路由就是一个映射关系（`key:value`）
2. `key` 为路径，`value` 可能是 `function` 或 `component`

### 2 路由分类

#### 2.1 后端路由（了解即可）

1. 理解：`value` 是 `function`，用来处理客户端提交的请求。
2. 注册路由：`router.get(path, function(req,res))`
3. 工作过程：当 `node` 接收到一个请求时，根据请求路径找到匹配的路由，调用路由中的函数来处理请求，返回响应数据

之前server.js本地服务器就使用了后端路由

![jtqs4x.png](https://s1.ax1x.com/2022/07/05/jtqs4x.png)

#### 2.2 前端路由

1. 浏览器端路由，`value` 是 `component` ，用于展示页面内容。
2. 注册路由：`<Route path="/test" component={Test}>`
3. 工作过程：当浏览器的 `path` 变为 `/test` 时，当前路由组件就变为 `Test` 组件

#### 2.3 前端路由的优缺点

**优点**：用户体验好，不需要每次都从服务器全部获取整个 HTML，快速展现给用户

**缺点**：

1. SPA 无法记住之前页面滚动的位置，再次回到页面时无法记住滚动的位置
2. 使用浏览器的前进和后退键会重新请求，没有合理利用缓存

## 🍟 三、 路由的原理

### 1 原理

前端路由的主要依靠的是 BOM 下的 history ，也就是浏览器的历史记录

> history 是 BOM 对象下的一个属性，在 H5 中新增了一些操作 history 的 API

浏览器的历史记录就类似于一个栈的数据结构，前进就相当于入栈，后退就相当于出栈

并且历史记录上可以采用 `listen` 来监听请求路由的改变，从而判断是否改变路径

### 2 例如

1. 点击导航链接，引起路径变化

2. 被前端路由器监测到，进行匹配组件，从而展示

### 3 History 对象 API

在 H5 中新增了 `createBrowserHistory` 的 API ，用于创建一个 history 栈，允许我们手动操作浏览器的历史记录（兼容性问题，某些浏览器不允许）

新增 API：`pushState` ，`replaceState`，原理类似于 Hash 实现。 用 H5 实现，单页路由的 URL 不会多出一个 `#` 号，这样会更加的美观

更多内容可以点击[Histroy对象](https://miloreact.github.io/pages/js_basic/bom/history.html)查看

## 🌭 四、路由的基本使用

react-router有三个库，一个专门给web人员使用，一个给native，还有一个any两者都行

这react-router-dom就是专门给web人员使用的库

![jNoQ6H.png](https://s1.ax1x.com/2022/07/05/jNoQ6H.png)

> react-router-dom 专门给 web 人员使用的库

1. react 的一个插件库
2. 专门用来实现一个 SPA 应用
3. 给予 react 的项目基本都会用到此库

### 1 划分布局

首先我们要明确好页面的布局 ，分好导航区、展示区

![jUDCjO.png](https://s1.ax1x.com/2022/07/06/jUDCjO.png)

其中红色区域的header和导航区是一直存在的，而蓝色内容展示区需要切换显示

![jUrXf1.png](https://s1.ax1x.com/2022/07/06/jUrXf1.png)

### 2 引入所需文件

要引入 `react-router-dom` 库，暴露一些属性 `Link、BrowserRouter、Route`

```jsx
import { Link, BrowserRouter, Route } from 'react-router-dom'
```

要引入 `Home` 组件和 `About` 组件

```jsx
import Home from './components/Home'
import About from './components/About'
```

### 3 导航区 Link 标签

在 `APP` 组件中，导航区的 `<a>` 标签改为 `<Link>` 标签

```js
// 在React中靠路由链接实现切换组件
<Link className="list-group-item" to="/about">About</Link>
<Link className="list-group-item" to="/home">Home</Link>
```

⚠️注意：

1. `<Link>` 将 `<a>` 中的 `herf` 换成了 `to`
2. `to` 里面不写大小写因为不识别大小写
3. `to` 里不写 `./XXX.html`，而是写 `/XXX`

### 4 内容展示区 Route 标签

内容展示区现实的内容要放在 `Home` 组件和 `About` 组件中

```jsx
// Home/index.jsx
<h3>我是Home的内容</h3>
// About/index.jsx
<h3>我是About的内容</h3>
```

而在 `APP` 组件中，我们需要用 `Route` 标签，来进行路径的匹配，从而实现不同路径的组件切换

```jsx
// 注册路由
<Route path="/about" component={About} />
<Route path="/home" component={Home} />
```

当导航区的`<Link>` 标签被触发后，就会来进行路径匹配，切换不同路径的组件

### 5 路由器标签 BrowserRouter

这样之后我们还需要一步，加个路由器，在上面我们写了两组路由，同时还会报错指示我们需要添加 `Router` 来解决错误，这就是需要我们添加路由器来管理路由

⚠️注意：如果我们在 `Link` 和 `Route` 中分别用路由器管理，那这样是实现不了的，只有在一个路由器的管理下才能进行页面的跳转工作。

#### 方法一（不推荐）

我们可以在 `App` 组件里 `Link` 和 `Route` 标签的外层标签采用 `BrowserRouter` 包裹

![jaJnbt.png](https://s1.ax1x.com/2022/07/06/jaJnbt.png)

⚠️注意：但是这样当我们的路由过多向外扩展时，我们要不停的更改标签包裹的位置

#### 方法二（推荐方式）

我们回到 `src` 目录下的 `index.js` 文件，将整个 `<App/>` 组件标签用 `BrowserRouter` 标签去包裹，这样整个 `App` 组件都在**一个路由器**的管理下

![jaJIRe.png](https://s1.ax1x.com/2022/07/06/jaJIRe.png)

### 总结

1. 明确好界面中的导航区、展示区
2. 导航区的 `a` 标签改为 `Link` 标签，`<Link to="/xxx">Demo</Link>`
3. 展示区写 `Route` 标签进行路径的匹配，`<Route path='/xxx' compinent={Demo} />`
4. `<App>` 的最外侧包裹一个 `<BrowserRouter>` 或 `<HashRouter>`


---

效果展示

![](https://s3.bmp.ovh/imgs/2022/07/06/983f1e4a56dabcd4.gif)



## 🍿 五、路由组件和一般组件

在我们前面的内容中，我们是把 `Home` 和 `About` 当成是一般组件来使用，我们将它们写在了 `src` 目录下的 `components` 文件夹下，但引入它们的时候我们是通过 `{Home}` 来引用的，而不是 `<Demo/>` 。

这就涉及一般组件和路由组件的差异，其实 `Home` 和 `About` 是路由组件，要改一下

### 1 写法不同

首先它们的写法不同

**一般组件**：`<Demo/>`

**路由组件**：`<Route path="/demo" component={Demo}/>`

比如我们将 `Header` 部分写成组件形式，在 `App` 中写法为

```jsx
<Header/>
```

而 `Home` 和 `About` 在 `App` 中写法为

```jsx
<Route path="/about" component={About} />
<Route path="/home" component={Home} />
```

### 2 存放位置不同

同时为了规范我们的书写，一般将路由组件放在 `pages` 文件夹中，路由组件放在 `components` 

```
src
├─ App.jsx
├─ components
│  ├─ Header
│  │  └─ index.jsx
├─ pages
│  ├─ Home
│  │  └─ index.jsx
│  └─ About
│     └─ index.jsx
└─index.js
```

这里我们将 `Home` 和 `About` 放到 `pages` 中，将 `Header` 放在 `component` 中

### 3 接受到的props不同

最重要的一点就是它们接收到的 `props` 不同

在一般组件中，如果我们不进行传递，就不会收到值；如果传递什么，就能收到什么

```jsx
// App.jsx
<Header a={1}/>
// Header/index.jsx
console.log('Header组件收到的props是',this.props)
```

而对于路由组件而言，它会接收到 3 个固定属性 `history` 、`location` 以及 `match` 

```jsx
// About/index.jsx
console.log('About组件收到的props是',this.props)
```

两者收到的参数：

![jaTZbd.png](https://s1.ax1x.com/2022/07/06/jaTZbd.png)

### 总结

1. 写法不同，一般组件：`<Demo/>`，路由组件：`<Route path="/demo" component={Demo}/>`

2. 存放位置不同，一般组件：`components`，路由组件：`pages`

3. 接受到的props不同

   一般组件：写组件标签时传递了什么，就能收到什么

   路由组件：接受到三个固定属性，`history`、`location`、`match`

```js
history:
     go: ƒ go(n)
     goBack: ƒ goBack()
     goForward: ƒ goForward()
     push: ƒ push(path, state)
     replace: ƒ replace(path, state)
location:
     pathname: "/about"
     search: ""
     state: undefined
match:
     params: {}
     path: "/about"
     url: "/about"
```

## 🍛 六、NavLink 标签

`NavLink` 标签是和 `Link` 标签作用相同的，但是它又比 `Link` 更加强大。

### 1 增加高亮效果

在前面的 demo 展示中，你可能会发现点击的按钮并没有出现高亮的效果，正常情况下我们给标签多添加一个 `active`  的类就可以实现高亮的效果

```jsx
<Link className="list-group-item active" to="/about">About</Link>
```

![ja7wTA.png](https://s1.ax1x.com/2022/07/06/ja7wTA.png)

而 `NavLink` 标签正可以帮助我们实现这一步

当我们选中某个 `NavLink` 标签时，就会自动的在类上添加一个 `active` 属性

```jsx
<NavLink className="list-group-item" to="/about">About</NavLink>
```

![](https://s3.bmp.ovh/imgs/2022/07/06/1779a64190b40255.gif)

### 2 更换高亮样式

上图可以看到右侧的元素类名在不断的切换，当然 NavLink 标签是默认的添加上 `active` 类，我们也可以改变它，在标签上添加一个属性 `activeClassName` 

例如 `activeClassName="aaa"` 在触发这个 NavLink 时，会自动添加一个 `aaa` 类

这里我们改成自定义的 `milo` 类

```jsx
<NavLink activeClassName="milo" className="list-group-item" to="/about">About</NavLink>
```

在 `public` 的 `index.html` 添加 `milo` 类 style

```html
<style>
    .milo{
        background-color: rgb(209,137,4) !important;
        color: white !important;
    }
</style>
```

⚠️注意：由于 `index.html` 引入了 `bootstrap.css`，它的优先级较高，所以自定义的 style 要加上 `!important`

![](https://s3.bmp.ovh/imgs/2022/07/06/452d98eaa4bd38c4.gif)

## 🥩 七、NavLink 封装

在上面的 `NavLink` 标签中，我们可以发现我们每次都需要重复的去写 `className` 或者是 `activeClassName` ，这并不是一个很好的情况，代码过于冗余。

那我们是不是可以想想办法封装一下它们呢？

我们可以采用 `MyNavLink` 组件，对 `NavLink` 进行封装

### 1 新建 MyNavLink 组件

首先我们需要新建一个 `MyNavLink` 组件

内容就是对 `NavLink` 进行封装

```jsx
<NavLink className="list-group-item" {...this.props} />
```

`{...this.props}` 用来接受在 `App` 组件中调用 `MyNavLink` 组件时，传递的所有参数

### 2 引入所需文件

在 `MyNavLink` 组件中引入 `react-router-dom` 库

```jsx
import { NavLink } from 'react-router-dom'
```

在 `App` 组件中引入 `MyNavLink` 组件

```jsx
import MyNavLink from './components/MyNavLink' // MyNavLink是一般组件
```

### 3 调用并传递参数

在 `App` 组件中调用 `MyNavLink` 组件时，直接写

```jsx
<MyNavLink to="/home">home</MyNavLink>
```

⚠️注意：我们在标签体内写的内容都会成为一个 `children` 属性，因此我们在调用 `MyNavLink` 时，在标签体中写的内容，都会成为 `props` 中的一部分，从而能够实现参数传递

---

以上就是本节关于 React 路由的相关知识！

> 非常感谢您的阅读，欢迎提出你的意见，有什么问题欢迎指出，谢谢！🎈