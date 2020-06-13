---
title: react hooks
date: 2020-06-11 12:02:25
tags: react
---

#### 作用

###### 让函数组件彻底取代 class 组件，解决函数组件中使用 state,生命周期函数，this的问题

**使用 reactHook(useState,useEffect) 解决这个问题**

**解决state**

```javascript
import React,{useState} from 'react';
fucntion App(){
    //组件内部useState() 返回一个数组，数组中分别是属性，set属性方法，传递的参数为属性的初始值
    // 此时该组件内部相当于有了一个 count 属性以及一个 setCount 方法，初始值为0;
    const [count,setCount] = userState(0);
    //改变 count 的值可以调用 setState(新值)即可，使用直接使用 count
      //可以有多个useState, 多个属性
    
}
```

**解决生命周期函数 useEffect**

```javascript
import React,{useState,useEffect} from 'react';
function Counter(){
//一个函数参数
    const [count,setCount] = userState(0);
    //这个函数相当于 componentDidMount和 componentDidUpdate
    useEffect(()=>{
        console.log('数据挂载或更新时执行')
    }); // 监控所有的 state 属性
//一个函数参数且该函数 return 一个函数
      useEffect(()=>{
        console.log('state数据挂载或更新时执行');
        return ()=>{
            //相当于 componetWillUnmount,组件卸载时执行
            consoel.log('状态更新和写在组件时执行')
        }
    });
 //一个函数参数，一个数组参数
      useEffect(()=>{
        console.log('组件挂载或更新时执行')
    },[count]);
    //count 为 state 的属性，此时代表只监控count所在组件的状态，若为空数组，则不监控，若没有设置则监控state 中所有属性 也就是当这个属性的值改变时就会执行到该函数内部
   // 所谓卸载也就是不在引用这个state 属性了 可以这么理解
}
```

#### 实现一个简单的 useState

###### 模仿它的使用，定义一个函数 useState 传递参数，返回数组及方法

```javascript
let state;// 定义一个属性 注意要定义在外部，否则每次调用 setState 都会给初始值
function setState(inivalue){
    //因为如果多次使用这个 state,我们要保留 state 上一次的执行结果，所以要写到全局作用域中
    // 如果state 没有值就是用初始值 也就代表是第一次执行
    state = state || initvalue;
    function setState(newState){
        state = newState;
         render()  // ReactDom.render()
    }
   
    return [state,setState];
}
function render() {
 ReactDOM.render(
 <App />,
 document.getElementById('root')
 );
}
// 刚刚又犯迷糊了，函数的定义和返回结果是不一样的好吗，定义时只是形参，叫什么都行，调用时你是可以更改的呀 刚才傻逼似的 直接用返回值【state】 去了...... 而且数组只能用下标访问才对啊，怎么那么蠢
// 我可以直接用一个变量接收，那这个变量就是 一个数组，为了取出每一项，我可以数组【【0】赋值给一个值。。。。 为了方便可以直接使用 【a,b】 = 返回的值即可
// 例如 【a,b】= [1,3]  a=1,b =3; 啊啊啊啊啊
```

#### 实现一个简单的 userEffect	

#### 最主要的就是看传递来的属性值有没有改变过，改变过就执行 callback,否则就不执行

```javascript
let oldProps;
function useEffect(callback,props){
    //如果不是第一次执行callback并且传递了 props，那么oldProps 就会有值,此时对比新值，返回对比结果取反，(如果经过比较发生了变化，那么会返回 false,我们需要取反变为 true，这样才能继续执行 callback)如果不存在说明是第一次执行，那么 callback 肯定要被执行所以是 true
    // 也就是说第一次进来无论如何都会去执行 callback 的
    const hasChangeProps = oldProps ? !props.every(el,index=> el=== oldProps[index]) : true;
    //如果没有传递这个属性或者说 传递了这个属性而且值发生了变化 就会执行这个 callback函数
    // 这里对props 取反来判断，如果!props 为true，则代表 props 为空，妙啊
    if(!props || hasChangeProps){
         callback();
         oldProps = props;  //将新的属性值传递给一个变量保存
    }
}

// 如果传递的这个属性没有发生改变，那么久不会执行 callback
// 因为第一次进来这个属性假如是1，再次进来还是1， 它的值没有变过，自然不会执行(第一次还是会执行的)
```

#### reactHook-useState 的完整实现

###### 之前的之所以说是简单实现是因为我们只定义了一个 全局变量，加入我们有多个 useState,useEffect,那么这个变量就会被覆盖

```javascript
let memorizState = []; // 用来保存不同的 state
let cursor=0; //用来指向当前数组中操作的属性下标
function useState(initvalue){
    //将初始值赋值到数组中
    memorizeState[cursor] = memorizeState[cursor] || initvalue;
    //保留当前下标到当前作用域中 (闭包)
    const currentCursor = cursor;
    function setState(newState){
         memorizeState[currentCursor] = new State;
        render();
    }
   return [memorizedState[cursor++,setState];
           //注意这里 cursor 会先取出 然后再++
}
```

#### useEffect 的完整实现

```javascript
function useEffect(callbcak,depArray){
    //取反，depArray 为空时，hasNoDep 才为true;
    const hasNoDeps = !depArray;
    const deps = memorizedState[cursor]; //将当前的state 属性复制给这个变量
    const hasChangeDeps = oldProps ? !deps.every(el,index=> el=== oldDeps[index]) : true;
    if(hasNoDeps || hasChangeDeps){
        callback()
        memorizeState[cursor] = depArray;
    }
    cursor++;
}
```



