# 11_React 路由传参

> 📢 大家好，我是Milo
>
> 📢 这篇文章是学习 React 中 React 路由的学习笔记
>

## 引言

在上一篇中，我们学习了 React 中使用路由技术，以及如何使用 `MyNavLink` 去优化使用路由时的代码冗余的情况。

这一节我们继续上一篇 React 路由进行一些补充

## 🍈 一、 Switch 解决相同路径问题

首先我们看一段这样的代码

```jsx
<Route path="/home" component={Home}></Route>
<Route path="/about" component={About}></Route>
<Route path="/about" component={About}></Route>
```

这是两个路由组件，在2，3行中，我们同时使用了相同的路径 `/about` 

![image-20210903075753268](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/image-20210903075753268.png)

我们发现它出现了两个 `about` 组件的内容，那这是为什么呢？

其实是因为，`Route` 的机制，当匹配上了第一个 `/about` 组件后，它还会继续向下匹配，因此会出现两个 About 组件，这时我们可以采用 `Switch` 组件进行包裹

在使用 `Switch` 时，我们需要先从 `react-router-dom` 中暴露出 `Switch` 组件

```jsx
import { Route,Switch } from 'react-router-dom'
```

用 `Switch` 组件包裹路由们

```jsx
<Switch>
    <Route path="/home" component={Home}></Route>
    <Route path="/about" component={About}></Route>
    <Route path="/about" component={About}></Route>
</Switch>
```

这样我们就能成功的解决掉这个问题了

## 🥟 二、 解决多级路由样式丢失的问题

⚠️注意：当我们将路径改写成 `path="/milo/about"` 这样的形式时，如果我们强制刷新页面，页面的 CSS 样式会消失。

```jsx
{/* 在React中靠路由链接实现切换组件 */}
<MyNavLink to="/milo/home">Home</MyNavLink>
<MyNavLink to="/milo/about">About</MyNavLink>
{/* 注册路由 */}
<Switch>
  <Route path="/milo/about" component={About} />
  <Route path="/milo/home" component={Home} />
</Switch>
```

![](https://s3.bmp.ovh/imgs/2022/07/07/d05c6f32f8481ef6.gif)

这是因为，我们在 `src` 下的 `index.html` 中，引入样式文件时用的是相对路径

```jsx
<link rel="stylesheet" href="./css/bootstrap.css">
```

当我们使用二级路由的时候，会使得请求的路径发生改变，浏览器会向 `localhost:3000/milo` 下请求 css 样式资源，这并不是我们想要的，因为我们的样式存放于公共文件下的 CSS 文件夹中。

### 1 将样式引入的路径改成绝对路径

在 `src` 下的 `index.html` 中，引入样式文件时用绝对路径

```jsx
<!-- %PUBLIC_URL%代表public文件夹的路径 -->
<link rel="stylesheet" href="%PUBLIC_URL%/css/bootstrap.css">
```

### 2 引入样式文件时不带.

在 `src` 下的 `index.html` 中，引入样式文件时不带 `.`

```jsx
<link rel="stylesheet" href="/css/bootstrap.css">
```

### 3 使用 HashRouter

之前我们在 `src` 目录下的 `index.js` 文件，将整个 `<App/>` 组件标签用 `BrowserRouter` 标签去包裹

这里我们换成 `HashRouter`  标签去包裹即可

```js
// index.js
<HashRouter>
    <App/>
</HashRouter>
```

### 总结

我们有3种方法，可以解决多级路由样式丢失的问题

1. 将样式引入的路径改成绝对路径（React推荐使用）
2. 引入样式文件时不带 `.` （常用）
3. 使用 HashRouter（用的少）

在React中我们一般采用**第一种方式**去解决

## 🍑 三、路由的精准匹配和模糊匹配

路由的匹配有两种形式，一种是精准匹配一种是模糊匹配，React 中默认开启的是模糊匹配

模糊匹配可以理解为，在匹配路由时，只要有匹配到的就好了

精准匹配就是，两者必须相同

### 1 模糊匹配

默认使用的是模糊匹配：【输入的路径】必须包含【要匹配的路径】，且顺序要一致

一个模糊匹配的小例子：

```jsx
{/* 在React中靠路由链接实现切换组件 */}
<MyNavLink to = "/home/a/b" >Home</MyNavLink>
```

这个标签输入的路由，我们可以拆分成 `home => a => b`，将会根据这个先后顺序匹配路由

```jsx
{/* 注册路由 */}
<Route path="/home"component={Home}/>
```

模糊匹配就可以匹配到上面的注册路由，因为【输入的路径 `/home/a/b` 】包含【要匹配的路径 `/home` 】

当要匹配的路由改成下面这样时，就会失败。

```jsx
<Route path="/a" component={Home}/>
```

⚠️Milo的理解：模糊匹配遵循最左前缀法则，它会按照第一个来匹配，如果第一个没有匹配上，那就会失败，这里的 `/a` 和 `/home` 没有匹配上，就不会继续往后找 `/home/a` 

### 2 精准匹配

当注册路由时，用 `exact` 来开启精准匹配（一般不开启）

```jsx
<Route exact={true}  path="/home" component={Home}/>
```

当我们开启了精准匹配后，需要完全一样的值才能匹配成功

还有简写方式

```jsx
<Route exact path="/home" component={Home}/>
```

### 总结

1. 默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）
2. 开启严格匹配：`<Route exact={true}  path="/home" component={Home}/>`
3. 严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由

## 🍋 四、重定向路由

在我们写好了这些之后，我们会发现，我们需要点击任意一个按钮，才会去匹配一个组件，这并不是我们想要的，我们想要页面一加载上来，默认的就能匹配到一个组件。

### Redirecrt

这个时候我们就需要用 `Redirecrt` 进行默认匹配了。

```js
 {/* 注册路由 */}
<Switch>
  <Route path="/about" component={About} />
  <Route path="/home" component={Home} />
  <Redirect to="/about" />
</Switch>
```

一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由 `/about` 

这里当我们请求3000端口时，就会重定向到 `/about` 这样就能够实现我们想要的效果了

![](https://s3.bmp.ovh/imgs/2022/07/07/c53f96d04894cd6e.gif)

### 总结

1. 一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由
2. 使用方式：`<Redirect to="/about" />`

## 🍓 五、嵌套路由

嵌套路由也就是我们前面有提及的多级路由，嵌套路由包括了二级、三级...还有很多级路由，比如当我们需要在一个路由组件中添加两个路由组件，就是二级路由

### 1 划分布局

首先我们要明确好页面的布局 ，分好导航区、展示区

![j0AEUs.png](https://s1.ax1x.com/2022/07/07/j0AEUs.png)

这里我们需要在 `Home` 组件的内容展示区内添加新的导航区和内容展示区

![j0EPRx.png](https://s1.ax1x.com/2022/07/07/j0EPRx.png)

### 2 引入所需文件

要引入 `react-router-dom` 库，暴露一些属性 `Switch、Route、Redirect`

```jsx
import {Route,Switch,Redirect} from 'react-router-dom'
```

创建 `News` 组件和 `Message` 组件，这里我们将 `News` 和 `Message` 放到 `Home` 中

```
src
├─ App.jsx
├─ components
├─ pages
│  ├─ Home
│  │  ├─ index.jsx
│  │  ├─ News
│  │  │  └─ index.jsx
│  │  └─ Message
│  │     └─ index.jsx
│  └─ About
└─index.js
```

引入 `News` 组件、 `Message` 组件和 `MyNavLink` 组件

```jsx
import News from './News'
import Message from './Message'
import MyNavLink from '../../components/MyNavLink'
```

### 3 导航区 MyNavLink标签

在 `Home` 组件中，导航区的 `<a>` 标签改为 `<MyNavLink>` 标签

```jsx
// 在React中靠路由链接实现切换组件
<MyNavLink to="/home/news">News</MyNavLink>
<MyNavLink to="/home/message">Message</MyNavLink>
```

⚠️注意：在这里我们需要使用嵌套路由的方式，才能完成匹配，匹配子路由时要写上父路由的 `path` 值

### 4 内容展示区 Route 标签

内容展示区的内容要放在  `News` 组件和 `Message` 组件中

```jsx
// News/index.jsx
<ul>
    <li>news001</li>
    <li>news002</li>
    <li>news003</li>
</ul>
// Message/index.jsx
<div>
  <ul>
    <li>
      <a href="/message1">message001</a>&nbsp;&nbsp;
    </li>
    <li>
      <a href="/message2">message002</a>&nbsp;&nbsp;
    </li>
    <li>
      <a href="/message/3">message003</a>&nbsp;&nbsp;
    </li>
  </ul>
</div>
```

而在 `Home` 组件中，我们需要用 `Route` 标签，来进行路径的匹配，从而实现不同路径的组件切换

```jsx
{/* 注册路由 */}
<Switch>
  <Route path="/home/news" component={News} />
  <Route path="/home/message" component={Message} />
  <Redirect to="/home/news" />
</Switch>
```

⚠️注意：在这里我们需要使用嵌套路由的方式，才能完成匹配，注册子路由时要写上父路由的 `path` 值

这里我们套上了 `<Switch>` 标签解决相同路径问题

这里我们用 `Redirecrt` 对 `News` 默认匹配，它先高亮

### 5 路由的匹配按照注册路由的顺序执行

React 中的路由注册是有顺序的。当我们匹配时，由于 `Home` 组件先注册路由，会先去找 `/home` 进行匹配，由于是模糊匹配，匹配成功。

然后会在 `Home` 组件里，再匹配输入的路由，从而找到 `/home/news` 进行匹配，因此找到 `News` 组件，进行匹配渲。

> 如果开启精确匹配的话，第一步的 `/home/news` 匹配 `/home` 就会卡住不动，这个时候就不会显示有用的东西了！

### 总结

1. 注册子路由时要写上父路由的 `path` 值
2. 路由的匹配是按照注册路由的顺序进行的

---

最终效果：

![](https://s3.bmp.ovh/imgs/2022/07/07/cdbb5f7c9e67d04a.gif)

## 🍟 六、传递 params 参数

我们需要实现的效果是，点击消息列表，展示出消息的详细内容

这个案例实现的方法有三种，第一种就是传递 params 参数，由于我们所显示的数据都是从数据集中取出来的，因此我们需要有数据的传输给 Detail 组件

### 1 划分布局

首先我们要明确好页面的布局 ，分好导航区、展示区

![](https://s3.bmp.ovh/imgs/2022/07/08/e5e294213b5d7793.png)

这里我们需要在 `Message` 组件的内容展示区内添加新的导航区和内容展示区

![](https://s3.bmp.ovh/imgs/2022/07/08/ac569ea0d0001423.png)

### 2 引入所需文件

要引入 `react-router-dom` 库，暴露一些属性 `Link,Route`

```jsx
import {Link,Route} from 'react-router-dom'
```

创建 `Detail` 组件，这里我们将 `Detail` 放到 `Message` 中

```
src
├─ App.jsx
├─ components
├─ pages
│  ├─ Home
│  │  ├─ index.jsx
│  │  ├─ News
│  │  └─ Message
│  │     ├─ index.jsx
│  │     └─ Detail
│  │         └─ index.jsx
│  └─ About
└─index.js
```

引入 `Detail` 组件

```jsx
import Detail from './Detail'
```

### 3 数据初始化

由1划分布局的图可见，导航区显示消息列表，内容展示区展示详细内容的数据列表

将消息列表保存在 `Message` 组件的 `state` 状态中

```jsx
// Message/index.jsx
state = {
  messageArr: [
    { id: '01', title: '消息1' },
    { id: '02', title: '消息2' },
    { id: '03', title: '消息3' },
  ]
```

将详细内容的数据列表，保存在 `Detail` 组件的 `DetailData` 中

```jsx
// Detail/index.jsx
const Detaildata = [
    {id:'01',content:'你好，中国'},
    {id:'02',content:'你好，Milo'},
    {id:'03',content:'你好，未来的自己'},
]
```

### 4 导航区 Link 标签

首先结构赋值，取出 `state` 中的数组

```jsx
const { messageArr } = this.state
```

由于是消息列表，这里用 `map` 的方式将 `state` 中 `messageArr` 遍历并生成 `<li>`

在 `Message` 组件中，导航区的 `<a>` 标签改为 `<Link>` 标签

```jsx
messageArr.map((msgObj) => {
  return (
    <li key={msgObj.id}>
      <Link to={"/home/message/detail/"}>{msgObj.title}</Link>
    </li>
  )
})
```

⚠️注意点：

1. 添加**唯一值 key 来区分节点**，这里用 `id` 作为 `key`
2. 这里因为不需要高亮，所以用 `<Link>` 
3. 嵌套路由匹配子路由时要写上父路由的 `path` 值

### 5 内容展示区 Route 标签

内容展示区的内容要放在  `Detail` 组件中

```jsx
// Detail/index.jsx
<ul>
    <li>ID:???</li>
    <li>TITLE:???</li>
    <li>CONTENT:???</li>
</ul>
```

在 `Message` 组件中，用 `Route` 标签来进行路径的匹配

```jsx
// Message/index.jsx 注册路由
<Route path="/home/message/detail" component={Detail}/>
```

⚠️注意：嵌套路由注册子路由时要写上父路由的 `path` 值

### 6 传递 params 参数

#### 6.1 向路由组件传递params参数

我们通过将数据拼接在路由地址末尾，来实现数据的传递（携带参数）

```jsx
// Message/index.jsx 向路由组件传递params参数
<Link to={`/home/message/detail/${msgObj.id}/${msgObj.title}`}>{msgObj.title}</Link>
```

⚠️注意：需要采用模板字符串以及 `$` +`{}`的方式来进行数据的获取

如上，将消息列表的 `id` 和 `title` 拼接路由地址后面

#### 6.2 声明接收 params 参数

在注册路由时，我们可以通过 `:数据名` 来接收数据（声明接收）

```jsx
// Message/index.jsx 声明接收params参数
<Route path="/home/message/detail/:id/:title" component={Detail} />
```

如上，使用了 `/:id/:title` 成功的接收了由 `Link` 传递过来的 `id` 和 `title` 数据

这样我们既成功的实现了路由的跳转，又将需要获取的数据传递给了 `Detail` 组件

#### 6.3 验证

我们在 `Detail` 组件中 `render` 下打印 `this.props` 来查看当前接收的数据情况

```jsx
console.log(this.props)
```

![](https://s3.bmp.ovh/imgs/2022/07/08/0ceddc40040a66cb.png)

我们可以发现，我们传递的数据被接收到了对象的 `match` 属性下的 `params` 中

#### 6.4 接收参数

因此我们可以在 `Detail` 组件中获取到由 `Message` 组件传递来的 `params` 数据

在 `Detail` 组件 `render ` 下解构赋值得到 `params` 数据中的 `id` 和 `title`

```jsx
const { id, title } = this.props.match.params
```

并通过 `params` 数据中的 `id` 值，在详细内容的数据列表 `DetailData` 中查找出指定 `id` 的详细内容

```js
const findResult = DetailData.find((detailObj) => {
    return detailObj.id === id
})
```

这里 `findResult` 会返回得到指定 `id` 及其详细内容 `content `

```jsx
console.log(findResult)
```

![j0OwcR.png](https://s1.ax1x.com/2022/07/08/j0OwcR.png)

最后在renturn里把相应的数据替换并渲染即可

```jsx
<ul>
    <li>ID:{id}</li>
    <li>TITLE:{title}</li>
    <li>CONTENT:{findResult.content}</li>
</ul>
```

### 总结

1. 路由链接（携带参数）：`<Link to='/demo/test/milo/18'>详情</Link>`
2. 注册路由（声明接收）：`<Route path="/demo/test/:name/:age" component={Test}/>`
3. 接收参数：`const {id,title} = this.props.match.params`

---

最终效果

![](https://s3.bmp.ovh/imgs/2022/07/08/4840b45379db21d2.gif)

## 🍀 七、传递 search 参数

我们还可以采用传递 search 参数的方法来实现，传递参数前步骤都一样

### 1 向路由组件传递 search 参数

我们通过将数据拼接在路由地址末尾，来实现数据的传递（携带参数）

```jsx
// Message/index.jsx 向路由组件传递search参数
<Link to={`/home/message/detail?id=${msgObj.id}&title=${msgObj.title}`}>{msgObj.title}</Link>
```

⚠️注意：采用 `?` 符号的方式来表示后面的为可用数据

如上，将消息列表的 `id` 和 `title` 拼接路由地址后面

### 2 无需声明，正常注册路由

采用 `search` 传递的方式，无需在 `Route` 中再次声明，可以在 `Detail` 组件中直接获取到

```jsx
// Message/index.jsx search参数无需声明接收
<Route path="/home/message/detail" component={Detail}/>
```

### 3 验证

我们在 `Detail` 组件中 `render` 下打印 `this.props` 来查看当前接收的数据情况

```jsx
console.log(this.props)
```

![jBQp4O.png](https://s1.ax1x.com/2022/07/08/jBQp4O.png)

我们可以发现，我们的数据保存在了 `location` 对象下的 `search` 中

### 4 qs 转化字符串

这里 `search` 是以 `urlencoded` 编码字符串形式保存的，需要借助 `qs` 解析，引入 `qs` 库

```js
import qs from 'qs'
```

这个库是 React 中自带有的，它有两个方法，一个是 `parse` 一个是 `stringify` 

 `parse` 方法可以将字符串转化为对象

```jsx
let str = 'name=milo&age=18'
console.log(qs.parse(str)) //{name: 'milo', age: '18'}
```

`stringify` 方法可以将对象转化为字符串

```jsx
let obj = { name: 'milo', age: 18 }
console.log(qs.stringify(obj)) //name=milo&age=18
```

### 5 接收参数

解构赋值，从 `location` 里拿到 `search`

```jsx
const { search } = this.props.location
```

这里我们可以采用 `qs` 的 `parse` 方法，将字符串转化为键值对形式的对象

解构赋值，从转化后的对象中拿到 `id` 和 `title`

```js
const { id, title } = qs.parse(search.slice(1))
```

⚠️注意：

1. `search` 拿到的值是 `?id=01&title=消息1`
2. `qs.parse(search)` 转化的值是 `{?id: '01', title: '消息1'}`
3. 用 `slice()` 方法去除 `？`，`search.slice(1)` 的值是 `id=01&title=消息1`

这样我们就能成功的获取数据，并进行渲染，后面步骤一样

### 总结 

1. 路由链接（携带参数）：`<Link to='/demo/test?name=milo&age=18'>详情</Link>`
2. 注册路由（无需声明）：`<Route path="/demo/test" component={Test}/>`
3. 接收参数：`const {search} = this.props.location`  `const {id,title} = qs.parse(search)`

备注：获取到的 `search` 是 `urlencoded` 编码字符串，需要借助 `qs` 解析

## 🌷 八、传递 state 参数

采用传递 state 参数的方法，它不会将数据携带到地址栏上，采用内部的状态来维护

传递参数前步骤都一样

### 1 向路由组件传递 state 参数

我们通过传递一个路由对象，来实现数据的传递（携带参数）

```jsx
// Message/index.jsx 向路由组件传递state参数
<Link to={{pathname:'/home/message/detail',state:{id:msgObj.id,title:msgObj.title}}}>{msgObj.title}</Link>
```

⚠️注意：在 `Link` 中需要传递一个路由对象，包括一个跳转地址名 `pathname` 和一个数据对象 `state` 

如上，将消息列表的 `id` 和 `title` 传递给 `Detail` 组件

### 2 无需声明，正常注册路由

采用 `state` 参数传递的方式，无需在 `Route` 中再次声明，可以在 `Detail` 组件中直接获取到

```jsx
// Message/index.jsx state参数无需声明接收
<Route path="/home/message/detail" component={Detail}/>
```

### 3 验证

我们在 `Detail` 组件中 `render` 下打印 `this.props` 来查看当前接收的数据情况

```jsx
console.log(this.props)
```

![jB2rVI.png](https://s1.ax1x.com/2022/07/08/jB2rVI.png)

我们可以发现，我们的数据保存在了 `location` 对象下的 `state` 中

### 4 接收参数

解构赋值，取出我们所传递的数据 `id` 和 `title`

```js
const { id, title } = this.props.location.state
```

成功的获取数据，并进行渲染，后面步骤一样

⚠️注意：解决清除缓存造成报错的问题，我们可以在获取不到数据的时候用空对象来替代，例如，

```js
const { id, title } = this.props.location.state || {}
```

当获取不到 `state` 时，则用空对象代替

> 这里的 state 和状态里的 state 不是一个东西！！！

总结：

1. 路由链接（携带参数）：

```jsx
<Link to={{pathname:'/demo/test',state:{id:'milo',age:18}}}>详情</Link>
```

2. 注册路由（无需声明）：`<Route path="/demo/test" component={Test}/>`

3. 接收参数：`const {id,title} = this.props.location.state`

备注：用 `<BrowserRouter>` 时刷新可以保留住参数，但用 `<HashRouter>` 刷新参数消失

---

> 非常感谢您的阅读，欢迎提出你的意见，有什么问题欢迎指出，谢谢！🎈

