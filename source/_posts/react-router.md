---
title: react-router
date: 2020-06-12 14:53:47
tags: react
---

#### 安装

npm install --save react-router-dom

#### 基本使用

###### 在页面上引入所需的组件对象

```javascript
import {BrowserRouter || HashRouter as Router,
       Switch,
        Route,
        Link
       }  // 取别名
// hashRouter 和 BrowserRouter 区别是url 一个带 # 一个不带

BrowserRouter 的作用就是声明这个组件被 router 接管了。
// 在组件 return 的 逻辑中，我们使用 <Router> 标签来包裹这些 路由标签，Switch,route,link 等
Switch 相当于 js 中的 switch 语句主要用来判断不同的条件执行不同的代码
Route 标签则是用来渲染组件的，一般会包裹一个组件，或者在它的属性上使用 component，children 来指定要渲染的组件   
Link 标签则是指定点击时要跳转的组件路径 很像 a 链接
```

**下面是一个简单的使用**

```javascript
import React from 'react';
import {BrowserRouter as Router,
    Switch,
    Route,
    Link
} from 'react-router-dom'

function Nav(props){
    return (
        <Router>
            <nav>
                <Link to="/home">首页</Link>
                <Link to="/history">历史</Link>
                <Link to="/my">我的</Link>
                <Link to="/about">关于</Link>
            </nav>
            <Switch>
                <Route path="/about">
                    <About/>
                </Route>
                <Route path="/history" component={History}>              
                </Route>
                <Route path="/my" children={<My/>}>
                   
                </Route>
                <Route path="/home">
                    <Home/>
                </Route>
            </Switch>
        </Router>
    )
}

function Home(){
    return <h2>首页</h2>
}
function History(){
    return <h2>历史</h2>
}
function My(){
    return <h2>我的</h2>
}
function About(){
    return <h2>关于</h2>
}

export default Nav;
```

###### 当然一般情况下我们会把这个路由 Router 标签放在 index.js 中，包裹所有的 组件，这样在其他组件中我们无需再引入这个 router，直接使用其提供的标签即可。

#### Navlink & Redirect

```javascript
import {Navlink,Redirect} from 'react-router-dom'
```

###### navlink 为我们提供了一个 activeClassName css 属性，只有当这个link被选中时，它才会拥有这个属性，我们可以通过事先给它绑定样式来实现我们当前选中的是哪个 标签

```javascript
  <NavLink activeClassName='active'exact  to="/">首页</NavLink>
  <NavLink activeClassName='active'exact  to="/history">历史</NavLink>
  <NavLink activeClassName='active' exact  to="/my">我的</NavLink>
  <NavLink activeClassName='active'exact  to="/about">关于</NavLink>
```

###### 有一个匹配的问题，因为它最终还是通过当前访问的url来确定你点击的是哪个link,所以当首页的path是 / 时，其他页面分别是 / xxx ，它只做了一级匹配，它只匹配一级url,也就是它值对比 一个 /,后面的/sxx 它都不管了，只要你有我访问路径的一层我就当做你被选中了。那么当页面访问 /about 时，也匹配/ 首页，所以首页也会应用 css。为了解决这个问题，我们可以给它加一个属性 exact 就会完全匹配，一般只给首页加，因为其他的url一般不会相同，看情况吧

#### Redirect 重定向到。。。。

```javascript
<Redirect to ='/about'></Redirect> //重定向到 /about 这个路径下的 组件里
```

#### React-router 代码分割

###### 其实就是把需要引入的代码在不同的时机（需要的时候）分别引入进来

###### 使用之前学习的const 组件 = React.lazy(()=> import ......)

#### React-router 滚动设置

###### 单页面应用，当我们切换不同的路由时，也就是不同的组件，我们在 a页面之前滚动的位置，切换到 b 时，也会默认在b页面 a 的位置，按理来说，切换后应该默认在当前组件页面的顶部才对，为了解决这个问题，我们定义一个组件 ScrollToTop

引入 useLoacation

```javascript
import {useLocation} from 'react-router-dom';
//在组件中就可以使用 useLoaction 的方法
const {pathName} = useLoaction();  //获取到当前页面上的url 访问路径
相当于浏览器中的 location 对象的 pathname 属性 location.pathname

export function ScrollToTop(){
    const {pathName} = useLoaction();
    useEffect(()=>{
        window.scrollTo(0,0);
    },[pathname])
    return null;
}
// 我们使用 useEffect 监控 访问的url pathname ,当它发生改变的时候，将滚动到页面顶部
这个组件放在全局任意一个地方即可

也可以放在一个单独的组件中，放在单独组件中我们就不用监控这个 访问路径了，就让它一切换到这个组件的路径就将页面滚动到顶部即可
export funtion SrcollToTop(){
    useEffect(()=>{
        window.scrollTo(0,0);
    },[])//传递空 什么都不监控，那么就会只执行一次
}

```

#### useParams

```javascript
// 获取url中传递来的 参数
例如页面访问 /blog/aaaa
<Route path = '/blog/:slug'>
    //这个路由渲染的组件是 blog
    <BlogPost/>
    </Route>
当访问/blog/aaa 会到这个路由，进而访问到这个 blogPost 组件
那么在 blogPost中
function BlogPost(){
    let {slug} = useParams();  // slug 就能得到url传递来的参数
    return ......
}
```

**更多用例查看 react-router 官网**