---
title: js 事件
date: 2020-05-12 16:29:17
tags:
---

常用事件

鼠标事件、键盘事件、拖放事件、媒体事件、进度事件、存储事件，更新事件等

单击，双击，鼠标放置，表单内容发生变化，拖拽，页面滚动。。。。。

#### dom事件流

三个阶段

- 事件捕获阶段
- 处于目标阶段
- 事件冒泡阶段

#### 事件处理程序

当事件发生时执行的函数

三种绑定方式

1. 在html 中绑定
2. 给dom元素的 onclick 赋值
3. 调用dom的 addEventlistener函数

###### addEventListener

target.addEventListener(type, listener, options)

- type:事件类型
- listener:事件处理方法
- options: 默认全是false {capture:是否捕获阶段监听，once:只监听一次，passive:是否忽略 preventDefault}

target.addEventListener(type, listener, useCapture)

useCapture: 可选，true表示在捕获阶段调用listener，false表示冒泡阶段处理

target.removeEventListener

- target.removeEventListener('click', handler)
- target.removeEventListener('click', handler, true)

#### 冒泡与捕获

<!--more-->

```html
<div class="parent">container
<div class="child">box
<div class="target">target</div>
</div>
</div>
<style>
div {
border: 1px solid;
margin: 10px;
}
</style>

<script>
    const $ = s => document.querySelector(s)
$('.parent').addEventListener('click', function(e){
console.log('在捕获阶段，parent 开始处理')
}, true)
$('.child').addEventListener('click', function(e){
console.log('在捕获阶段，child 开始处理')
//e.stopPropagation()
}, true)
$('.target').addEventListener('click', function(e){
console.log('在捕获阶段，target 开始处理')
}, true)
$('.parent').addEventListener('click', function(e){
console.log('在冒泡阶段，parent 开始处理')
}, false)
$('.child').addEventListener('click', function(e){
console.log('在冒泡阶段，child 开始处理')
}, false)
$('.target').addEventListener('click', function(e){
console.log('在冒泡阶段，target 开始处理')
}, false)
</script>

// e.stopPropagation() 停止事件传播
//在事件的传播阶段可以使用上面方法停止传播
// 事件的冒泡指的是：当你点击子元素的时候其实整个窗口都收到了这个点击事件，只是我们只给这个元素添加了事件响应的函数代码而已，事件会从最外层的html一层层向内传递，然后再冒泡到最外层
```

##### 阻止默认事件 e.preventDefault()

- 链接不自动跳转
- 表单不自动提交

##### 事件代理

1. js中事件冒泡我们知道，子元素身上的事件会冒泡到父元素身上。
2. 事件代理就是，本来加在子元素身上的事件，加在了其父级身上。
3. 那就产生了问题：父级那么多子元素，怎么区分事件本应该是哪个子元素的？
4. 答案是：event对象里记录的有“事件源”，它就是发生事件的子元素。
5. 它存在兼容性问题，在老的IE下，事件源是 window.event.srcElement，其他浏览器是 event.target
6. 用事件委托有什么好处呢？
7. 第一个好处是效率高，比如，不用for循环为子元素添加事件了
8. 第二个好处是，js新生成的子元素也不用新为其添加事件了，程序逻辑上比较方便

案例：

我们要实现点击某个元素，然后它的内容显示在文本框中，同时有一个可以创建新元素的功能，当我们为每个元素绑定这个事件后，发现我们创建的新元素却没有办法实现这个功能，因为在绑定事件的时候，这个元素都还没有，自然没办法应用这个函数。解决方法就是我们不直接给该元素绑定函数，我们给元素的父元素绑定事件，反正点击子元素的同时，会冒泡到父元素上，我们监听父元素，然后从点击事件的默认参数e中会发现有path，target等内容可以帮助我们找到我们实际要监听的对象，然后给他做处理即可；

```javascript
$container.onclick = function(e){
  console.log(e)
  let $box = e.path.find(el => el.classList
                         && el.classList.contains('box'))
  if($box) {
    $log.innerText = $box.innerText
  }
}
```

