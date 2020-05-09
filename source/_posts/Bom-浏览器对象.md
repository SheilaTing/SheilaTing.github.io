---
title: Bom 浏览器对象
date: 2020-05-09 09:53:25
tags: bom
---

#### 概念

##### BOM(Browser Object Model) 浏览 器对象模型，提供了独立于内容的、 可以与浏览器窗口进行互动的对象结构

##### window

- var声明的全局变量和functin声明的函数是window的属 性
- let 、const声明的变量不是

##### navigator

###### Navigator 对象包含有关浏览器的信息。

```javascript
window.navigator.userAgent

//Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like //Gecko) Chrome/81.0.4044.138 Safari/537.36
//可以用来判断用户的设备信息
const isIphone = ()=> /iphone/i.test(navigator.userAgent);
```

#### screen

```
screen.width
screen.height
screen.orientation.type  //方向landscape-primary横屏 portrait-primary 竖屏
screen.orientation.onchange = function(){
	console.log(screen.orientation.type)
}
```

##### 尺寸

```javascript
document.documentElement.offSetHeight //页面文档高度
screen.height //屏幕高度
//浏览器视窗高度
document.documentElement.clientHeight
window.innerHeight
//距离最近是定位元素的祖先元素对象
element.offSetParent
//到距离最近的是定位元素的祖先元素的距离
element.offSetTop
//页面滚动的垂直距离
window.scrollY
document.documentElement.scrollTop
//获取元素在视窗中的位置
element.getBoundingClientRect()
//元素距离页面顶部的距离  元素距离窗口的距离加上滚动的距离
element.getBoundingClientRect().top + document.body.scrollTop
//判断元素是否出现在窗口视野
function isShow(dom){
    //两种情况
  var res = false;
  if(dom.getBoundingClientRect().y<0){
      //判断元素是在窗口上部还是下部，如果是上部，则它距离窗口的距离会小于0
      //并且如果它距离窗口的距离是个负数加上它本身的高度大于0，它就会出现在视窗中
    if((dom.getBoundingClientRect().top+dom.getBoundingClientRect().height)>0){
      res = true;
    }
  }else if(dom.getBoundingClientRect().y<window.innerHeight){
      //在窗口下部，如果元素到窗口顶部的距离小于窗口的高度，它肯定出现在视窗中
    res = true;
  }
  return res;
}

```

##### 滚动

```javascript
//水平滚动到哪里
window.scrollTO({
	top:100,
	left:0,
	behavor:'smooth'
})
//水平滚动多少
window.scrollBy({
	top:window.innerHeight,
	left:0,
	behavior:'smooth'
})
//回到顶部效果
  let $toTop = document.querySelector('.btn-to-top')
    window.onscroll = function() {
      if(window.scrollY > 150) {
        $toTop.classList.add('show')
      } else {
        $toTop.classList.remove('show')
      }
    }
    $toTop.onclick = function() {
      window.scrollTo({
        top: 0,
        behavior: 'smooth'
      })
    }
```

##### fullpage滚动的效果

###### 思路：监听用户键盘按下事件，如果按的是向下的箭头，则将滚动条滚动一整个窗口的高度

```javascript
  window.onkeydown = function(e) {
        console.log(e)
        if(e.key === 'ArrowDown') {
          e.preventDefault()
          window.scrollBy({
            top: window.innerHeight,
            behavior: 'smooth'
          })
        }else if(e.key === 'ArrowUp') {
          e.preventDefault()
          window.scrollBy({
            top: 0 - window.innerHeight,
            behavior: 'smooth'
          })          
        }
      }
//按向下键，向下滚动一屏的高度，向上键滚动-一屏的高度
```

#### URL编码解码

- encodeURI()  不会对 : / ? & = # 编码
- encodeURIComponent()   会对 : / ? & = # 编码

#### cookie

- cookie是存储在浏览器上的一小段数据，用来记录某些当页面关闭或者刷新后 仍然需要记录的信息。在控制台用 「document.cookie」查看你当前正在浏览的网 站的cookie
- cookie可以使用 js 在浏览器直接设置（用于记录不敏感信息，如用户名）, 也 可以在服务端通使用 HTTP 协议规定的 set-cookie 来让浏览器种下cookie，这是 最常见的做法。
- cookie可以使用 js 在浏览器直接设置（用于记录不敏感信息，如用户名）, 也 可以在服务端通使用 HTTP 协议规定的 set-cookie 来让浏览器种下cookie，这是 最常见的做法。
- 一般浏览器存储cookie 最大容量为4k，所以大量数据不要存到cookie。
- 设置cookie时的参数：
  - path：表示 cookie 影响到的路径，匹配该路径才发送这个 cookie。 
  -  expires 和 maxAge：告诉浏览器 cookie 时候过期，maxAge 是 cookie 多久后过期的相对时间。不设置这两个选项 时会产生 session cookie，当用户关闭浏览器时，就被清除。一般用来保存 session 的 session_id。 •
  - secure：当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效 
  - httpOnly：浏览器不允许脚本操作 document.cookie 去更改 cookie。一般情况下都应该设置这个为 true，这样可以 避免被 xss 攻击拿到cookie。

#### session

- 创建session后，会把关联的session_id 通过setCookie 添加到http响应头部中。 
- 浏览器在加载页面时发现响应头部有 set-cookie字段，就把这个cookie 种到浏览器指定域名下。 
- 当下次刷新页面时，发送的请求会带上这条cookie， 服务端在接收到后根据这个session_id来识别用户。
- cookie 是存储在浏览器里的一小段「数据」，而session是服务器用来 识别身份的一段数据，有时候也特指身份识别机制

#### localStorage

- localStorage HTML5本地存储web storage特性的API之一，用于将大量数据（最大5M）保存在 浏览器中
- 保存后数据永远存在不会失效过期，除非用 js手动清除
- 不参与网络传输

```javascript
let information = {name: 'hunger', friends: ['Bob', 'Alice']}
localStorage.detail= JSON.stringify(information)
localStorage['age'] = 20
localStorage.setItem('sex', 'male')
localStorage.removeItem('sex')
console.log(localStorage.getItem('age'))
let oldInfo = JSON.parse(localStorage.detail)
oldInfo.friends.push('David')
localStorage.detail= JSON.stringify(oldInfo)
localStorage.clear()
//注意只能存字符串；
```

