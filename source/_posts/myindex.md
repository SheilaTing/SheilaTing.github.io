---
title: myindex
date: 2018-04-17 21:30:02
tags:
---
## 样式 样式  ##
    关于布局，真的还有好多需要学习
	最近看到老师采用的布局方式为： height:0,overflow:hidden;
	将内容隐藏，直接通过 padding-bottom，将容器撑开，此布局方式我还真的没有用过。但真的就达到了预期的效果，还是蛮神奇的。
	看来还是要多学习一些布局的案例，将css 属性牢记于心，才能运用自如。
	一些技巧哈：
	如果想使用css 写一个箭头，可以使用border来实现：


    例如：这样的写法：
	.down-arrow {
	    display: inline-block;
	    border: 3px solid transparent;
	    border-top-color: #999;
	    position: absolute;
	    top: 18px;
	    right: 17px;

	还有加气泡的写法 需要使用到：before 伪类 和 ：after 伪类：
	  div的基本样式
		.tip {
		    background: #eee;
		    border: 1px solid #ccc;
		    padding: 10px;
		    border-radius: 8px;
		    box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
		    position: relative;
		    width: 200px;
		}	
		before 伪元素写好箭头样式，颜色与元素边框颜色相同
		.tip:before {
		    position: absolute;
		    display: inline-block;
		    border-top: 7px solid transparent;
		    border-right: 7px solid #eee;
		    border-bottom: 7px solid transparent;
		    border-right-color: rgba(0, 0, 0, 0.2);
		    left: -8px;
		    top: 20px;
		    content: '';
		}
		after 伪元素更改右侧边框颜色与背景色相同，形成空的气泡
		.tip:after {
		    position: absolute;
		    display: inline-block;
		    border-top: 6px solid transparent;
		    border-right: 6px solid #eee;
		    border-bottom: 6px solid transparent;
		    left: -6px;
		    top: 21px;
		    content: '';
		}

		然后就实现啦！
![avatar](../img/banner.jpg)
			    