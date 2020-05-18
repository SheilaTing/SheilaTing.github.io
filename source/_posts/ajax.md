---
title: ajax
date: 2020-05-18 16:23:21
tags: ajax
---

#### 与后端交互的方式

###### 1. Form 表单提交

###### 2. AJAX 

###### 3. WebSocket  可以由服务端主动发起

## **我们如何向服务器发送请求？**

1. 用 form 可以发请求，但是会刷新页面或新开页面
2. 用 a 可以发 get 请求，但是也会刷新页面或新开页面
3. 用 img 可以发 get 请求，但是只能以图片的形式展示
4. 用 link 可以发 get 请求，但是只能以 CSS、favicon 的形式展示
5. 用 script 可以发 get 请求，但是只能以脚本的形式运行

有没有什么方式，可以使用get、post、put、delete 请求都行，并且用任意的方式返回给浏览器展示？

**当然有，这个就是AJAX。**

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）

Jesse James Garrett 讲同时满足以下技术取名叫做AJAX:**异步的 JavaScript 和 XML。**

1. 使用 XMLHttpRequest 发请求
2. 服务器返回 XML 格式的字符串 (当然现在基本上都是用json来传递数据了)
3. JS 解析 XML，并更新局部页面

ajax使用内置的XMLHttpRequest 和 fetch 对象，实现和服 务端进行数据交互

**发送请求四步走**

- 创建 xmlhttprequest对象
- 配置参数（请求携带的参数，参数类型等）
- 绑定事件 （请求的事件处理）
- 发送请求 （send()）

```javascript
let xhr = new XMLHttpRequest()
xhr.open('GET', url, true)
xhr.onload = function(){}
xhr.send()

// 完整例子 GET 请求，参数在url中拼接
let url = 'http://rap2api.taobao.org/app/mock/244238/weather?city=北京'
let xhr = new XMLHttpRequest()
xhr.open('GET', url, true)
xhr.onreadystatechange = function(){
if(xhr.readyState === 4) { // xhr.DONE === 4 也可
if((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304){
console.log(JSON.parse(xhr.responseText))
} else {
console.log('服务器异常')
}
}
}
xhr.onerror = function(){ // 可选
console.log('服务器异常')
}
xhr.send()
//或者
let url = 'http://rap2api.taobao.org/app/mock/244238/weather?city=北京' 
let xhr = new XMLHttpRequest()
xhr.open('GET', url, true)
// onload 就不用了再判断状态了
xhr.onload = function(){
if((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304){
console.log(xhr.response)
} else {
console.log('数据异常')
}
}
xhr.responseType = 'json' //兼容性稍微差一些
xhr.onerror = function(){
console.log('服务器异常')
}
xhr.send()
```

<!--more-->

#### POST请求

```javascript
let url = 'http://rap2api.taobao.org/app/mock/244238/login' let xhr = new XMLHttpRequest()
xhr.timeout = 3000 //可选，设置请求超时时间
xhr.open('POST', url, true)
//设置参数类型
xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded')
xhr.onload = function(){
if((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304){
console.log(JSON.parse(xhr.responseText))
} else {
console.log('响应异常')
}
}
xhr.ontimeout = function() { console.log('请求超时') }
xhr.onerror = function(){ console.log('服务器异常') }
xhr.send('username=hunger&password=12345678') //发送请求的时候携带请求参数
```

#### 封装

```javascript
const request = (url, params, onsuccess, onerror) => {
    url = url + '?' + Object.entries(params).map(arr => arr[0] + '=' + 		 arr[1]).join('&')
    let xhr = new XMLHttpRequest()
    xhr.open('GET', url, true) // 异步写法，最后一个参数是true
    xhr.onload = function(){
    if(xhr.status === 200 || xhr.status === 304) {
    onsuccess(JSON.parse(xhr.responseText))
	} else {
			onerror()
		}
	}
    xhr.onerror = onerror
    xhr.send()
}
//调用
    request('http://rap2api.taobao.org/app/mock/244238/weathe', {city: '杭州'}, data => {
        console.log('请求成功')
        console.log(data)
	}, () => {
		console.log('接口异常')
}
)

```

#### Post请求编码方式 告诉服务器请求参数的编码方式

###### application/x-www-form-urlencoded 参数变成 key1=value1&key2=value2 的形式

###### multipart/form-data  一般上传文件时使用

```javascript
let formData = new FormData()
formData.append('username', 'abcdefg')
formData.append('password', '123456')
let url = 'http://rap2api.taobao.org/app/mock/244238/register' let xhr = new XMLHttpRequest()
xhr.open('POST', url, true)
xhr.onload = function(){
if(xhr.status === 200 || xhr.status === 304) {
console.log(JSON.parse(xhr.responseText))
} else {
console.log('接口异常')
}
}
xhr.send(formData )  //使用formdata传递参数
```

#### 表单验证

```javascript
const $ = s => document.querySelector(s)
const $submit = $('[type=submit]')
const $form = $('form')
const $msg = $('#msg')
let url = 'http://rap2api.taobao.org/app/mock/244238/register'
//这里不直接给button绑定事件，而是给form表单绑定，反正会冒泡到表单上的
$form.onsubmit = function(e) {
e.preventDefault()
//这里校验，这里传递表单数据时，直接传递了表单对象
let formData = new FormData($form)
let xhr = new XMLHttpRequest()
xhr.open('POST', url, true)
xhr.onload = function(){
if(xhr.status === 200 || xhr.status === 304) {
let data = JSON.parse(xhr.responseText)
$msg.innerText = data.msg
} else {
$msg.innerText = '接口异常' }
}
xhr.send(formData )
}

```

#### fetch

简单使用，get请求

```javascript
let url = 'http://api.jirengu.com/getWeather.php?city=北京' fetch(url).then(response => response.json() )
.then(data => {
document.body.innerText = data.results[0].weather_data[0].weather
})
```

复杂使用 post 请求

```javascript
const data = { username: 'example' };

fetch('https://example.com/profile', {
  method: 'POST', // or 'PUT'
  headers: {
    'Content-Type': 'application/json',
  },// 'Content-Type': 'application/x-www-form-urlencoded',
  body: JSON.stringify(data), //body data type must match "Content-Type" header
})
.then(response => response.json())
.then(data => {
  console.log('Success:', data);
})
.catch((error) => {
  console.error('Error:', error);
});
```

#### 双工通信

**Ajax轮询**

每隔固定时间发一次请求

- 发请求，立即响应，返回空
- 发请求，立即响应，返回新数据

长轮询(Comet)

客户端 

-  发请求，等待响应 

- 当响应时，再次发请求

服务器端

- 请求到来，如果没新数据，则不发
- 当有新数据，需要通知客户端，再响应

实际场景

ajax轮询实现的简易聊天室

https://github.com/jirengu/chat-xhr-poll

ajax长轮询(comet)实现的简易聊天室

https://github.com/jirengu/chat-comet

websocket 简易聊天室

https://github.com/jirengu/chat-websocket