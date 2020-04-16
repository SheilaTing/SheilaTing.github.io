---
title: grid 布局
date: 2020-04-16 10:56:08
tags: css
---

#### 定义

是一种二维布局模型，先在页面上画好“虚拟格子” ，再设置元素在格子上的 位置和跨度

#### 相关概念

- Grid Container 
-  Grid Item 
- Grid Line 
- Grid Track
- Grid Cell
- Grid Area

#### 使用方法

- 声明使用grid布局 • display: grid; /* inline-grid*/

- 画好虚拟表格 • grid: 30px auto 30px / 10% 1fr 10% • grid-gap: 10px 20px; 
  - 表示：设置格子 三行两列，auto表示长度由内容决定，1fr表示撑满自由空间，此例子表示画一个三行三列的表格，行高，列宽，第一行高30px，第二行高度自适应，第三行高度也是30px,第一列宽度为总宽度的10%，第二列宽度自适应，第三列宽度为总宽度的10%；grid-gap 第一个参数表示，行之间的间隙，第二个为列之间的间隙；
- 告诉内容如何放置 • align-items: center; • justify-items: center; • align-content: start; • justify-content: space-evenly;

#### 子项属性

设置子项的范围和位置

- grid-column： 3 / span 2; 
  - 表示：从列的第三条线开始，跨两列，等价于 grid-column:1/3;
- grid-row: 3 / 4; 
  - 表示：从行的第三条线开始，到行的第四条线结束，相当于 grid-row:3/span 1;
-  justify-self: start; 
-  align-self: center; 
- grid-area:3/1/4/3
  - 表示：从第3条行线和第1条列线开始，到第4条行线和第3条列线结束 
  - 表示 开始的行线/开始的列线/结束的行线/结束的列线 所覆盖的范围 
  -  grid-column、grid-row的替代写法，相当于 grid-row: 3 / 4; grid-column: 1 / 3

#### 更复杂用法参考

https://zhuanlan.zhihu.com/p/33030746