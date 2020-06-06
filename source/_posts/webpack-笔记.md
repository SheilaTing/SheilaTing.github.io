---
title: webpack 笔记
date: 2020-06-01 15:25:41
tags: webpack
---

#### 定义

###### 静态模块打包器

###### 根据文档学习

#### 资源管理

###### 使用 webpack 命令时，可以指定webpack的配置文件，webpack --config webpack.config.js,该命令可以简写为 webpack，它会默认去找这个名称的配置文件

```javascript
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
     "build":"webpack"  
  },
```

**webpack.config.js 部分内容**

```javascript
const path = require('path')

module.exports = {
    entry:'./src/index.js',
    output:{
        filename:'bundle.js',  //指定打包后的文件名称,在入口html中引入即可
        path:path.resolve(__dirname,'dist')   //文件路径
    }
}
```



###### 有一个概念你必须清楚的就是,我们的项目目录，分为 src 源代码目录，供开发人员开发使用，而dist目录是打包后的目录，供上线使用，这个里面的文件经过压缩处理，体积小，而且把我们模块化编写的代码打包成浏览器可以识别的代码。

#### 管理输出

###### htmlwebpackPlugin 帮助我们生成 dist 下的index.html,(也就是最终运行的html)并且将打包后的js文件引入该 html.



#### 开发环境

**inline-source-map**

###### webpack.config.js 中 配置 dev-tool:inline-source-map,作用是对源码和打包后的代码做一个映射，这样出错后，点击相应的文件能定位到源代码，方便开发人员

****

#### webpack-dev-server

###### 刚发现 webpack-dev-server 执行后并不会在本地生成 dist 文件夹，以及打包后的文件，在浏览器中可以看到打包后的js，再一次明确一点哈：开发环境我们为了方便查看页面的变化所以引入了webpack-dev-server ，而在生产环境，我们直接执行 npx webpack 打包好代码，这个命令会在当前目录生成 dist文件夹，文件夹下是相应的打包文件，然后把这个dist放入服务器即可。

刚刚使用 webpack-dev-server 纳闷了半天 dist 为何是空的

也就是说：我们引入 htmlwebpackplugin 插件 这个插件会生成dist文件夹，并且在里面生成 index.html,并且引入相关打包后的js,以及我们引入的 cleanwebpackplugin 插件，清理 dist文件夹，都是为了我们的生产环境而引入的。我这样理解没错吧，可为何，安装包时放到了 dev中，为了开发测试？







#### HMR （hot module replacement） 模块热更新）（仅限开发环境使用）

它允许在运行时更新各种模块，而无需进行完全刷新，即只变动你修改的地方，其他的代码还保留之前的状态。

启用这个功能需要配合 webpack-dev-server 一起使用。

###### 使用这个功能的时候，如果想要对某一个模块进行热更新，需要在入口文件中加入一些代码：

```javascript


//在要热更细的模块中
if(module.hot){  //判断是否开启了热更新
//热更新功能接受print.js的变动的更新,即只刷新print.js 中的变动，其他代码状态不会被刷新
// 例如，入口文件中有一个变量i,每次执行都会++，你会发现当接受print.js的热更新后，入口文件中的 i还是会从它上次执行的地方开始++，而不是从初始值（例如1）开始；这就是热更新
	module.hot.accept('./print.js',function(){
		//回调函数
		
	}),
}
```







#### Tree shaking

###### 你可以将应用程序想象成一棵树。绿色表示实际用到的源码和 library，是树上活的树叶。灰色表示无用的代码，是秋天树上枯萎的树叶。为了除去死去的树叶，你必须摇动这棵树，使它们落下。

结论： 在生产环境下，加入  mode: "production" 就会自动将无用的代码给去除，减小体积，提高效率

例如，在a.js 中有export 出了两个函数，但只被引用了一个函数，但打包后，你会发现打包后的代码中依旧是包含未使用的函数的，加入这个配置后，就会只引入使用的这个函数；binggo



#### 生产环境

###### *开发环境(development)*和*生产环境(production)*的构建目标差异很大。在*开发环境*中，我们需要具有强大的、具有实时重新加载(live reloading)或热模块替换(hot module replacement)能力的 source map 和 localhost server。而在*生产环境*中，我们的目标则转向于关注更小的 bundle，更轻量的 source map，以及更优化的资源，以改善加载时间。由于要遵循逻辑分离，我们通常建议为每个环境编写**彼此独立的 webpack 配置**

使用参考官方文档

思路：拆分 webpack.config.js

开发环境：webpack-dev.js

生产环境： webpack-prod.js

common.js ：包含生产和开发环境都需要的公共的一些配置

下载一个 merge 包，用来根据不同的环境  ---- 开发环境（或者生产环境）的js 和 common.js 合并



在package.js scripts 中设置脚本，

```javascript
//例如
scripts:{
    "start":"webpack-dev-server --open --config webpack.dev.js" , //启动 webpackserver 并且是通过开发环境的配置文件启动的
    "build":"webpack --config webpack.prod.js"  //生产环境的配置文件，生产环境是不需要启动webpackserver的，所以就是这样喽
}
```

#### 指定环境

任何位于 `/src` 的本地代码都可以关联到 process.env.NODE_ENV 环境变量，所以以下检查也是有效的

配置环境的步骤如下：

在相应的webpack配置文件中写上，例如在dev.js 中写 development

```javascript
new webpack.DefinePlugin({
            'process.env.NODE_ENV':JSON.stringify('development')
        })
```

这样，你执行 该文件的话，就会获得当前配置的环境名称，然后做判断，例如打印日志什么的

在 src下的文件中都可以访问到该 类似全局变量的这个环境配置



#### 代码分离 好处就是可以把共有的东西抽离出来

###### 代码分离就是可以把不同的文件打包到不同的bundle.js中，例如之前配置的 多个 entry,以及output



如果入口 chunks 之间包含重复的模块，那些重复模块都会被引入到各个 bundle 中

但这种方式需要我们手动配置太麻烦，而且还会有一个问题，例如这两个入口文件中都引用了jquery,那么最终生成的两个 bundle.js 中都会包含 jquery,这样无疑文件体积会大很多，怎么解决呢，就是把共有的juqery 提取出来，其他模块需要，引入即可。

具体解决方案：CommonsChunkPlugin

这个插件可以将公共的依赖模块提取到已有的入口 chunk 中，或者提取到一个新生成的 chunk

使用步骤见官网文档

**动态导入**

###### 就是在使用到它的时候在引入，例如有一个功能组件需要我们登陆后操作，那么我们在登录之前可以不用加载该模块，等用到的时候再导入

这时候，我们不用指定多个入口，配置好后，通过动态引入这个公用的库，它会自动生成一个公用的js

使用 import 函数 promise 风格的函数

参见官网

#### 疑问

**webpack 为何需要多个入口文件和输出文件？**

###### 答：项目中肯定不止一个html页面，a页面需要引入a.js，b.js，c.js 在入口文件中require a页面需要的js文件即可。

###### b页面需要引入d.js ，e.js ，f.js ，如果不能设置多个入口文件和多个编译输出文件的话就需要在入口文件中引入全部6个js文件，并编译到一个输出文件，这岂不是违背了模块管理工具的初衷呢？

###### 这就像是不同的模块需要不同的js一样

#### 自己创建 loader

###### 自己手写一个loader,需要在 webpack配置文件中指定loader的路径，有需要可以配置一些 options

```javascript
 //指定loader 目录
    resolveLoader: {
        modules: [
            'node_mdules',
            path.resolve(__dirname, 'loaders')
        ]
    }, 

--------------------------------------------------------------
{
                test:/\.js$/,
                use:[
                    {
                        //如果不在上面统一指明 loaders目录，那么就要像下面这样写全路径
                        // loader:path.resolve(__dirname,'loaders/replace.js'),
                        //如果已经制定了loader的目录那么这里loader直接写
                        loader:"replace",
                        options:{
                            name:'abc'
                        }
                    }
                ]
            }
```

###### loader 的编写

###### 所谓 loader 只是一个导出为函数的 JavaScript 模块，（官网介绍的特别详细）[loader runner](https://github.com/webpack/loader-runner) 会调用这个函数，然后把上一个 loader 产生的结果或者资源文件(resource file)传入进去。函数的 `this` 上下文将由 webpack 填充，并且 [loader runner](https://github.com/webpack/loader-runner) 具有一些有用方法，可以使 loader 改变为异步调用方式，或者获取 query 参数。

```javascript
// testLoader.js
const { getOptions } = require('loader-utils')
module.exports = function(source,map,meta){
    //source 就是我们在配置文件中，设置匹配正则表达式的文件，而在loader 中 我们要做的事情就是，通过我们自己的一些个性化处理，来处理这个 source,然后返回出去
    doSomeThing(csource)
    const options = getOptions(this)
    // options 就是webpackconfig.js中loader配置的 options 参数
    return source;
}
```

#### 自己创建 plugin

###### 插件能够 [钩入(hook)](https://www.webpackjs.com/api/compiler-hooks/#hooks) 到在每个编译(compilation)中触发的所有关键事件。在编译的每一步，插件都具备完全访问 `compiler` 对象的能力，如果情况合适，还可以访问当前 `compilation` 对象。

就是在编译过程的某个阶段做一些特定的事情

`webpack` 插件由以下组成：

- 一个 JavaScript 命名函数
- 在插件函数的 prototype 上定义一个 `apply` 方法。
- 指定一个绑定到 webpack 自身的[事件钩子](https://www.webpackjs.com/api/compiler-hooks/)
- 处理 webpack 内部实例的特定数据
- 功能完成后调用 webpack 提供的回调。

```javascript
//基本构架  es3
function HelloPlugin(options){
    
}
//compiler 对象是webpack 创建的一个对象，可以访问到webpack构建的每个过程,
HelloPlugin.prototype.apply = function(compiler){
    //compiler 在执行过程中，可以获得当前过程中的 编译过程对象 compilation
    compiler.hooks.done.tap('helloPlugin',compilation=>{
        //做一些事情
        consoel.log('do something here')
    })
}
module.exports = HelloPlugin
-----------------------------------------
//es6
class HelloPlugin{
    constructor(options){
        //..
    }
    apply(compiler){
         compiler.hooks.done.tap('helloPlugin',compilation=>{
        consoel.log('do something here')
    })
    }
}

module.exports = HelloPlugin

```

**哎，具体，我们也没学明白，就知道，先这样用起来**

