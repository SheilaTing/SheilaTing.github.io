---
















title: promise
date: 2020-05-20 14:59:31
tags: promise
---

#### 语法

```javascript
new Promise()接受两个函数参数对象 此时状态为 pending，resolve 和 reject 
执行resolve()后 promise的状态变为 已执行（fullfilled） 代表成功执行
执行reject() 代表执行失败 failed

new Promise((resolve,reject) =>{
    reject("拒绝")
}).then(value =>{
    //代表promise函数执行成功后的回调，value为执行后得到的数据
},reason =>{
    //执行失败后执行的函数
})
```

#### JS任务优先级  

代码执行优先级从上到下一次执行

- 同步代码块 
- 微任务队列
  - promise 的 then() 会放入微任务队列执行（在执行完 resolve后返回状态后放入微任务队列）
- 宏任务队列
  - setTimeOut 会放入宏任务队列执行

```javascript
	 new Promise(resolve => {
            setTimeout(() => {
                resolve(); // 生成微任务放入微任务队列
                console.log("settimeout");
            }, 0); // 宏任务
        }).then(value => console.log('成功'))
        console.log("案例")

//输出顺序为 "案例" 》 "settimeout" 》 "成功"
```

#### then 层层传递

###### promise.then() 也是一个 promise

```javascript
    new Promise((resolve,reject) =>{
        // resolve("一瓶可乐")  resolve 和 reject 一次只能返回一种结果，不可能又成功又失败
        reject("涨价了");
    }).then()  //此处不处理，结果会向下传递
    .then(null,err=>{
        console.log("失败的原因"+err)
    })
```

#### 链式调用

```javascript
  let p1 = new Promise((resolv, reject) => {
            //resolve("fullfilled")
            reject("rejected")
        })

        let p2 = p1.then(
            value => console.log(value),
            reason => console.log(reason)
            ).then(
                a=> console.log("成功"),
                b => console.log(b)
            )
        
        console.log(p2) //会发现它是一个 promise对象
        
        // .then() 本身返回的也是一个 promise对象 p2也是一个promise，所以它也有相应的.then()函数
        //此刻p1的结果为 rejected,所以p1.then会捕获到 reason 输出 rejected,而p2,我们没有显示的指定它是成功还是失败，默认是成功的，所以会监听到 成功 （此刻我们不能打印 a 的值，因为我们没有设置 例如使用 resolve（值） 或者 return（值））
```

##### 常用语法

```javascript
function fn(arg){
	return new Promise(function(resolve,reject){
		if(成功){
			resolve(data)
		}else{
			reject(reason)
		}
	})
}

fn(arg).then(function(data){
	console.log(data)
}).catch(function(reason){
	console.log(reason)
})
```

```javascript
//复杂案例 也就是使用 Promise 封装 ajax 的请求过程    
function getIp() {
            var promise = new Promise(function (resolve, reject) {
                var xhr = new XMLHttpRequest()
                xhr.open('GET',
                    'http://rap2.taobao.org:38080/app/mock/245421/getIp', true)
                xhr.onload = function () {
                    var retJson = JSON.parse(xhr.responseText)
                    resolve(retJson.ip)
                }
                xhr.onerror = function () {
                    reject('获取IP失败')
                }
                xhr.send()
            })
            return promise
        }
        const getCityFromIp = ip => {
            return new Promise((resolve, reject) => {
                let xhr = new XMLHttpRequest()
                xhr.open('GET',
                    'http://rap2.taobao.org:38080/app/mock/245421/getCity?ip=' + ip, true)
                xhr.onload = () => {
                    resolve(JSON.parse(xhr.responseText).city)
                }
                xhr.onerror = () => reject('获取city失败')
                xhr.send()
            })
        }
        
        
```

#### Promise 封装 ajax 请求（上面也是）

```javascript
new Promise((resolve,reject) =>{
    
}).then()   //一般这样写，promise会立即执行，并在then()中做后续处理
//封装 ajax 这里只定义 promise 请求，未对请求结果做处理，在调用时处理
function ajax(url){
    return new Promise((resolve,reject) =>{
        let xhr = new XMLHttpRquest();
        xhr.open('GET',url);
        xhr.send();
        xhr.onload = function(){
            if(this.status == 200){
                resolve(JSON.parse(this.response))
            }else{
                reject('请求失败')
            }
        }
        xhr.onerror = function(){
            reject(this)
        }
    })
}
//调用时候使用.then()处理请求的返回结果
//第一个ajax 请求
ajax('getUser?参数...').then(
						user =>{
                            return ajax(`getCourse?id=`+user.id)
                           // consoel.log('user 是请求得到的结果')
                        },
    					reason =>{
                            //可以在此处单独处理getname 请求的错误信息
                            consoel.log('失败的原因reason')
                        }
					).then(
						course =>{
                            console.log('course 是根据上面一步的请求的user.id获取到的结果')
                        }
						).chtch(error =>{
   								console.log('在此处做统一的错误处理')
					})

-------------------------------------------------------------------------------

//未使用 promise 之前 封装的 ajax 方法
//四个参数，请求url, 请求参数，请求成功的处理，请求失败的处理
 const request = (url,params,onsuccess,onerror) =>{
     //get 方式拼接参数 url 字符串 将传递的对象参数拆分后拼接
     url = url+'?'+Object.entries(params).map(param => arr[0]+'='+arr[1]).join('&');
     let xhr = new XMLHttpRquest()
     xhr.open('GET',url,true); // 第三个参数是否使用异步
     xhr.onload = function(){
         if(xhr.status == 200 || xhr.status === 304){
             //请求成功执行  onsuccess函数
             onsuccess(JSON.parse(xhr.responseText))
         }else{
             //请求失败执行 onerror函数
             onerror()
         }
     }
     //xhr有默认的 error方法，把我们自定义的赋值给它
     xhr.onerror = onerror
     xhr.send()
 }

 
 //调用
 request('url',{param:'rel'},
        	data=>{
     			consoel.log('请求成功')
    			 consoel.log(data)
 			},
         ()=>{
     			consoel.log('异常')
			 }
        )

```

<!--more-->



#### promise 错误处理

```javascript
new Promise((resolve,reject) =>{
  // reject('运行出错')
    //可以自定义错误返回
    reject(new Error('promise fail'))  // then中错误处理会接受到
}).then(
	value => console.log('运行正常的处理'),
    reason => console.log('运行出错的处理')  //代码会执行到这里
).catch(error){
    //可以在 catch 里面统一对promise的错误进行处理
}



new Promise((resolv,reject) =>{
    reject('出错啦')
}).then(
	value =>{
        return new Promise((resolv,reject)=>{
            reject('p2')
        })
    },
    reason =>{
        console.log('p1 的出错原因')
    }
).then(
	value =>{
        console.log("p2 的成功处理")
    },
    reason => {
        console.log('p2的失败处理')
    }
).catch(error){
    //可以省略 p1,p2的错误处理 reason（）这里可以铺货到 promise所有的错误
}
```

#### finally  无论请求成功或者失败都会执行的代码块

###### 可以利用这个特性做  loadding的 css 效果  因为异步请求可能会耗费时间，那我们希望在数据加载的时候有一个  loadding 的效果，请求结束后把效果取消掉（放到 finally 里面）

注意，这个  ajax 方法是通过  promise 来封装的

发送请求的时候 显示这个 loadding ，（ajax 方法中设置，后面调用的时候自然会显示），调用时捕获在finally 中隐藏这个 loadding



#### 使用 promise 加载图片小例子

```javascript
function loadImage(src) {
            return new Promise((resolve, reject) => {
                let img = new Image();
                img.src = src;
                img.onload = function () {
                    resolve(img) //返回 img 对象，后续可以对图片做样式更改
                }
                img.onerror = function () {
                    reject('加载失败')
                }
                document.body.appendChild(img)
            })
        }

        loadImage('./boy.jpg').then(
            value => {
                value.style.border = '1px solid red'
                console.log(value)
            },
            reason => {
                console.log(reason)
            }
        )
```

#### 使用 promise 封装 setTimeout

```javascript
  //使用promise 封装 setTimeOut
//原理都是一样的，都是通过 promise 来处理后续的操作，返回一个  promise 对象，把操作成功的数据放入 resolv 失败的方式 reject 中后续 then() 处理即可
// setTimeOut 本身里面是一个函数对象，现在把resolv 传递进去，后续 then中处理 resolv相当于执行了setTimeout中的函数
        function timeOut(delay){
            return new Promise((resolve,reject) =>{
                setTimeout(resolve, delay);
            })
        }

        //调用
        timeOut(1000).then(
            () =>{
                console.log('sfsdf')
            }
        )
```

#### 构建扁平化的 setInterval

```javascript
function interval(delay){
        //构建扁平化的  interval
          function interval(delay, callback) {
            return new Promise((resolve, reject) => {
                let id = setInterval(() => {
                    //每隔一段时间做某事,由回调函数来决定做某事，以及何时清除这个 interval
                    //然后调用时想要使用then() 处理返回结果，需要把 resolve带上
                    callback(id, resolve)
                }, delay);
            })
        }
        //调用
        interval(300, (id, resolve) => {
            //想一下这里我们需要做什么事情，是不是处理我们自己的逻辑，然后清除这个定时器
            //然后改变 resolve的状态
            console.log('sdfdsf') //做一件事
            clearInterval(id)
            resolve('成功')
        }).then(
            success=>{
                console.log(success)
            }
        )
}
```

#### setInterval 案例

```javascript
 //案例  页面上有一个div，我们让它每隔一段时间向右移动10px,当宽度大于200px 的时候清除这个定时器
        //并返回执行后的结果

<style>
    #div{
        position: absolute;
        width:50px;
        height:50px;
        background:green;
    }
</style>

    <div id='div'>
    </div>


    <script>
          //构建扁平化的  interval  有没有发现这里的 callback 用到了闭包的知识（回头再看下闭包的知识）
          function interval(delay, callback) {
            return new Promise((resolve, reject) => {
                let id = setInterval(() => {
                    //每隔一段时间做某事,由回调函数来决定做某事，以及何时清除这个 interval
                    //然后调用时想要使用then() 处理返回结果，需要把 resolve带上
                    callback(id, resolve)
                }, delay);
            })
        }
        //调用
        interval(100, (id, resolve) => {
            console.log('调用方法')
            const div = document.querySelector('#div');
            // div.style.left = div.offsetLeft + 10 +'px'
            //也可以这样写
            let left = parseInt(window.getComputedStyle(div).left);
            div.style.left = left + 10 +'px'
        
            if(div.offsetLeft > 300){
                clearInterval(id);
                //更改 promise 状态 把div 返回
                resolve(div)
            }
        }).then(
            //这里可以拿到返回的 div,
            div=>{
                // console.log('倒数第二个then')
                // **注意此处要加上 return 先拿到 执行结果，不然最后一个then 会先执行，找不到div**
             return   interval(100,(id,resolve) =>{
                    // console.log('倒数第二个 then 的定时方法')
                    let width = parseInt(window.getComputedStyle(div).width);
                    div.style.width = width -10 +'px';
                    if(width <= 20){
                        clearInterval(id);
                        
                        resolve(div)
                    }
                })
            }
        ).then(
           div=>{
            //    console.log("执行到最后一个then")
                //继续处理
            div.style.backgroundColor = 'red'
           }
        )

```

promise 怎么这么多知识点呀呀呀呀呀呀呀呀呀呀呀









#### 更多的API

**Promise是一个类 类是一个特殊的函数**

方法有：

- Promise.all 
- Promise.race 
- Promise.reject 
- Promise.resolve



对象属性

- Promise.prototype.then
- Promise.prototype.finally
- Promise.prototype.catch

对象内部属性

state = pending/fullfilled/rejected

#### Promise/A+ 规范







#### 更多用法

**Promise.race 得到一个最先resolve的结果  **

**Promise.all 把全部resolve的结果放进一个数组**

**async/await **

await需要在Promise对象之前

await只在async函数内有效

#### 手写一个简易版的promise

```javascript
//todo
class Promise2{
    state = 'pending'
    succeed = null;
    fail = null;
    resolve(result){
        //改变状态
        setTimeout(() => {
            this.state = 'fullfilled'
            this.succeed(result)
        }, 0);
    }
    reject(err){
         setTimeout(() => {
             this.state = 'rejected'
             this.fail(err)
         }, 0);
    }
    constructor(fn){
        fn(this.resolve.bind(this),this.resolve.bind(this));
    }
    then(succeed,fail){
        this.succeed = succeed;
        this.fail = fail;
    }
}

```

**先打个结吧，太多内容了**