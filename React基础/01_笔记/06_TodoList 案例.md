# 06_TodoList 案例 

> 📢 大家好，我是Milo
>
> 📢 这篇文章是学习 React 练习中 TodoList 案例的操作笔记
>

## 引言

TodoList 案例在前端学习中挺重要的，从原生 JavaScript 的增删查改，到现在 React 的组件通信，都是一个不错的案例，这篇文章主要记录，还原一下通过 React 实现 TodoList 的全过程

![image-20210826091013929](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/image-20210826091013929.png)

## 一、拆分组件

首先第一步需要做的是将这个页面拆分成几个组件

首先顶部的输入框，可以完成添加项目的功能，可以拆分成一个 **Header 组件**

中间部分可以实现一个渲染列表的功能，可以拆分成一个 **List 组件**

在这部分里面，每一个待办事项都可以拆分成一个 **Item 组件**

最后底部显示当前完成状态的部分，可以拆分成一个 **Footer 组件**

![image-20210826092737826](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/image-20210826092737826.png)

在拆分完组件后，我们下一步要做的就是去实现这些组件的静态效果

## 二、实现静态组件

首先，我们可以先写好这个页面的静态页面，然后再分离组件，所以这就要求我们

以后写静态页面的时候，一定要有明确的规范

1. 打好注释
2. 每个部分的 CSS 要写在一个地方，不要随意写
3. 命名一定要规范
4. CSS 选择器不要关联太多层级
5. 在写 HTML 时就要划分好布局

这样有利于我们分离组件

首先，我们在 `src` 目录下，新建一个 `Components` 文件夹，用于存放我们的组件，然后在文件夹下，新建 `Header` 、`Item`、`List` 、`Footer` 组件文件夹，再创建其下的 `index.jsx`，`index.css` 文件，用于创建对应组件及其样式文件

```markdown
todolist
├─ package.json
├─ public
│  ├─ favicon.ico
│  └─ index.html
├─ src
│  ├─ App.css
│  ├─ App.jsx
│  ├─ Components
│  │  ├─ Footer
│  │  │  ├─ index.css
│  │  │  └─ index.jsx
│  │  ├─ Header
│  │  │  ├─ index.css
│  │  │  └─ index.jsx
│  │  ├─ item
│  │  │  ├─ index.css
│  │  │  └─ index.jsx
│  │  └─ List
│  │     ├─ index.css
│  │     └─ index.jsx
│  └─ index.js
└─ yarn.lock
```

最终目录结构如上

然后我们将每个组件，对应的 HTML 结构 CV 到对应组件的 `index.jsx` 文件中 `return` 出来，再将 CSS 样式添加到 `index.css` 文件中

**记得**，在 `index.jsx` 中一定要引入 `index.css` 文件

实现了静态组件后，我们需要添加事件等，来实现动态组件

## 三、实现动态组件

### 🍎 1. 动态展示列表

我们目前实现的列表项是固定的，我们需要它通过**状态**来维护，而不是通过**组件标签**来维护

首先我们知道，父子之间传递参数，可以通过 `state` 和 `props` 实现

我们通过在父组件也就是 `App.jsx` 中设置状态

```jsx
// 初始化状态
state = {
  todos: [
    { id: '001', name: '吃饭', done: true },
    { id: '002', name: '睡觉', done: true },
    { id: '003', name: '打代码', done: false },
    { id: '004', name: '逛街', done: true },
  ]
}
```

再将它传递给对应的渲染组件 `List` 

```jsx
const { todos } = this.state
<List todos={todos}/>
```

这样在 `List` 组件中就能通过 `props` 来获取到 `todos` 

我们通过解构取出 `todos`

```jsx
const { todos } = this.props
```

再通过 `map` 遍历渲染 `Item` 数量

```js
todos.map(todo => {
  return <Item key={todo.id} {...todo}/>
})
```

同时由于我们的数据渲染最终是在 `Item` 组件中完成的，所以我们需要将数据传递给 `Item` 组件

这里有两个注意点

1. 关于 `key` 的作用在 diff 算法的文章中已经有讲过了，需要满足**唯一性**
2. 这里采用了简写形式 `{...todo}` ，这使得代码更加简洁，它代表的意思是 

```js
id = {todo.id} name = {todo.name} done = {todo.done}
```

在 `Item` 组件中通过解构取出 `props` 即可使用

```js
const { id, name, done } = this.props
```

这样我们更改 `APP.jsx` 文件中的 `state` 就能驱动着 `Item` 组件的更新，如图

![react-todolist-1](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-1.gif)

同时这里需要注意的是

对于复选框的选中状态，这里采用的是 `defaultChecked = {done}`，相比于 `checked` 属性，这个设定的是默认值，能够更改

### 🍍 2. 添加事项功能

首先我们需要在 Header 组件中，绑定键盘事件，判断按下的是否为回车，如果为回车，则将当前输入框中的内容传递给 App 组件

> 因为，在目前的学习知识中，Header 组件和渲染组件 List 属于兄弟组件，没有办法进行直接的数据传递，因此可以将数据传递给 APP 再由 APP 转发给 List。

```jsx
//键盘事件的回调
handleKeyUp = (event)=>{
  //解构赋值获取keyCode,target
  const {keyCode,target} = event
  //判断是否是回车按键
  if(keyCode !== 13) return
  //准备好一个todo对象
  const todoObj = {id:nanoid(),name:target.value,done:false}
  //将todoObj传递给App
  this.props.addTodo(todoObj)
}
```

⚠️注意1：这里除了绑定键盘事件，判断按下的是否为回车键，用 `addTodo` 传递参数给App组件，还有一些细节

- 添加的todo名字不能为空，所以需要判断一下 `target.value` 值是否为空串，`trim()` 用来去除空格

```jsx
//添加的todo名字不能为空
if(target.value.trim() === ''){
  alert('输入不能为空')
  return
}
```

- 传递完后，要把输入框内的数据清空，直接将 `target.value` 值设为空串即可

```jsx
//清空输入
target.value = ''
```

⚠️注意2：我们在 `App.jsx` 中添加了事件 `addTodo` ，这样可以将 Header 组件传递的参数，维护到 `App` 的状态中

```jsx
// App.jsx
addTodo = (todoObj) => {
  const { todos } = this.state
  // 追加一个 todo
  const newTodos = [todoObj, ...todos]
  this.setState({ todos: newTodos })
}
```

在这小部分中，需要我们注意的是，我们新建的 `todo`  对象，一定要保证它的 `id` 的唯一性

这里采用的 `nanoid` 库，这个库的每一次调用都会返回一个唯一的值

```shell
npm i nanoid  #或者用 yarn add nanoid
```

安装这个库，然后哪里使用就在哪里引入，这里我们在Header引入

```jsx
import {nanoid} from 'nanoid'
```

通过 `nanoid()` 即可生成唯一值

```jsx
console.log(nanoid()) //wUncb6ywbx-9CWzHhrOj7
```

![react-todolist-addtodo](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-addtodo.gif)

### 🍋 3. 实现鼠标悬浮效果

接下来我们需要实现每个 `Item` 中的小功能

首先是鼠标移入时的变色效果

我的逻辑是，通过一个状态来维护是否鼠标移入，比如用一个 `state={mouse:flag }` ，当鼠标移入时，设定状态为 `true` ；当鼠标移出时设为 `false` 。然后我们只需要在 `style` 中用 `mouse` 去设定样式的两种不同情况即可

下面我们来代码实现

在 `Item` 组件中，先设定状态

```jsx
// 标识鼠标移入，移出
state = { mouse: flag } 
```

给元素 `li` 绑定上鼠标移入，移出事件

```js
<li onMouseEnter={this.handleMouse(true)} onMouseLeave={this.handleMouse(false)} ><li/>
```

当鼠标移入时，会触发 `onMouseEnter` 事件，调用 `handleMouse` 事件传入参数 `true` 表示鼠标进入，更新组件状态

```jsx
// 鼠标移入、移出的回调
handleMouse = (flag) => {
  return () => {
    this.setState({ mouse: flag })
  }
```

再在 `li` 身上添加由 `mouse` 控制的背景颜色

```js
style={{ backgroundColor: this.state.mouse ? '#ddd' : 'white' }}
```

同时在 `button` 上通过 `mouse` 来控制删除按钮的显示和隐藏

```jsx
style={{ display: mouse ? 'block' : 'none' }}
```

![react-todolist-mouse](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-mouse.gif)

观察 mouse 的变化

### 🍉 4. 复选框状态维护

我们需要将当前复选框的状态，维护到 `state`  当中

我们的思路是

在复选框中添加一个 `onChange`  事件来进行数据的传递，通过 `id` 我们知道改变状态的是哪一个 `Item` ,当事件触发时我们执行 `handleCheck` 函数，这个函数可以通过 App 组件内定义的 `updateTodo` 向 App 组件中传递参数 `id` 和 `done`，这样再在 App 中改变状态即可

首先在 `input` 复选框绑定一个 `onChange`  事件

```js
// Item/index.jsx
<input type="checkbox" defaultChecked={done} onChange={this.handleCheck(id)} />
```

勾选、取消勾选某一个todo的事件回调函数

```jsx
// 勾选、取消勾选某一个todo的回调
handleCheck = (id) => {
  return (event) => {
    this.props.updateTodo(id,event.target.checked)
  }
}
```

⚠️注意：由于我们需要传递 `id` 来记录状态更新的对象，因此我们需要采用**高阶函数**的写法，让 `this.handleCheck(id)` 的返回值是一个函数，不然会报错，复选框的状态我们可以通过 `event.target.checked` 来获取

我们将需要改变状态的 `Item` 的 `id` 和复选框的状态 `done ` 传递给 App 组件，此时需要通过 App 组件内定义的 `updateTodo` ，来接受参数并更新 App 组件中的状态

我们传递了两个参数 `id` 和 `done` ，通过 `map` 遍历找出该 `id` 对应的 `todo` 对象，更改它的 `done` 即可，最后更新状态

```js
// App.jsx
// updateTodo用于更新一个todo对象
updateTodo = (id, done) => {
  // 获取状态中的todos
  const { todos } = this.state
  // 匹配处理数据
  const newTodos = todos.map((todoObj) => {
    if (todoObj.id == id) return { ...todoObj, done }
    else return todoObj
  })
  // 更新状态
  this.setState({ todos: newTodos })
}
```

这里更改的方式是 `{ ...todoObj, done }`，首先会展开 `todoObj` 的每一项，再对 `done` 属性做覆盖

⚠️注意：App是List的父元素，List是Item的父元素，所以App是Item的祖先元素。要想让App与Item传递数据，必须先让App给List传递函数，再在List里给Item传递函数。

和传递 `todos` 一样，在 `List` 组件中可以通过 `props` 来获取到 `updateTodo` 函数

将这个函数传递给 List 组件

```jsx
<List todos={todos} updateTodo={this.updateTodo} />
```

在List组件里通过解构取出 `updateTodo` 函数

```jsx
// List index.jsx
const { todos, updateTodo } = this.props
```

在通过 `map` 遍历渲染 `Item` 时，将 `updateTodo={updateTodo}` 再次通过 `props` 传递给 `Item`

```jsx
todos.map(todo => {
  return <Item key={todo.id} {...todo} updateTodo={updateTodo} />
```

最后在 `Item` ，这里没解构取出，直接用 `this.props.updateTodo(id,done)` 了，当然你也可以解构

```jsx
this.props.updateTodo(id,event.target.checked)
```

![react-todolist-update](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-update.gif)



### 🍏 5. 限制参数类型

在我们前面写的东西中，我们并没有对参数的**类型以及必要性**进行限制

在前面我们也学过这个，我们需要借助 `propTypes` 这个库

```sh
npm i prop-types  #或者 yarn add prop-types
```

首先我们需要引入这个库

```jsx
import PropTypes from 'prop-types'
```

然后对 `props` 进行限制

在Header 组件中需要接收一个 `addTodo` 函数，所以我们进行一下限制

```jsx
// 对接收的props进行类型以及必要性的限制
static propTypes = {
  addTodo: PropTypes.func.isRequired
}
```

在 List 组件中也需要进行对 `todos` 以及 `updateTodo` 的限制

```jsx
// 对接收的props进行类型以及必要性的限制
static propTypes = {
  todos:PropTypes.array.isRequired,
  updateTodo:PropTypes.func.isRequired
}
```

如果传入的参数不符合限制，则会报 **warning**

### 🍒 6. 删除按钮

现在我们需要实现删除按钮的效果，这个和前面的挺像的

首先我们需要在 `Item` 组件上的按钮绑定删除的点击事件 `handleDelete` 。在App组件中定义一个 `deleteTodo` ，将被点击删除按钮的 `Item` 的 `id` 值，通过 `props` 传递给父元素 `List`，再通过在 `List` 中绑定的 `App` 组件的 `deleteTodo` ，将 `id` 传递给 `App` 来改变 `state` 状态

⚠️注意：因为 `setState` 每次调用会重新执行 `render`渲 染，所以 `state` 中如果没有了要删除的 `todo` 对象，再次渲染后就达到了删除的效果。

首先我们先编写 `handleDelete` 点击事件

```jsx
// Item/index.jsx
// 删除一个todo的回调
handleDelete = (id) => {
    this.props.deleteTodo(id)
}
```

子组件想影响父组件的状态，需要父组件传递一个函数，因此我们在 `App` 中添加一个 `deleteTodo` 函数

```jsx
// app.jsx
// deleteTodo用于删除一个todo对象
deleteTodo = (id) => {
  const { todos } = this.state
  const newTodos = todos.filter(todoObj => {
    return todoObj.id !== id
  })
  this.setState({ todos: newTodos })
}
```

然后将这个函数传递给 List 组件

```jsx
<List todos={todos} updateTodo={this.updateTodo} deleteTodo={this.deleteTodo} />
```

在List组件里通过解构取出 `updateTodo` 函数

```jsx
// List index.jsx
const { todos, updateTodo, deleteTodo } = this.props
```

再将这个函数传递给 Item 组件

```jsx
//List index.jsx
todos.map(todo => {
  return <Item key={todo.id} {...todo} updateTodo={updateTodo} deleteTodo={deleteTodo} />
})
```

顺便对接收的props进行类型以及必要性的限制

```jsx
deleteTodo: PropTypes.func.isRequired
```

再传递给 Item

在 `Item` 的 `handleDelete` 回调函数里增加一个判断，弹窗问是否确认删除

```jsx
if(window.confirm('确认删除吗？')) {
    this.props.deleteTodo(id)
}
```

⚠️注意：这里用了 `confirm` ，记得前面加上 `window`

![react-todolist-detele](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-detele.gif)

### 🍓 7. 获取完成数量

我们在 App 中向 `Footer` 组件传递 `todos` 数据，再去统计数据

统计 `done `为 `true` 的个数

```js
const doneCount = todos.reduce((pre, todo) => {
    return pre + (todo.done ? 1 : 0)
}, 0)
```

再渲染数据即可

![image-20210826160505813](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/image-20210826160505813.png)

### 🍊 8. 全选按钮

首先我们需要在按钮上绑定事件，由于子组件需要改变父组件的状态，所以我们的操作和之前的一样，先绑定事件，再在 App 中传一个函数给 Footer ，再在 Footer 中调用这个函数并传入参数即可

这里需要特别注意的是

`defaulChecked` 只有第一次会起作用，所以我们需要将前面写的改成 `checked` 并添加 `onChange` 事件即可

首先我们先在 App 中给 Footer 传入一个函数 `checkAllTodo` ，它需要传入一个参数done来决定全选还是全不选

```js
// App.jsx
// checkAllTodo用于全选
checkAllTodo = (done) => {
  // 获取原来的todos
  const { todos } = this.state
  // 加工数据
  const newTodos = todos.map((todoObj) => {
    return { ...todoObj, done }
  })
  // 更新状态
  this.setState({ todos: newTodos })
}
```

然后将这个函数传递给 Footer 组件

```jsx
<Footer todos={todos} checkAllTodo={this.checkAllTodo}/>
```

然后我们需要在 Footer 中调用一下，这里我们通过事件传入了一个参数：当前按钮的状态，用于全选和取消全选

```js
handleCheckAll = (event) => {
    this.props.checkAllTodo(event.target.checked)
}
```

同时我们需要排除总数 `total` 为 0 时的干扰

```js
<input type="checkbox" checked={doneCount === total && total !== 0? true : false} onChange={this.handleCheckAll} />
```

![react-todolist-all](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-all.gif)

### 🥭 9. 删除已完成

给删除按钮添加一个点击事件，回调中调用 App 中添加的删除已完成的函数，全都一个套路

**强烈建议这个自己打**

首先在 App 中定义函数，过滤掉  `done` 为 `true` 的，再更新状态即可

```js
// App.jsx
clearAllDone = () => {
  const { todos } = this.state
  const newTodos = todos.filter((todoObj) => {
    return todoObj.done !== true
  })
  this.setState({ todos: newTodos })
}
```

然后将这个函数传递给 Footer 组件

```jsx
<Footer todos={todos} checkAllTodo={this.checkAllTodo} clearAllDone={this.clearAllDone}/>
```

给Footer的button绑定点击事件

```jsx
<button onClick={this.handleClearAllDone} className="btn btn-danger">清除已完成任务</button>
```

然后我们需要在 Footer 的点击事件中调用 App 组件定义的函数

```jsx
//清除已完成任务的回调
handleClearAllDone=()=>{
  this.props.clearAllDone()
}
```

![react-todolist-clear](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react-todolist-clear.gif)



## 总结

1. 拆分组件、实现静态组件，注意：className、style 写法

2. 动态初始化列表，如何确定将数据放在那个组件的state中？

   - 某个组件使用：放在自身的state中
   - 某些组件使用：放在他们公共的父组件state中（官方称此操作为：状态提升）

3. 关于父子之间通信：

   - **父组件**给**子组件**传递数据，通过 `props`传递
   - **子组件**给**父组件**传递数据，通过 `props`，要求父提前给子传递一个函数


4. 注意 `defaultChecked` 和 `checked` 的区别，类似的还有 `defaultValue` 和 `value`

5. 状态在哪里，操作状态的方法就在哪里

6. 一定要自己敲一遍！！！

> 非常感谢您的阅读，欢迎提出你的意见，有什么问题欢迎指出，谢谢！🎈
