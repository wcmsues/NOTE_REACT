# 01_基础知识以及 jsx 语法

> 📢 大家好 ，我是Milo同学，最近在学习 React，是我学习的第一个框架
>

先附上[React官网](https://zh-hans.reactjs.org/) ，有很多问题都要通过查询官方文档来解决，要学会查文档~

## 一、React 简介

### 1. 关于 React

整几个面试题来认识一下~~

> 什么是 React ？

**React** 是一个用于构建用户界面的 JavaScript 库。

- 是一个将数据渲染为 HTML 视图的开源 JS 库
- 它遵循基于组件的方法，有助于构建可重用的 UI 组件
- 它用于开发复杂的交互式的 web 和移动 UI

> React 有什么特点？

1. 声明式编码，组件化编码
2. 使用虚拟 DOM 而不是真正的 DOM
3. 它可以用服务器渲染
4. 它遵循单向数据流或数据绑定
5. 高效（优秀的Diffing算法、使用虚拟 DOM 而不是真正的 DOM）

> React 的一些主要优点？

1. 它提高了应用的性能（优秀的Diffing算法、使用虚拟 DOM 而不是真正的 DOM）
2. 可以方便在客户端和服务器端使用
3. 由于使用 JSX，代码的可读性更好
4. 使用React，编写 UI 测试用例变得非常容易

### 2. Hello React

首先需要引入几个 react 包，我直接用的是老师下载好的，核心库必须在扩展库前面引入！

- React 核心库、操作 DOM 的 react 扩展库、将 jsx 转为 js 的 babel 库

```jsx
<!-- 准备好一个“容器” -->
	<div id="test"></div>
<!-- 引入react核心库 -->
	<script type="text/javascript" src="../js/react.development.js"></script>
	<!-- 引入react-dom，用于支持react操作DOM -->
	<script type="text/javascript" src="../js/react-dom.development.js"></script>
	<!-- 引入babel，用于将jsx转为js -->
	<script type="text/javascript" src="../js/babel.min.js"></script>
  /* 此处一定要写babel */
  <script type="text/babel" ></script>
```

然后在<script type="text/babel" ></script>里创建虚拟DOM

```jsx
//1.创建虚拟DOM
const VDOM = <h1>Hello,React</h1>
//2.渲染虚拟DOM到页面
ReactDOM.render(VDOM,document.querySelector(".test"))
```

如果我创建两个虚拟DOM，渲染两次（注意⚠️）

```jsx
// 1.创建虚拟DOM
const VDOM = <h1>Hello,React</h1>
const VDOM2 = <h1>Hello,Milo</h1>
// 2.渲染虚拟DOM到页面
ReactDOM.render(VDOM,document.getElementById('test'))
ReactDOM.render(VDOM2,document.getElementById('test'))
```

由于渲染不是一个叠加操作，而是覆盖操作，页面只会渲染生成hello，milo的DOM操作！



### 3. 虚拟 DOM 和真实 DOM 的两种创建方法
> 为什么不用js而使用jsx？

- JS创建虚拟DOM太繁琐了，JSX更方便

#### 3.1 JS 创建虚拟 DOM

```js
//1.创建虚拟DOM,创建嵌套格式的dom
const VDOM=React.createElement('h1',{id:'title'},React.createElement('span',{},'hello,React'))
//2.渲染虚拟DOM到页面
ReactDOM.render(VDOM,document.getElementById('test'))
```

#### 3.2 Jsx 创建虚拟DOM

```jsx
//1.创建虚拟DOM
	const VDOM = (  /* 此处一定不要写引号，因为不是字符串 */
    	<h1 id="title">
			<span>Hello,React</span>
		</h1>
	)
//2.渲染虚拟DOM到页面
ReactDOM.render(VDOM,document.getElementById('test'))
```

> js 的写法并不是常用的，常用jsx来写，毕竟JSX更符合书写的习惯

#### 3.3 虚拟DOM与真实DOM

> 关于虚拟DOM：

1. 本质是Object类型的对象（一般对象）

2. 虚拟DOM比较“轻”，真实DOM比较“重”，因为虚拟DOM是React内部在用，无需真实DOM上那么多的属性。

3. 虚拟DOM最终会被React转化为真实DOM，呈现在页面上。

```jsx
console.log('虚拟DOM',VDOM); //[Object Object]
console.log(typeof VDOM); //object
console.log(VDOM instanceof Object); //true
```

```jsx
const TDOM = document.getElementById('demo')
console.log('虚拟DOM',VDOM); //[Object Object]
console.log('真实DOM',TDOM); //<div id="demo"></div>
debugger;
```

## 二、jsx 语法

### 1. jsx语法规则

1. 定义虚拟DOM，不能使用`“”`

2. 标签中混入JS表达式的时候使用`{}`，toLowerCase()方法可转化成小写

```jsx
// 1.创建虚拟DOM
const VDOM = (
    <h2 id={myId.toLowerCase()}>
        <span>{myData.toLowerCase()}</span>
    </h2>)
```

3. 样式的类名指定不能使用class，使用`className`

4. 内敛样式要使用`{{}}`包裹，fontSize这种属性要写小驼峰

```jsx
style={{color:'skyblue',fontSize:'24px'}}
```

5. 不能有多个根标签，只能有一个根标签

6. 标签必须闭合，自闭合也行

 ```jsx
   <input type="text"/>
 ```

7. - 如果小写字母开头，就将标签转化为 html 同名元素，如果 html 中无该标签对应的元素，就报错

   - 如果是大写字母开头，react 就去渲染对应的组件，如果没有就报错

> 记几个

#### 1.1 注释

写在花括号里

```jsx
ReactDOM.render(
    <div>
    <h1>小丞</h1>
    {/*注释...*/}
     </div>,
    document.getElementById('example')
);
```

#### 1.2 数组

JSX 允许在模板中插入数组，数组自动展开全部成员

```jsx
var arr = [
  <h1>小丞</h1>,
  <h2>同学</h2>,
];
ReactDOM.render(
  <div>{arr}</div>,
  document.getElementById('example')
);
```

### 2. JSX 小练习

根据动态数据生成 `li`

```jsx
const data = ['A','B','C']
const VDOM = (
    <div>
        <ul>
            {
                data.map((item,index)=>{
                    return <li key={index}>{item}</li>
                })
            }
        </ul>
    </div>
)
ReactDOM.render(VDOM,document.getElementById('test'))
```

> 一定注意⚠️区分：【js语句(代码)】与【js表达式】

#### 2.1 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方

下面这些都是表达式：

- a

- a+b

- demo(1)
- arr.map() 

- function test () {}

#### 2.2 语句(代码)：

下面这些都是语句(代码)：

- if(){}

- for(){}

- switch(){case:xxxx}
