---
title: dom 相关操作
date: 2020-05-02 17:29:26
tags: dom
---

#### DOM

文档对象模型是HTML的编程接口。用对象和HTML结构一一映射，通过修改对象达到修改HTML节点的目的。

document 的基本信息

- document.head   获取文档的头部
- document.body
- document.title
- document.location   地址跳转相关操作

​        document.location.href

​		 document.location.hash

​         document.location.search

​		 document.location.reload()

​		 document.location.assign('https://jirengucom');

```javascript
var url = document.createElement('a');
url.href = 'https://developer.mozilla.org/en-US/search?q=URL#search-results-close-container';
console.log(url.href);      // https://developer.mozilla.org/en-US/search?q=URL#search-results-close-container
console.log(url.protocol);  // https:
console.log(url.host);      // developer.mozilla.org
console.log(url.hostname);  // developer.mozilla.org
console.log(url.port);      // (blank - https assumes port 443)
console.log(url.pathname);  // /en-US/search
console.log(url.search);    // ?q=URL
console.log(url.hash);      // #search-results-close-container
console.log(url.origin);    // https://developer.mozilla.org
```

<!--more-->

#### Element

Dom 元素对象   常见属性如下：

- node.nodeName：元素标签名，还有个类似的tagName 
-  node.nodeType：元素类型 • node.className：类名 
-  node.id：元素id 
-  node.children：子元素列表（HTMLCollection） 
-  node.childNodes：子元素列表（NodeList） 
-  node.firstChild：第一个子元素 
-  node.lastChild：最后一个子元素 
-  node.nextSibling：下一个兄弟元素 
-  node.previousSibling：上一个兄弟元素 
-  node.parentNode：父元素

#### 获取元素

```javascript
//ES3写法，不推荐
document.getElementById('id') //Element
document.getElementsByClassName('class') //HTMLCollection
document.getElementsByTagName('p') //HTMLCollection
document.getElementsByName('username') //NodeList
//ES5写法，推荐
document.querySelector('.box') //Element
document.querySelectorAll('.box') 
//任何元素都可以执行 querySelector()
---getElementsByClassName获取的是一个类数组对象，不是一个数组
----querySelector的参数是一个任意合法的CSS3选择器
----querySelectorAll的到的是一个类数组对象
----NodeList和HTMLCollection的差异是前者有forEach方法
```

#### 遍历类数组对象

###### HTMLCollection没有forEach方法

```javascript
//NodeList可以直接遍历
document.querySelector('.box').forEach(node => {
console.log(node.className)
})
//HTMLCollection需要转换为数组才能用forEach
Array.from(document.getElementsByClassName('box')).forEach(node => {
console.log(node.className)
})
[...document.getElementsByClassName('box')].forEach...
```

#### DOM事件

###### 事件绑定

- node.onclick = function(e){} 
- node.addEventListener('click' , function(e){})
- 在函数里面可以使用事件对象e 
-  函数里面的this代表node本身

#### class操作  常见API

- node.className 老方法，用起来不严谨，尽量不用 
-  node.classList.add 
-  node.classList.remove 
-  node.classList.contains 
-  node.classList.toggle

```javascript
<div class="box"></div>
<button>切换</button>
<style>
.box { width: 100px; height: 100px; background: red; }
.box.active {background: yellow;}
</style>
<script>
const button = document.querySelector('button')
const box = document.querySelector('.box')
button.onclick = function() {
box.classList.toggle('active')
}
</script>
```

#### 属性操作  常见API

- node.getAttribute('id')   此种获取属性的方式获取的是写在 html上的属性例如：div id=xxx
- node.setAttribute('data-id' , '100')   //这种方式设置的话，好像是临时加在 html上的
- node.removeAttribute('checked') 
- document.createAttribute('checked') 
- node.setAttributeNode(attrNode)

#### 元素的创建 常见 API

- document.createElement('div') • 创建DOM节点，参数是标签名 
- document.createTextNode("Hello") • 创建文本节点，参数是字符串 
- document.createDocumentFragment() • 创建一个虚拟的DOM，用于提升性能，避免高频DOM操作

#### 元素复制添加修改删除 常见API

- parentNode.appendChild(childNode) • 在父亲的末尾添加元素 ü
- parentNode.insertBefore(newNode, referenceNode) • 把newNode插入到referenceNode元素之前 
- parentNode.replaceChild(newChild, oldChild) • 把oldChild替换为newChild 
- parentNode.removeChild(childNode) • 从parentNode里删除childNode 
- node.cloneNode(true) • 克隆一个元素，参数true的时候会深复制，也就是会复制元素及其子元素，false 的时候只复制元素本身

#### 修改样式    修改Style 属性

```javascript
document.querySelector('p').style.color = 'red'
document.querySelector('.box').style.backgroundColor = '#ccc'
function css(node, cssObj) {
Object.assign(node.style, cssObj)
}
css(document.body, {
     color: 'red',
     backgroundColor: 'blue'
})
//一般通过添加删除class的方式修改样式
```

#### 获取样式

##### # 使用getComputedStyle获取元素计算后的样式，不要通 过 node.style.属性 获取

```javascript
const node = document.querySelector('p')
const color = window.getComputedStyle(node).color
console.log(color)
```

#### 案例 checkbox 的全选

```javascript
//html 结构
 全选 <input class="all" type="checkbox">
  <ul>
    <li class = "item"><input type="checkbox"> a</li>
    <li class = "item"><input type="checkbox"> b</li>
    <li class = "item"><input type="checkbox"> c</li>
    <li class = "item"><input type="checkbox"> d</li>
  </ul>

//js代码 正确的方式：
//1.获取要点击的元素对象
var all = document.querySelector('.all');
//获取要选中的列表
var allItems = document.querySelectorAll('.item)
//绑定事件
 all.onclick = function(e){
    //判断全选按钮是否被勾选，如果被勾选了，则选中所有子元素
    if(all.checked){
        allItems.forEach(node=>{
            node.querySelector('input').checked = true
        })
    }else{
        allItems.forEach(node=>{
            node.querySelector('input').checked = false
        })
    }
}

//这种写法仅供参考，可以提供一种思路
var checked = false; //默认不被选中
all.onclick = function(e){
    //判断是否被选中，假如没有.checked 属性
    //点击时候如果没被选中，则将items都选中（因为点击后状态就被选中了）
  	if(!checked){
        allItems.forEach(node=>{
            node.querySelector('input').checked = true
        })
        checked = true; //更新勾选状态
    }else{
        //取消勾选
       allItems.forEach(node=>{
            node.querySelector('input').checked = false
        })
        checked = false;
    }
}

//
```



​		

















