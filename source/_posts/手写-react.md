---
title: 手写 react
date: 2020-06-06 16:07:31
tags: react
---

#### JSX

###### javascript 语法扩展，用于形象描述一段DOM结构

例如:

```jsx
const element = <h1 className="hello">Hello World</h1>
      // 这就是一行 jsx 代码
```

###### React 推荐使用JSX,但是很明显这么一行JSX 代码是无法在浏览器中运行的。那么我们使用 babel 打包工具来处理我们的代码，babel 会把jsx 转换为 React.createElement(tag,attr,children);那么显然这样的代码运行也会出错，因为我们并没有定义React对象，更没有createElement 方法，所以为了运行不报错，我们需要定义一个 React 对象，并创建一个 createElement 方法,而babel 帮助我们给这个方法提供了三个参数，这三个参数与页面上的jsx 代码一一对应，也就babel把我们页面上的jsx 代码转换为了这三个参数；这三个参数分别是 jsx 代码的 标签（顶级标签）, 以及标签上的属性，还有标签里面的内容（一个数组）; 

###### 那我们拿到这三个参数之后，自定义 createElement 方法把这三个参数作为一个对象返回。拿到这个对象后，我们是不是就可以通过某些方法来构造标准的html代码了。手写React 核心就在这块。



 webpack config.js 中配置了 js 或者 jsx文件，会使用 babel 来加载

 babel 会把代码转换为 let div = React.createElement(tag,attr,childrenNode)

 如果 createElement 方法不返回，那么 consoel.log(div) 就是 undefined

 所以 我们把 babel 帮我们生成的这个方法的三个参数，这三个参数分别对应我们 html 的 标签，属性，和内容（以及标签嵌套的字标签）

 所以我们把这些内容返回出去，那么 div 就可以接收到这三个参数，做进一步处理

 就变成 let div = { tag,attr,child }

那么很显然如果我们直接打开生成的 index.html 会报错找不到 React,所以，我们需要定义一个 React

 webpack config.js 中配置了 js 或者 jsx文件，会使用 babel 来加载

 babel 会把代码转换为 let div = React.createElement(tag,attr,childrenNode)

 如果 createElement 方法不返回，那么 consoel.log(div) 就是 undefined

 所以 我们把 babel 帮我们生成的这个方法的三个参数，这三个参数分别对应我们 html 的 标签，属性，和内容（以及标签嵌套的字标签）

 所以我们把这些内容返回出去，那么 div 就可以接收到这三个参数，做进一步处理

 就变成 let div = { tag,attr,child }

 那么很显然如果我们直接打开生成的 index.html 会报错找不到 React,所以，我们需要定义一个 React

现在 这个 div 通过 babel 以及 react.createElment 方法已经成为了一个 携带 tag,attr,children 的对象

那么我们就可以通过这三个对象，将这个div 像html 一样展示在浏览器中

具体就是，我们定义一个方法，把这个div 以及document.body 传递进去，经过处理

拿到 dom 也就是我们这个 div对象后，我们取出里面的 tag

判断dom 的类型,当这个对象没有被标签包裹时，它是一个 string 字符串，可以打印出来看看

如果是字符串，那么直接创建一个 textnode

当有标签包裹时，它是一个 object

处理 属性 传递 节点 和 vdom 上的属性。。。。。。

**## 实现自定义标签的渲染**

 前面我们通过 babel 已经实现了将 普通的 jsx 转换为 React.createElement(tag,attr,children) 

 然后拿到这个生成的虚拟dom,通过定义的 React.render() 方法将tag,attr,children 以标准的 html 的形式展示到了页面上

 那么下一步我们如果想在页面上展示自己定义的标签需要怎么做呢？也就是不是html 内置的这些标签，而是自定义的一些语义化的标签模块

 例如 <App></App> <Menue></Menue> ......

 根据 babel 的知识，我们知道，它会把这样的标签内容渲染称为 React.createElement(App,attr,children)

 注意这里的 App 不带引号，也就是说它不是一个html 标签，而是一个变量。如果我们直接把它放入页面势必会报错，因为我们没有定义 App

 这个对象，所以我们要定义这个对象 App 

 定义好这个对象之后，为了实现内容的渲染，我们可以在 App 对象中定义一个 render() 方法，返回我们要渲染的 html 或者 jsx;

 这样在我们执行 ReactDom.render() 方法时，如果遇到了这样自定义的标签，那么我们就可以 new 一个我们定义好的 APP 对象，然后调用这个对象的 render() 方法 返回一个 html或者 jsx dom,然后我们在解析这个 返回值 不就可以实现了吗

 好了，开始写。。。。。。



 在设置好 state 对象的值后，如果想要有效果需要重新渲染页面，具体就是拿到这个state 所在的组件对象，然后根据新的state 创建新的dom,然后做一下替换即可



 接下来我们要实现一个函数式的组件，也就是es3 的写法的函数 function 组件名,我来重新捋一遍哈,如果是我们自定义的标签而非是 html 标签，babel 会帮助我们转换为 React.createElement(tag,attr,children),这样的形式，那么这个 tag 就是一个变量，而非一个 html 字符串标签，我们需要定义这个 变量对象否则运行就会报错

 前面我们使用es6的方法定义了class 对象，那如果我们使用 es3 function 方法定义呢?那我们上面写的逻辑是获取该组件上的 render() 方法获取我们的 jsx dom,现在改为 function 写法，里面不能再嵌套方法了，只能使用.prototype 的方式添加这个函数，而我们也没有定义这个方法，该如果获取到这个 render 里面的内容呢?

 思路哈： 当我们监测到要渲染的是一个 function 的时候，我们来判断这个对象是一个 function 还是一个 class，根据我们 class 的写法，我们是让这个组件继承了 React.component 这个对象的，那也就意味着 class 对象的 prototype == React.component 对吧, 而 function 方法写的则不会有，所以可以根据这个来判断

 我们可以定义一个处理这个对象的方法，传递这个 tag 标签，返回一个 class 对象(后续逻辑需要)

 那么如果是 class 方式写的，就直接 new 这个对象即可，如果是 function 方法写的，我们需要构造一个render() 方法，并且赋值给一个class，最后 new 这个 class,这样就解决啦,好啦，开写



