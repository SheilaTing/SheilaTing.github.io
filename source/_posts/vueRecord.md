---
title: vueRecord
date: 2019-08-20 11:21:25
tags:
---

- 
- vue路由传参--------params和query的区别
1.params只能用name来引入路由，query用path来引入
2.params类似于post，query更加类似于我们ajax中get传参，说的再简单一点，前者在浏览器地址栏中不显示参数，后者显示，所以params传值相对安全一些。
3.取值用法类似分别是this.$route.params.name和this.$route.query.name。
4.params传值一刷新就没了，query传值刷新还存在  <!--more--> 
	
		this.$router.push({
		path:"/detail",
		params:{
		name:'nameValue',
		code:10011
		}
		});

		this.$router.push({
		path:'/xxx'
		query:{
		id:id
		}
		})
总结：少量参数可以用此方法，如果有大量公共数据，可以采用vuex或本地存储的方式。

## router-link 传递参数 ##
	拼接 ： :to = '"/detail/"+item.id'
	或者： ：to{name="路由名称"，query:{参数名称：参数值}}
	或者： ：to{name="路由名称"，params:{参数名称：参数值}}
## 模板字符串 ##
	使用${} 传值 `` 包裹
## 使用moment.js 加过滤器 处理日期格式化 ##
	自定义全局过滤器
	Vue.filter('过滤器名称'，function(data,formatstr){
		return Monment(data),format(formatstr)
	});
	使用时：
	{{data|过滤器名称（converTime）("YYYY-MM-DD")}}
## 图片懒加载 ##
	可以使用MINT-UI的 lazy-load 来实现，机制 加载 n+1 张图片，n 为页面能显示的数量
## vue 绑定原生事件 click ... ##
	https://www.cnblogs.com/qjuly/p/8881982.html
## 为了解决用户直接使用URL 访问页面 ##
	可以使用导航守卫功能，
	beforeRouterUpdate
	onRouterUpdate
	afterRouterUpdate
	时候接受携带的参数，请求相应的接口，渲染页面即可
	或者直接进入也面时携带相应参数也可以
## 图片查看器  可以使用插件 vue-preview ##

								