---
title: react-高阶
date: 2020-06-10 16:33:42
tags: react
---

#### 代码分割

###### 动态加载js 或者组件文件

```javascript
//点击按钮才会动态引入文件，不会再刚加载时就引入 
handleClick=()=>{
        import ('./advance/math').then((math)=>{
            //使用 引入来的 js 文件中的功能
          this.setState({res:math.add(3,4)})
        })
    }
```

###### 懒加载 可以做到优雅降级，在引入文件未完全加载进来时，可以在页面展示一些预处理页面，例如Loading 等，使用时需要 suspense 标签包裹

```javascript
const Button = React.lazy(()=> import('./Button'));

 <Suspense fallback = {<div>Loading......</div>}>
      <Button/>
 </Suspense>
```

**Fragments**

###### react 要求我们的组件中的jsx 只能拥有一个顶级标签，为了解决这个问题我们可以使用一个空标签或者 Fragment 标签包裹（这个标签不会再页面上展示，仅起到语法作用）

#### Context

```javascript
function 组件写法  注意 function 方式的组件不用继承 component,手写 react时候想想是怎么处理的，使我们手动给它创造了一个 class
function 组件名称(props){
	......
}

也可以写成,一样的
const 组件名 = props => {......}

```

###### react 传递数据，父组件向子组件传值通过 props 属性，层层传递

###### 层层传递 如果层级太多，代码冗余

context 传递方式

 ```javascript
const ThemeContext = React.createContext();
// 在传递的组件地方 使用 这个标签 themeContext.provider 包裹 提供要传递的 value
<ThemeCOntext.Provider value="传递的内容">
 <组件/>   // 组件本身呢里面也包含组件a,b,c，d,我们要把这个值传递到 d上面，那么我们就在 d 中接收
 </ThemeContenxt.Provider>

// 在接收的组件内部定义 一个静态属性 等于这个标签对象，直接使用 this.context 获取
在 d中 定义 static abc = ThemeContext
使用 this.context 获取到传递过来的内容

// 注意，如果是用 function 方式写的，有一点不同之处在于，接收方无需定义 静态属性指向这个标签，直接 在 return 中使用 <ThemeContext.Consumer>{val=>(包裹这个组件的内容并且这是一个回调箭头函数,val 就是传递来的对象)}</Th>eContext.Consumer>  例如：
const Button = (props)=>{
    return(
    	<Theme.context.consumer>
        {val=>(<div>
         	val.a,val.c......
         </div>)
        }
        </Theme.context.consumer>
    )
}
 ```

#### 数据传递

###### 子组件向父组件传值

###### 当子组件中的数据发生变化时，调用父组件中的方法来改变，这种方式的前提是父子组件的数据源都来自于 state 中。例如：

```javascript
class 父组件{
    return (
    	<div>
         	<Son value={this.state.son} onChange=(e) => {this.setState({first:e.target.value})}/>
			<p>son : {this.state.son}</p>
         </div>
    )
}

//first 组件
function first(props){
    return (
    	<input value = {props.value} onChange={props.onChange}>
    )
}

// 刚开始子组件的值是父组件通过 props 传递来的，当子组件的值发生改变时调用传递来的onchange方法，也就是父组件的 oncahnge,这个方法将子组件现在的值更新到 state 中，然后父组件中更新，又传递给子组件，达到了父组件也更新的目的
```

#### 错误边界





#### 非受控组件与Refs

如何获取要操作的这个 dom?

**第一步：** 在组件内部创建一个 React.createRef() 对象，绑定到当前组件上，也就是赋值给 this.属性（在构造函数中），然后下次就可以通过 this.属性获取到这个对象了，然后在 render() 中，一般都在这里返回 dom 的，我们在需要操控的dom上加上 ref = {this.属性}

在需要时，就能通过 this.属性.current.  什么什么 -----  获取到这个对象了