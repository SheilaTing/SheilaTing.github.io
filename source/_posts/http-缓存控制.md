---
title: http 缓存控制
date: 2020-05-15 12:40:42
tags: http

---

#### node.js 静态服务器

```javascript
// Node.js实现简单 http文件服务器
//引入需要的包
const http = require('http')
//文件处理包
const fs = require('fs')
//路径处理包
const path = require('path')

//服务器就是一段一直运行在某个端口的代码
http.createServer((req,res)=>{
    console.log('path:===='+__dirname)
    //_dirname 就是当前路径
    fs.readFile(path.join(__dirname,req.url),(err,data) =>{
        if(err){
            res.writeHead(404,'not found')
            res.end('oh,not found')
        }else{
            res.writeHead(200,'ok')
            res.end(data)
        }
    })
}).listen(5000)  //监听5000 端口
```

#### 缓存控制

###### 为了更好的提升用户体验或者说为了节省流量，设置缓存 观看浏览器中的network中sizing字段的值，判断是走缓存还是从服务器请求来的数据。

**eg1 Expire 设置过期时间  res.setHeader('Expires','"Fri, 15 May 2020 05:00:39 GMT"');**

###### 返回数据之前先给浏览器或者客户端返回一个过期时间，它们拿到这个时间后，会存储在本地，每次再请求这个资源时，如果在这个时间内即当前时间小于这个过期时间，就从本地缓存中读取，不再向服务器发请求,这句话会在resposeHeader中设置一个过期时间字段，值是我们设置的值 

**eg2 pragma(pragmatic) 设置浏览器或客户端不读取缓存内容每次都向服务器发请求 res.setHeader('Pragma','no-cache');**

###### 这种情况下，服务器发给浏览器或客户端内容时，客户端还是会缓存，但它不会从缓存中拿

**如果 expires 和 pragma 同时存在， pragma 优先级较高；**

**获取 GMT(格林尼治时间 标准时间)方式  newDate().toGMTString();**

**以上为http version 1 中的内容，下面会对以上内容进行补充**

###### 只设置过期时间的话有一个问题，就是客户端的时间是可以随意更改的，如果客户端更改了时间一直小于这个过期时间，那我们是不是就永远也拿不到最新的服务器内容了，所以引出了下面这个字段来控制浏览器缓存时间

#### Cache-control 它的值有：

**eg1 res.setHeader('Cache-Control','max-age = 10')**

###### max-age 表明距离上次请求的时间为10s,即 10s之内你请求这个文件走缓存，之后重新发请求；假如请求了服务器并在a时刻返回响应结果，则在max-age规定的秒数内，浏览器将不会发送对应的请求到服务器，数据由缓存直接返回；超过这一时间段才进一步由服务器决定是返回新数据还是仍由缓存提供

**max-age 和 expire 同时存在的话，max-age 优先级较高**

**eg2  res.setHeader('Cache-Control','no-cache')**

###### 请求时，浏览器还是会在本地缓存一份，但不会从缓存中取，每次都向服务器发请求要最新的，而最终服务器是否会返回你最新的，看服务器端的处理了，如果服务器端检测到这个文件没有改变，可能会返回客户端一个状态码比如304，告诉客户端，文件内容没变，你从缓存中拿

**eg3   res.setHeader('Cache-Control','no-store')****

###### 请求时，浏览器收到服务器返回的数据，不会再本地缓存一份，所以更无所谓说从本地取了，每次都会请求最新的

**cache-control 的缺陷之处，在于如果服务器中的文件没变，起始就没必要每次都发文件给客户端了，多浪费流量啊，所以引出了 last-modified 服务器端做处理，如果客户端请求的文件内容改变了，再发给客户端**

<!--more-->

#### Last-Modified

```javascript
//引入需要的包
const http = require('http')
//文件处理包
const fs = require('fs')
//路径处理包
const path = require('path')

//服务器就是一段一直运行在某个端口的代码
http.createServer((req, res) => {
    console.log('path:====' + __dirname)
    let filePath = path.join(__dirname, req.url);
    fs.readFile(path.join(__dirname, req.url), (err, data) => {
        if (err) {
            res.writeHead(404, 'not found')
            res.end('oh,not found')
        } else {
            //注意，要设置 no-cache,因为浏览器默认会从缓存找
            res.setHeaders('Cache-Control','no-cache');
            //Last-Modified 
            //判断用户请求的文件是否被修改过，修改过才发给客户端，以index.css为例子
            //localhost:5000/index.css 
            let mtime = Date.parse(fs.statSync(filePath).mtime);  //获取文件的修改时间
            //查看客户端来的请求头上是否有 if-modified-since字段
            if (!req.headers['if-modified-since']) {
                //没有，说明是第一次请求，将文件修改时间返回给客户端响应头
                res.setHeader('Last-Modified', new Date(mtime).toGMTString());
                res.writeHead(200, 'ok')
                res.end(data)
            }else{
         //如果有,判断现在的时间是否大于上次修改的时间，如果是，更新文件修改时间，并返回最新的
                let oldTime = Date.parse(req.headers['if-modified-since'])
                if(mtime>oldTime){
                    res.setHeader('Last-Modified', new Date(mtime).toGMTString());
                    res.writeHead(200, 'ok')
                    res.end(data)
                }else{
          //如果不大于或者等于之前的时间，说明文件未被修改过，返回一个状态码，让浏览器去缓存中取
                    res.writeHead(304);
                    res.end();
                }
            }

          //  res.writeHead(200, 'ok')
           // res.end(data)
        }
    })
}).listen(5000)
```

###### 设置 cache-control 为 no-cache,这样浏览器或者客户端每次都会向服务器发请求要最新的数据或文件，这个时候，判断请求头中是否有 if-modified-since,这个字段如果服务器有返回给客户端过 last-modified字段，则后面每次请求都会带上该字段，如果没有，则证明是第一次请求这个文件，服务器获取文件的修改时间，然后返回给浏览器，下次有了该字段，判断该文件的修改时间是否大于传递来的时间，如果大于则证明文件被修改过了，返回给客户端最新的文件，如果不大于，则证明文件未被修改，返回给客户端一个状态码304即可，让客户端取缓存中取数据；

**问题来了，我们上面判断文件是否修改，是根据修改时间来判断的，那如果我只是简单更改了分好号又添加上，时间也会变化，但真正的内容并没有改变，所以严谨一点应该判断文件的内容是否改变**

#### E-tag 字段

**其实，last-modified,e-tag,只是在浏览器决定不走缓存的时候，向服务器要数据，服务器决定如何给客户端数据的设定，所以测试时要加上 cache-control :nocache ,否则默认会从缓存拿，而你不设置 no strore 的话，每次请求成功都会在客户端缓存一份**

###### e-tag 这个字段中，其实是设置一个特殊的字符也好，什么也好，只要能够表明文件是否修改的标识符即可，例如，你可以将文件的md5值得 base64编码存储在这个字段中，如果文件变了，这个值也会变，那根据这个来判断是否返回给客户端最新的文件

```javascript
   
const http = require('http')
//文件处理包
const fs = require('fs')
//路径处理包
const path = require('path')
//node.js 加密包
const crypto = require('crypto')

//服务器就是一段一直运行在某个端口的代码
http.createServer((req, res) => {
    console.log('path:====' + __dirname)
    let filePath = path.join(__dirname, req.url);
    fs.readFile(path.join(__dirname, req.url), (err, data) => {
        if (err) {
            res.writeHead(404, 'not found')
            res.end('oh,not found')
        } else {
let oldEtag = req.headers['if-none-match']
            //如果不存在
            if(!oldEtag){
                let md5 = crypto.createHash('md5')
                res.setHeader('Etag',md5.update(data).digest('base64'))
                res.writeHead(200,'ok')
                res.end(data)
            }else{
                // 如果存在,获取文件当前的md5值
                let newEtag = crypto.createHash('md5').update(data).digest('base64')
                if(oldEtag!== newEtag){
                    //对比发现不一样返回最新的
                    res.setHeader('Etag',newEtag);
                    res.writeHead(200,'ok')
                    res.end(daat)
                }else{
                    //一样的话，直接返回状态码，无需返回数据，节省流量
                    res.writeHead(304)
                    res.end();
                }
            }
     }
    })
}).listen(5000)
```



#### 下面说明几种情况：

前端层面对刷新缓存的控制，通过改变请求的ur来刷新缓存 每次打包上线把包的名字改掉，url变后，本地没有缓存，自然是请求最新的；

一、如果单独使用max-age，在缓存有效的时间内浏览器对相同的资源发起请求是得不到更新的、为了解决这个问题，可以在每次编译时给静态文件设置hash值，这样，在浏览器再次对静态资源发起请求时，资源的名称已经修改了，浏览器不会认为是同一资源，也就不会去缓存中寻找，直接去源服务器中获取

二、如果设置了no-cache，在缓存有效的时间内，浏览器对相同的资源发起请求时，会先去服务器询问资源是否修改过，资源没有修改时，响应304 Not Modified，浏览器得到304响应后，判断缓存可以使用，就会直接使用缓存。如果资源更新过，就返回200，浏览器使用返回的资源。

当设置了no-cache时，需要有一种验证机制来判断资源是否更新过：

1. Last-Modified配合If-Modified-Since或者If-Unmodified-since使用：对比上次修改时间来验证是否使用缓存。步骤是：client第一次对资源发起请求时，server会设置Last-Modified的头信息，当浏览器再次对相同的资源发起请求时，浏览器会设置request的头部(chrome可以使用disable cache来禁止浏览器自动设置，这样每次都会去源服务器获取)，设置If-Modified-Since或者If-Unmodified-since的值为之前接收到的Last-Modified的值，这样，server每次取得头部信息If-Modified-Since或者If-Unmodified-since，与文件的修改时间进行对比，如果相同，就返回304Not Modified，client使用缓存，如果不相同，就返回更新后的资源。
2. Etag 数字签名配合If-Match或者If-None-Match使用：一般的数字签名可以对资源的内容进行hash计算，client第一次请求资源时，在server中设置头部信息Etag，值为hash值，当浏览器再次对相同的资源发起请求时，浏览器会设置request的头部，设置If-Match或者If-None-Match的值为之前接收到的Etag的值，这样，server每次取得头部信息If-Match或者If-None-match，与重新计算的hash值进行对比，如果相同，就返回304Not Modified，client使用缓存，如果不相同，就返回更新后的资源。