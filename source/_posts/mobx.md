---
title: mobx
date: 2020-06-13 12:05:32
tags: mobx
---

#### 全局状态管理

  ###### 之前在 class 组件中通过 state 来实现状态管理，存储数据，在 function 组件中我们通过 useState 来实现状态管理

###### 上面这些都是父子关系之间组件传值可以通过共享state中的数据。如果层级嵌套十分严重，那么共享数据会变得十分复杂。

###### 假如我们可以把一些组件会公用的一些数据放在组件外面，那个组件需要就直接拿即可，就十分方便了，而且当我们修改了这些公用的全局的数据，对应的引用这个数据的组件也会自动更新。所以我们需要这个全局状态管理工具，react 中可以用 redux,mobx Vue 中有 vux.

#### mobx

**state**

###### state 中保存了我们要维护的一些数据，也就是一个数据容器

**Action**

###### 如果我们想要修改这个state中管理的数据，不建议直接修改它，要使用mobx 提供的一个action方法去修改，保证了数据的安全，也可以追溯用户的操作（打印日志等）

**computed value** 计算属性值

###### 当state中维护的数据发生改变时，就会引起 计算属性值得改变，也就是说这个 computed value 是根据这个 state 中数据的值来生成的。

**Reaction**s  反应

###### 当state 中数据发生改变时，如果此时用户需要在这个时候做一些事情，这些事情就是 reactions

#### 基本用法

```javascript
import {observable,computed,action,autorun} from 'mobx';
// todos 就是要监控的数据 通过 observale 函数来生成，相当于state中的数据
const todos = (observable([
    {
        title:'起床',
        completed:false
    },
      {
        title:'穿衣',
        completed:false
    },
      {
        title:'洗漱',
        completed:false
    }
]))

//Computed values 这个值是通过 state 中数据得来的，当state 数据更新时，它也会更新
// 通过 computed()函数，操作 state 中的数据
let uncompletedCount = computed(()=> todos.filter(todo=> !todo.completed.length);
//Reactions  当state 和 computedValue 改变时会触发 reactions
  autorun = (()=>{
    console.log(`剩余任务：${uncompletedCount}`
        todos
         .filter(todo => !todo.completed)
         .map(todo => todo.title)
         .join(", ")
    ),
})       

console.log(``,.....)  // console.log 可以这样写的
//这个方法就是 当state 和 computedvalue 变化时，会自动运行这个 autrun 函数，打印出state 中未完成的任务

// Actions  用来改变被观察者 也就是 state 中的数据
const doTask = action(()=>{
    todos.find(todo=>!todo.completed).completed = true;
})

// arry.find(item=>) 函数 find() 方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 undefined。  一次只返回一个值

// doTask 这个函数，因为action 本身是个函数赋值给 doTask 所以它也是个函数,只不过在action 参数中的函数中做了一些处理，来修改 state 也就是 todos 中的数据。                 
```

#### mobx 装饰器写法 (装饰器 是 ES2016 也就是 es7 的新语法,详情看 装饰器笔记)

```javascript
import { observable, autorun, computed, action } from 'mobx';
class Todo {
 @observable todos = []
 constructor() {
 autorun(() => {
 console.log(
 `剩余任务:${this.uncompletedCount}`,
 this.todos
 .filter(todo => !todo.completed)
 .map(todo => todo.title)
 .join(", ")
 );
 });
 }

 @computed get uncompletedCount() {
 return this.todos.filter(todo => !todo.completed).length;
 }
@action addTodo(title) {
 this.todos.push({
 title: title,
 completed: false
 });
 }
 @action doTask(){
 this.todos.find(todo => !todo.completed).completed = true
 }
}

let todo = window.todo = new Todo();
todo.addTodo('吃饭');
todo.doTask()

```

**核心思想：状态的改变引发⼀系列⾃动⾏为**

#### react 项目中使用装饰器

操作

```bash
create-react-app

yarn eject 或者 npm run eject 暴露出配置文件

yarn add @babel/plugin-proposal-decorators  //安装支持 装饰器的插件
修改package.json， 找到 babel字段， 添加
//查看 npm 上 plugin-proposal-decorators 包的用法
 "babel": {
 "plugins": [
 "@babel/plugin-proposal-decorators"
 ],
 "presets": [
 "react-app"
 ]
 }
```

#### react-mobx