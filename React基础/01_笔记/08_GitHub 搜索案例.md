# 08_GitHub 搜索案例

> 📢 大家好，我是Milo
>
> 📢 这篇文章是学习 React 中 GitHub 搜索案例的学习笔记
>

## 引言

本文主要介绍 React 学习中 Github 搜索案例，这个案例主要涉及到了 Axios 发送请求，数据渲染以及一些中间交替效果的实现

个人感觉在做完 TodoList 案例之后，这个案例会很轻松，只是多加了一个 Loading 效果的实现思路，以及一些小细节的完善，感觉练练手还是很不错的

## 一、实现静态组件

和之前的 TodoList 案例一样，我们需要先实现静态组件

### 1 拆分组件

在实现静态组件之前，我们还需要拆分组件，这个页面的组件，我们可以将它拆成以下两个组件，第一个组件是 `Search`，第二个是 `List` 

![j1AnXT.png](https://s1.ax1x.com/2022/07/02/j1AnXT.png)

### 2 实现静态组件

接下来我们需要将提前写好的静态页面，对应拆分到组件当中

首先，我们在 `src` 目录下，新建一个 `components` 文件夹，用于存放我们的组件，然后在文件夹下，新建 `List` 、`Search` 组件文件夹，再创建其下的 `index.jsx`，`index.css` 文件，用于创建对应组件及其样式文件

其次，在 `public` 下创建 `css` 文件夹，放入 `bootstrap.css` 文件，记得引入 css文件

```shell
Github搜索案例
├─ package.json
├─ public
│  ├─ css
│  │  ├─ bootstrap.css
│  ├─ favicon.ico
│  └─ index.html
├─ src
│  ├─ App.jsx
│  ├─ components
│  │  ├─ List
│  │  │  ├─ index.css
│  │  │  └─ index.jsx
│  │  └─ Search
│  │     └─ index.jsx
│  └─ index.js
└─ yarn.lock
```

最终目录结构如上

---

⚠️注意：各 jsx 编写的时候需要注意

1. class 需要改成 className
2. style 的值需要使用双花括号的形式

3. `img` 标签，一定要**添加** `alt` 属性表示图片加载失败时的提示

4. `a` 标签要添加 `rel="noreferrer"`属性，不然会有大量的警告出现

![image-20210828070148865](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/image-20210828070148865.png)

---

展示页面效果：

![jQc4pQ.png](https://s1.ax1x.com/2022/07/01/jQc4pQ.png)

## 二、axios 发送请求

在实现静态组件之后，我们需要通过向 `github` 发送请求，来获取相应的用户信息

### 1 启动服务器

但短时间内多次请求，可能会导致请求不返回结果等情况发生，因此我们采用了一个事先搭建好的本地服务器

用天禹老师给我们的本地服务器，这个文件在 [这里](https://github.com/Milo980412/NOTE_REACT/tree/master/React%E5%9F%BA%E7%A1%80/06_react%20B%E7%AB%99%E8%A7%86%E9%A2%91%E8%B5%84%E6%96%99/05_%E6%89%80%E9%9C%80%E6%9C%8D%E5%8A%A1%E5%99%A8)

用vscode打开这文件，里面有 `server.js`

```
server
├─ node_modules
├─ server.js
├─ package.json
└─ yarn.lock
```

建议在vscode里启动终端，然后启动 `sever1.js` 服务器

```shell
npm start
```

此时服务器启动成功，有两个访问地址，/users是真的访问github，/users2是本地模拟数据

```shell
服务器启动成功
请求github真实数据请访问：http://localhost:5000/search/users
请求本地模拟数据请访问：http://localhost:5000/search/users2
```

这俩选择一个去请求数据就行

### 2 axios发送请求

#### 2.1 按钮绑定事件

首先在 Search 的 index.jsx 里，对 `button` 按钮绑定事件 `serach`

```jsx
<button onClick={this.serach}>搜索</button>
```

编写 `serach` 函数，需要获取用户的输入以及发送网络请求

```jsx
serach = () => {
  // 获取用户的输入
  // 发送网络请求
  )
}
```

#### 2.2 获取用户的输入

在需要触发事件的 `input` 标签中，添加 `ref` 属性，这里采用 [02_面向组件编程/回调形式refs](https://miloreact.github.io/pages/react/02_%E9%9D%A2%E5%90%91%E7%BB%84%E4%BB%B6%E7%BC%96%E7%A8%8B.html#_3-refs)

```jsx
 <input ref={c => this.keyWordElement = c} type="text" placeholder="输入关键词点击搜索" />
```

在 `serach` 函数中，我们通过连续的解构赋值，解构到 `value` 后，并将 `value` 重命名为 `keyWord` 

```jsx
const { keyWordElement: { value: keyWord } } = this
```

此时 `value` 被重命名为 `keyWord` ，获取到了 `keyWord` 值，接下来我们就需要发送请求了

```jsx
console.log(keyWord)  // 返回你输入框输入的内容
```

#### 2.3 配置代理，发送网络请求

详情见 [07_脚手架配置代理](https://miloreact.github.io/pages/react/07_%E8%84%9A%E6%89%8B%E6%9E%B6%E9%85%8D%E7%BD%AE%E4%BB%A3%E7%90%86.html#_2-%E5%8D%95%E7%8B%AC%E9%85%8D%E7%BD%AE)

**首先** 我们需要在Reac t脚手架 `src` 目录下，创建代理配置文件 `setupProxy.js`

⚠️注意：这个文件只能叫这个名字，脚手架在启动的时候，会自动执行这些文件

**然后** 在 `setupProxy.js` 里配置代理

配置一个代理的完整代码如下：

```jsx
// setupProxy.js
const {createProxyMiddleware} = require('http-proxy-middleware')
module.exports = function(app) {
  app.use(
    createProxyMiddleware('/api1',{
      target: 'http://localhost:5000',
      changeOrigin: true, 
      pathRewrite: {'^/api1': ''}
    }),
  )
}
```

在 `serach` 函数中，调用 axios.get 发送网络请求

```jsx
axios.get(`https://localhost:3000/api1/search/users?q=${keyWord}`).then(
  response => {console.log('成功了',response.data)},
  error => {console.log('失败了', error)}
)
```

我们将 `keyWord` 接在请求地址的后面，来传递参数，以获得相关数据

因为配置了代理来解决跨域的问题，我们需要在请求地址前（端口号后面）加上启用代理的标志 `/api1`

---

⚠️注意：

1. 此时我们站在 3000 向 3000 发送请求，因为 3000 有代理，所以转发给 5000 从而解决了跨域的问题

2. 当我们站在 3000 向 3000 发送请求时（站的位置就是要发请求的地方），其实可以简写如下

```jsx
axios.get(`/api1/search/users?q=${keyWord}`).then()
```

---

这样我们就能成功的获取到了数据

![jlCNY6.png](https://s1.ax1x.com/2022/07/01/jlCNY6.png)

## 三、渲染数据

在获取到了数据之后，我们需要对数据进行分析，并将这些数据渲染到页面上

### 1 初始化状态

目前的学习知识中，`Search` 和 `List`  属于兄弟组件，没有办法进行直接的数据传递，因此可以将数据传递给 APP 再由 APP 转发给子组件

父子组件之间传递参数的方式，可以通过 `state` 和 `props` 实现

我们通过在父组件也就是 `App.jsx` 中设置状态

```jsx
// 初始化状态，users初始值
state = { users: [] }
```

### 2 数据传递函数

我们在 `App.jsx` 中添加 `saveUsers` 函数，这样可以将 `Search` 组件传递的参数，维护到 `App` 的状态中

```jsx
// 保存users数据
saveUsers = (users) => {
  this.setState({ users })
}
```

然后将这个函数通过 `props` 传递给 `Search` 组件

```jsx
<Search saveUsers={this.saveUsers}/>
```

### 3  serach 函数

在 `Search` 组件的 `serach` 函数中，调用 `saveUsers` 函数，将 `Search` 组件传递的参数，维护在 `App` 的状态中

```jsx
// Search inde.jsx
// 发送网络请求
axios.get(`/api1/search/users?q=${keyWord}`).then(
  response => {this.props.saveUsers(response.data.items)},
  error => {console.log('失败了', error)}
)
```

### 4 渲染数据

在 `App` 组件我们通过解构取出状态中的 `users`

```jsx
const {users} = this.state
```

再将它通过 `props` 传递给 `List` 组件

```jsx
<List users={users}/>
```

在 `List` 组件中，通过 `map` 遍历整个返回的数据，渲染用户数据

⚠️注意：我们获取到的用户个数是动态的，因此我们需要通过遍历的方式去实现

```js
this.props.users.map((userObj) => {
  return (
    <div key={userObj.id} className="card">
      <a rel="noreferrer" href={userObj.html_url} target="_blank">
        <img alt="avatar" src={userObj.avatar_url} style={{ width: '100px' }} />
      </a>
      <p className="card-text">{userObj.login}</p>
    </div>
  )
})
```

⚠️注意：我们新建的 `user` 对象，一定要保证它的 `id` 的唯一性，用数据里的用户 `id` 作为 `key={userObj.id}`

同时将一些用户信息添加到其中，需要添加的信息有用户链接 `html_url` 、用户头像 `avatar_url` 、用户昵称 `login`

---

这样我们就能成功的渲染用户数据

![j1kOkd.png](https://s1.ax1x.com/2022/07/02/j1kOkd.png)

## 四、增加交互

做到这里其实已经完成了一大半了，但是似乎少了点交互

1. 第一次进入页面时 List 组件中的 **欢迎使用字样**

2. 加载时的 **Loading... 效果**

3. 在报错时应该 **提示错误信息**

这一些都预示着我们不能单纯的将用户数据直接渲染

我们需要添加一些判断，什么时候该渲染数据，什么时候渲染 loading，什么时候渲染 err 

---

### 1 增加初始化状态

首先我们需要增加一些状态，来指示我们该渲染什么，比如

1. 采用 `isFrist` 来判断页面是否第一次启动，初始值给 `true`，点击搜索后改为 `false`

2. 采用 `isLoading` 来判断是否应该显示 Loading 动画，初始值给 `false`，在点击搜索后改为 `true`，在拿到数据后改为 `false`

3. 采用 `err` 来判断是否渲染错误信息，当报错时填入报错信息，初始值**给空**

```jsx
// 初始化状态
state = {
  users: [], // users初始化为数组
  isFirst:true, // 是否为第一次打开页面
  isLoading:false, // 标识是否处于加载中
  err:'', // 存储请求相关错误信息
}
```

### 2 重写数据传递函数

重写 `saveUsers` 函数方法为 `updateAppState` ，采用更新状态的方式，接收一个状态对象来**更新数据**

```js
// 接收一个状态对象
updateAppState = (stateObj) => {
    this.setState(stateObj)
}
```

⚠️注意：我们需要改变数据传递方式，这样就不用去指定什么时候更新什么，减少了很多**不必要**的函数声明

然后将这个函数通过 `props` 传递给 `Search` 组件

```jsx
<Search updateAppState={this.updateAppState} />
```

### 3 重写 serach 函数

在 `Search` 组件的 `serach` 函数中，多次调用 `updateAppState` 函数

```jsx
// Search/index.jsx
serach = () => {
    // 获取用户的输入
    const { keyWordElement: { value: keyWord } } = this
    // 发送请求前通知App更新状态
    this.props.updateAppState({ isFirst: false, isLoading: true })
    // 发送网络请求
    axios.get(`/api1/search/users?q=${keyWord}`).then(
      response => {
        // 请求成功后通知App更新状态
        this.props.updateAppState({ isLoading: false, users: response.data.items })
      },
      error => {
        // 请求失败通知App更新状态
        this.props.updateAppState({ isLoading: false, err: error.message })
      }
    )
  }
```

我们的状态更新是在 `Search` 组件中实现的，在点击搜索之后

1. 发送请求前通知 App 更新状态 `isFirst: false, isLoading: true`

2. 请求成功后通知 App 更新状态 `isLoading: false, users: response.data.items`

3. 请求失败通知 App 更新状态 `isLoading: false, err: error.message`

### 4 判断状态的值来渲染数据

在 App 组件给 List 组件传递数据时，我们可以采用批量传递的方式，将状态里的对象都传递给 List ，这样可以减少代码量

```jsx
<List {...this.state} />
```

⚠️注意：此时之前的解构取出状态中的 `users `就可以删掉了

然后我们只需要在 List 组件中，判断这些状态的值，来显示即可，这里采用连续三目运算符

```jsx
// List/index.jsx
// 对象解构
const { users, isFirst, isLoading, err } = this.props
// 判断
{
  isFirst ? <h2>欢迎使用，输入关键字，点击搜索</h2> :
    isLoading ? <h2>Loading...</h2> :
      err ? <h2 style={{ color: 'red' }}>{err}</h2> :
        users.map((userObj) => {
          return (
           // 渲染数据块
            <div key={userObj.id} className="card">
              <a rel="noreferrer" href={userObj.html_url} target="_blank">
                <img alt="avatar" src={userObj.avatar_url} style={{ width: '100px' }} />
              </a>
              <p className="card-text">{userObj.login}</p>
            </div>
          )
        })
}
```

连续三目运算符判断：

1. 我们需要先判断是否第一次
2. 再判断是不是正在加载
3. 再判断有没有报错
4. 通过层层判断，如果不是第一次、不是正在加载、没有报错，最后再渲染数据

---

以上就是 Github 搜索案例的实现过程

最终效果图：

![](https://s3.bmp.ovh/imgs/2022/07/03/d9268e9955d9fdac.gif)

---

> 前端路还有很长，加油吧！！！

> 非常感谢您的阅读，欢迎提出你的意见，有什么问题欢迎指出，谢谢！🎈

