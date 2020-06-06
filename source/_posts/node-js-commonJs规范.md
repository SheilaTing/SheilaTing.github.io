---
title: node.js commonJs规范
date: 2020-06-01 11:28:42
tags: node.js
---

#### 定义

基于 Chrome V8 引擎的 javascript 运行环境。

可用于开发网站后台，开发本地工具，应用，特点是：事件驱动，非阻塞式IO模型，强大的生态NPM

#### 服务器端概念

**计算密集型  最好用 C C++ 来实现**

###### 大量的计算，小号CPU 资源，例如对视频进行解码，加密解密操作

**IO密集型 node.js 适合 io密集型应用**  

###### 网络、磁盘读写频繁，例如上传下载等功能，包括读写数据库

#### 使用

###### 登录 nodejs 官网下载安装

**使用node.js 内置模块**  

###### node.js 内置了许多处理模块，例如文件处理模块 filesystem,路径处理模块path，http模块等

使用如下

```javascript
//使用 require 关键字引入 文件处理模块
const fs = require('fs')
fs.readFile('path','编码'，callback(err,str){})
//具体使用查看 api
```

**使用本地模块**

```javascript
// 引入 util.js 后缀可以省略
const util = require('./util')
使用： util.fact(5)

//util.js
function fact(n){
   if(n===1) return 1
    return  n*fact(n-1)
}
```

**使用第三方模块**

node.js 有强大的生态系统 npm，上面有许多开发者上传的自己写的各种工具包，如果我们有需要可以从上面下载下来，作为第三方的模块来使用

###### npm install 模块

#### CommonJs 规范

###### 一个文件是一个模块

 	 文件中变量、函数、类都是私有的

###### 每个模块内部，module 代表当前模块

​	 	module.exports 是对外的接口。加载某个模块，其实是加载该模块的 module.exports

###### 注意 module.exports 与直接使⽤ exports的差异

```javascript
//a.js
 let num =1
 const add = ()=>++num
 module.exports.num = num
 module.exports.add = add   module.exports 可以把它当成一个对象
 
 //b.js
 let obj = require('./a.js')  //obj 对象就是你export 出来的 module.exports
 console.log(obj.num)  // 普通变量
console.log(obj.add())  //函数

//module.exports 也可以这样用
// test.js
module.exports = {
    sum:function sum(){
        return [...arguments].reduce((v1,v2)=>v1+v2)
    }
}

//引入使用也是一样的,记住你 export 出来的是什么引入的就是什么,module.exports 就是module 的一个属性
const m = require('./test.js')
m.sum(4,5)
//补充例子  n.js	
module.exports = {
    sum:function sum(){
        return [...arguments].reduce((v1,v2)=> v1+v2)
    },
    name:'zhangsan'
}
//引入
const m = require('./n.js')
n.sum(4,5)
n.name
```

#### exports 与 module.exports的区别

```javascript
//c.js
// 你可以理解为有这么一句话  exports = module.exports
function sum(){
    return [...arguments].reduce((v1,v2)=> v1+v2)
}
module.exports = sum  //正确
// exports = sum   错误的写法
// exports.sum = sum 正确


//d.js
const sum = require('./c')  //名称可以随意取
consoel.log(sum(3,4))
```

#### npm 常用命令

• npm init   在当前目录下，初始化一个package.json  npm init -y 按照默认生成

• npm install 

​	✓ npm install -g http-server    全局安装 http-server

​	✓ npm install --save axios        将依赖保存在package.json 中的dependency中

​	 ✓ npm install -S axios 				同上

​	✓ npm install --save-dev webpack 	将依赖保存在 devdependency中

​	✓ npm install -D webpack   

​	✓ npm install 

• npm uninstall  

• npm start

 • npm run 

 • npm config --help 

• npm login 

• npm publish

• npx <commond>    安装的第三方工具，需要在命令行中执行时，需要使用npx

###### npx 想要解决的主要问题，就是调用项目内部安装的模块。比如，项目内部安装了测试工具 [Mocha](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)

一般来说，调用 Mocha ，只能在项目脚本和 package.json 的[`scripts`](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)字段里面， 如果想在命令行下调用，必须像下面这样。

```bash
# 项目的根目录下执行
$ node-modules/.bin/mocha --version
```

npx 就是想解决这个问题，让项目内部安装的模块用起来更方便，只要像下面这样调用就行了

```bash
$ npx mocha --version
```

npx 的原理很简单，就是运行的时候，会到`node_modules/.bin`路径和环境变量`$PATH`里面，检查命令是否存在。

由于 npx 会检查环境变量`$PATH`，所以系统命令也可以调用。

```bash
# 等同于 ls
$ npx ls
```

参考 阮一峰的网络日志

#### yarn

###### yarn 也是一个包管理器，起初是为了解决npm下载速度慢的情况，但现在可以直接使用npm

