---
title: css学习笔记
date: 2020-04-06 18:55:30
---



#### css引入方式

* link方式在html页面中引用

  `` <link rel="stylesheet" href="index.css">``

* @import 方式在 style标签或者 css文件中引入，注意在CSS文件中引入时，要在顶部引入（即前面无css语句）且句尾要用分号;结束 否则不生效

  例如：		

  <style>
  @import url("index.css");
  @import url('index.css');
  @import url(index.css);
  @import 'custom.css';
  @import "common.css";
  @import url('landscape.css') screen and (orientation:landscape);
  </style>

#### link方式和@import引入CSS有 什么区别？<!--more--> 

``` 是HTML的标签，在页面上代表一个元素，可以被JS选中修改； @import是CSS的语法，JS无法操控 • 两个			link标签，文件会并行加载。 一个link的CSS里包含一个@import， 文件串行加载会更慢。因此不推荐用@import```

#### css语法

		<style> p {font-size: 20px; }@media print { font-size: 60px; } </style>
		选择器 {
	    	属性: 值; 
	    	属性: 值; 
	    			}
	    @关键字 {
	    			其他 
	    					}

#### css选择器

- 元素选择器（标签选择器）

- ID选择器

- class 选择器

- 通用选择器 *

- 属性选择器

  ``` [attr~=val] 仅选择 attr 属性的值（以空格间隔出多个值） 中有包含 val 值的所有元素，比如位于被空格分隔的多个 类（class）中的一个类。 ü [attr*=val] 选择 attr 属性的值中包含字符串 val 的元素。 ü [attr^=val] 选择 attr 属性的值以 val 开头（包括val）的 元素。 ü [attr$=val] 选择 attr 属性的值以 val 结尾（包括val）的 元素。 ü [attr|=val] 选择 attr 属性的值是val或以 val-开头的元素 （-用来处理语言编码）。
  
  ```

- 组合选择器

     ` A, B 并集选择器 A, B 同时选中A 和 B`

  - A B 后代选择器 选中A的后代B元素，注意A和B之间有个空格

  - AB  选中既是A又是B的元素，注意A和B之间没有空格

  - A > B  选中A 的直接子元素B

    ``` html
    <ul class=”detail”> <li><ul><li>aaa</li> </ul> </li> <li>bbb</li> </ul> <style> .detail li { border: 1px solid red; }</style>
    ```

  - A + B  选中A 的下一个相邻元素B
  - A ~ B  选中A 的后面全部相邻元素B

  

  #### 伪元素与伪类

  > 伪类是一个选择器，用来选择处于特定状态的元素。 ü 比如某种类型元素的第一个、元素的鼠标放置上的 状态 ü 类似于给元素加了个额外的class

  - 伪类/first-child

    > 作为自己父亲的第x个孩子

  - 伪类/first-of-type

    > 作为自己父亲当前标签类型的第x个孩子 ü first-of-type、last-of-type、nth-of-type(n)

  - 伪类 :link - :visited - :hover - :active

  - 更多伪类

    - • :checked ü 选中被勾选的表单元素 •
    - :disabled ü 选中被禁用的表单元素 •
    - :focus ü 选中被激活的表单元素 • :root ü 选中根节点

  - • :target  选中页面上id为当前hash的元素 

    > 实战1 • 不使用JS实现一个Tab切换 http://js.jirengu.com/fikan ü 实战2 • 不使用JS实现弹框 http://js.jirengu.com/neyul • :not(xx) ü 选中不为xx的元素

  #### 伪元素

  - ::before、 ::after

  > 在元素内插入一段内容，作为元素的第一个/最后一个 孩子 ü 必须有 content 属性 ü 常用来替代图标、无实际意义的标签

  - ::first-line ü 选中段落的第一行 •
  -  ::first-letter ü 选中段落的第一个字符
  - ::selection ü 匹配被鼠标选中的文字内容

  #### css层叠样式表 （选择器权重，优先级）

  + 样式来源 
  + 1. 使用不同的选择器设置的样式 
  + 2. 浏览器默认样式 (user agent stylesheet) 
  + 3. 继承的样式 (Inherited from xxx)

  #### 优先级顺序

  > !import > 内联样式 > 选择器设置样式 > 浏 览器默认样式 > 继承样式

  > !import的优先级最高 ü 尽量少用!import，除非迫不得已 • 比如队友写了垃圾代码如内联样式，需要覆盖掉 • 比如有一些高优先级的代码，只能通过!import来覆盖
  >
  > 浏览器默认样式
  >
  > 继承样式

  #### 继承属性与非继承属性

  > color、 font-size、font-family、line-height

  #### 非继承属性

  > border、background、margin、padding、display...

  #### 如何查询该属性是否可继承

  > https://www.w3.org/TR/CSS21/propidx.html

  #### 优先级计算

  > 千位： 如果声明在 style 的属性（内联样式）则该位得一分 ü 百位： 选择器中包含ID选择器则该位得一分 ü 十位： 选择器中包含类选择器、属性选择器或者伪类则该位得一分。 ü 个位：选择器中包含元素、伪元素选择器则该位得一分。

  ![](./cal.png)