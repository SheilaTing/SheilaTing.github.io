---
title: mock数据
date: 2020-05-19 16:35:44
tags: mock
---

#### 简单的 node.js 服务器

```javascript
// Node.js 模拟 server

const http = require('http')
const fs = require('fs')
const url = require('url')

var server = http.createServer((req,res)=>{
    //解析url 将url解析成对象
    let urlObj = url.parse(req.url);
    // console.log(urlObj)
    //如何在服务器中展示一个我们已经写好的html页面呢？
    //使用fs模块解析该文件，res返回即可
    let file =  fs.readFileSync(__dirname+'/index.html');
    //处理用户请求
    if(urlObj.pathname === '/getWeather'){
        res.end(JSON.stringify({data:'晴朗'}))
    }else{
        res.end(file)
    }
});
server.listen(8888);
```

**对应的html代码**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h2 class = "weather">天气：<span></span></h2>
</body>
<script>
    let $ = s => document.querySelector(s);
    let xhr = new XMLHttpRequest();
    xhr.open('GET','http://localhost:8888/getWeather',true);
    xhr.onload = ()=>{
        $('span').innerText = JSON.parse(xhr.responseText).data;
    }
    xhr.send();
</script>
</html>
```

###### 详解步骤

- 使用node.js 创建一个 httpServer,监听端口 8888
- 为了让我们已有的html在服务器中运行，在服务器的代码中使用 fs模块取读文件，然后将结果返回到客户端，于是展现了我们的html 页面
- html页面中的 javascript 代码中又向我们的server中发送了一个请求，server中接收到请求后根据请求的路径进行相应的响应处理

#### 更完善的 server

<!--more-->

```javascript
// Node.js 模拟 server
const http = require('http')
const fs = require('fs')
const url = require('url')

http.createServer((req,res)=>{
    //设置 content-type 防止乱码
    res.setHeader("Content-type","text/html;charset=utf8");
    //将请求的 url解析成对象  请求的路径
     let pathObj = url.parse(req.url,true);
     //对不同的路径做不同的处理
     switch(pathObj.pathname){
         case '/getWeather':
             if(pathObj.query.city === 'beijing'){
                res.end(JSON.stringify({city:'beijing',data:'晴朗'}))
             }else{
                 res.end(JSON.stringify({city:pathObj.query.city,data:'unknown'}))
             }
             break;
             default :
             try {
                 //判断请求路径是否是根路径，如果是就显示index.html的内容
               let pathname =  pathObj.pathname === '/' ?'index.html':pathObj.pathname;
               //__dirname 当前文件在磁盘的绝对路径
               res.end(fs.readFileSync(__dirname+pathname))
             } catch (error) {
                res.writeHead(404, 'Not Found')
                res.end('<h1>404 Not Found~</h1>')
             }
           
     }

}).listen(8888)
```

**TODO 从实现简易 Server 到实现node后端 框架 Express.js **

https://github.com/jirengu/node-server

#### mock.js

常见用法

- @ctitle(3, 10)
- @cparagraph
- @cword
- @cname
- @integer(10, 100)
- @float(20, 30, 2, 3)
- @color
- @date
- @time
- @now
- @id
- @url
- @email
- @image('200x100')

#### Rap2

###### 可以到处接口规范，供前后端人员遵循

#### 接口规范

接口约定

- 当前接口的路径是什么？ 如 /auth/register
- 当前接口提交数据的类型是什么? 如
  - GET 获取数据
  - POST 提交或者创建
  - PATCH 修改数据，部分修改
  - DELETE 删除数据
  - PUT 修改数据，整体替换原有数据
- 参数类型/格式，比如
  - fromdata，或者 application/x-www-form-urlencoded
- 参数字段，及限制条件
- 返回成功的数据格式
- 返回失败的数据格式

例如，这个文档  https://www.yuque.com/docs/share/08fd7cfb-6716-4409-9b15-fd9e9d491f34



#### 接口测试

**curl 初步检测后端接口的准确性** 

```javascript
// GET请求
curl "http://rap2api.taobao.org/app/mock/244238/getWeather?city=beijing"
// -d 提交的参数，默认是POST请求
curl -d "username=aaaa&password=bbb" "http://rap2api.taobao.org/app/mock/244238/login"
// -i 展示响应头 如果用户已登录可获得服务器设置的cookie,供登录测试的接口使用
curl -d "username=hunger1&password=123456" "http://blog-server.hunger- valley.com/auth/login" -i
// -H 设置请求头
curl -H "Content-Type:application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' http://127.0.0.1:8000/login
// -X 设置请求类型
curl -d "username=aaaa&password=bbb" -X POST
"http://rap2api.taobao.org/app/mock/244238/login"
// -b 请求带上cookie
curl "http://blog-server.hunger-valley.com/auth" -b
"connect.sid=s%3AmeDbrn03UtTM8fqChaPQ20wmWlnKeHiu.e3uMtu7j1zQ1iNeaajCmxkYYG
Q%2FyHV1ZsozMvZYWC6s"
```

