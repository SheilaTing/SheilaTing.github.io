---
title: react-css使用
date: 2020-06-12 14:21:04
tags: create-react-app
---

#### 样式引入 

###### 现在我们都通过 create-react-app 来创建 react 项目，在 create react app 中支持我们使用 module 的方式来引入 css 文件

es6 中我们引入文件可以使用 import 关键字即可，坏处是这个css 会全局公用

第二种方式 引入 css modules 这种方式可以指定使用的是哪个 css 文件的样式，但该 css 文件的命名必须为 XXX.module.css 

```javascript
import styles from './Error.module.css';
//使用时可以直接 styles.文件中的样式选择器即可使用
//例如 css 中有一个 .error {color:red}
style.error 即可
// 这样这个 css 就只在这个组件中生效
```

****

**style-components**

###### 之前我们引入 css 都是需要引入 css 文件，如果我们想要直接在 js 中写 css 需要使用 style-components 这个包 使用前需要先安装

然后再组件中引入

```javascript
import style from 'style-components'
//然后再组件中定义这个样式
const Wrapper = style.footer`border:1px solid red`; 注意这个符号
//然后 直接使用这个 Wrapper 对象即可，此时这个包帮我们生成一个footer 标签，而且应用了我们定义的css。
<Wrapper>someThing</Wrapper>
反正就记住这样用就好了，定义一个对象，然后想使用什么标签就 style.标签名`css 代码`,最后直接使用这个返回的对象即可，自动应用我们``中定义的样式
===================================
解密：
style.footer`` 其实是一个函数 ``中的内容时参数，省略了小括号
let style = {footer:str=>console.log(str)}
style.footer`border:1px solid blue`
```

