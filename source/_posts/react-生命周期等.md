---
title: react 生命周期等
date: 2020-06-09 15:11:53
tags: react
---

#### 生命周期

![

](../img/react/cycle.png)

###### 这个图好糊呀  反正记住 这主要的额三个就行了

**生命周期完整版如下**

- 挂载时
  - constructor()
  - render() 
  -  componentDidMount()
- 更新时
  - shouldComponentUpdate()  组件中数据更新时，return true 才会渲染
  - render()
  - componentDidMount()
- 卸载时
  - componentWillUnmount()
- 错误处理
  - componentDidCatch()

**comonentWillMount、componentWillUpdate、componentWillReceiveProps 已过时废弃**

#### 列表

列表循环必须加key属性 

 key属性对应的值尽量是独⼀⽆⼆的 

不要⽤列表序号做为key属性的值，除⾮万不得已

###### key 是用来在渲染页面时做 diff 算法的，一般选择一个独一无二的值，不推荐使用数据的下标 0,1,2...... 因为在做diff时候，会把key拿出来跟上次的对比，（如果有就直接拿出来用）如果那数组中的下标都是 0,1,2,3,4,5 无论数组中的内容怎么变化，所以这样就达不到对比的效果了，都会更新 例如原来数组 1,2,3 新数组 0,1,2,3 原来 1的下标为0key=0 现在 1的 key 为1，这就起不到diff 的作用了，相当于白加，它还会用它默认的算法来对比。

#### 表单

**受控组件**

state做为唯⼀数据驱动源，且⽤户对表单的操作需要反馈到 state

特点：表单的value由state决定，监听表单变化后从event获取新 value重新设置state

#### setState 同步与异步

## 为什么setState有异步更新？

React在执行setState之后，要执行render、diff、更新DOM等一系列操作，性能开销是比较大的。加入异步更新、更新合并等策略能优化性能。

## 什么时候异步更新，什么时候同步更新

组件里等事件处理程序，如 `onClick={this.handleClick}` 里面等setState是异步更新。声明生命周期函数里等setState也是异步更新。如果需要多次更新需要用异步设置等语法，如

```javascript
this.setState((state, props) => {
  return {count: state.count + props.count }
})
```

如setTimeout里，对原生绑定如 addEventListener 里，都是同步更新。

例如：

```javascript
document.querySelector("#btn").onclick=()=>{.....setState(...)}
                                                          
setTimeout(()=>{this.setState(...),this,setState(...)},0)                                                          
```



## 异步更新在效果上有什么表现

表现为更新合并，上次更新未生效。也就是如果你执行了两次 setState 它只会执行一次，因为它自己做了优化