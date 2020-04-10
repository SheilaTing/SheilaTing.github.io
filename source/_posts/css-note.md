---
title: css note
date: 2019-07-21 12:45:59
tags:
---

- css 盒子模型
- box-sizing: content-box|border-box|inherit;
- content-box	
	这是由 CSS2.1 规定的宽度高度行为。
	宽度和高度分别应用到元素的内容框。
	在宽度和高度之外绘制元素的内边距和边框。
- border-box	
	为元素设定的宽度和高度决定了元素的边框盒。
	就是说，为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。
	通过从已设定的宽度和高度分别减去边框和内边距才能得到内容的宽度和高度
- inherit	规定应从父元素继承 box-sizing 属性的值。
<!--more-->
- 例子：
- ![avatar](../img/header.png)
- 在这个例子中，一个div 中包含一个input ，给input设置了固定的大小，想要使框中的文字的左侧边距大一些，设置了 padding-left ,由于盒子的固定大小，使得右侧边框超出了父div，此时使用box-sizing : border-box 即可解决该问题

## 为什么"overflow:hidden"能清除浮动的影响 ##
##bfc

----------
我们都知道"overflow:hidden"可以溢出隐藏，即当内容元素的高度大于其包含块的高度时，设置该属性即可把内容区域超出来的部分隐藏，使内容区域完全包含在该包含块中。
然而"overflow:hidden"还有另外一个特殊的用途，那就是清除包含块内子元素的浮动


----------
BFC
BFC（Block Formatting Context），块级格式化上下文，它规定了内部的块级元素的布局方式，默认情况下只有根元素（即body）一个块级上下文。

----------
1、BFC布局规则

内部的块级元素会在垂直方向，一个接一个地放置；
块级元素垂直方向的距离由margin决定。属于同一个BFC的两个相邻的块级元素会发生margin合并，不属于同一个BFC的两个相邻的块级元素不会发生margin合并；
每个元素的margin box的左边，与包含border box的左边相接触（对于从左往右的格式化，否则相反）。即使存在浮动也是如此；
BFC的区域不会与float box重叠；

BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素；外面的元素也不会影响到容器里面的子元素；

计算BFC的高度时，浮动元素也参与计算。

2、创建一个BFC
首先我们要知道怎样创建BFC。一个BFC可以被显式触发，只需满足以下条件之一：

float的值不为none；
overflow的值不为visible；
position的值为fixed / absolute；
display的值为table-cell / table-caption / inline-block / flex / inline-flex。

3、BFC的应用
1）、消除margin合并
在margin合并这篇博客中，我描述了相邻兄弟元素、父元素与第一个和最后一个子元素、空块元素的margin合并情况。那么对于发生margin合并的元素，我们怎样消除margin合并？
对于父子元素，我们可以通过设置padding、border、inline content、height、min-height、max-height等属性来消除；但是相邻的兄弟元素之间必出现margin合并，这时如果想要消除margin合并，又该怎么办？
我们首先要搞清楚为什么会发生margin合并。这些元素（包括兄弟、父子元素等）之间之所以会发生margin合并，是因为它们属于同一个BFC

作者：前端小少女
链接：https://www.jianshu.com/p/7e04ed3f4bea
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

----------
参考此文：
[https://www.jianshu.com/p/7e04ed3f4bea](https://www.jianshu.com/p/7e04ed3f4bea)