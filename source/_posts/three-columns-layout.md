---
title: 布局篇 - 三栏布局（左右定宽，中间自适应）
date: 2019-04-02 01:01:26
categories: 编程
tags:
- CSS
- 布局
---
> 面试时，经常会被问到一种常用布局方式 —— `三栏式布局`（左右两栏定宽，中间一栏宽度自适应），这样的布局方式用`CSS`应该如何来实现呢？今天我们来总结一下实现这种布局方式的`5种`不同方法。

我们先来看看最终要实现的效果。

![三栏式布局][1]

### 1. 利用浮动（float）布局
```
<!--HTML-->
<body>
  <section id="container">
    <!--注意！！.left和.right谁在前都可以，但是.center必须在它俩后面-->
    <aside class="left">left(定宽)</aside>
    <aside class="right">right(定宽)</aside>
    <main class="center">center(宽度自适应)</main>
  </section>
</body>
```


```
/***CSS***/
.left {
	width: 200px;
	height: 100vh;
	background: #61daa5;
	/* 左侧左浮动 */
	float: left;
}

.right {
	width: 200px;
	height: 100vh;
	background: #ffa7e9;
	/* 右侧右浮动 */
	float: right;
}

.center {
	height: 100vh;
	background: #78a5f1;
	/* 多出10px，是给左中右三栏留出10px间距 */
	margin-left: 210px;
	margin-right: 210px;
}
```

### 2. 利用绝对定位（absolute）布局
```
<!--HTML-->
<body>
  <section id="container">
    <!--注意！！.left/.center/.right任意排序，不会影响最终结果-->
    <aside class="left">left(定宽)</aside>
    <main class="center">center(宽度自适应)</main>
    <aside class="right">right(定宽)</aside>
  </section>
</body>
```

```
/***CSS***/
.left {
	width: 200px;
	height: 100vh;
	background: #61daa5;
	/* 绝对定位 */
	position: absolute;
	left: 0;
	top: 0;
}

.right {
	width: 200px;
	height: 100vh;
	background: #ffa7e9;
	/* 绝对定位 */
	position: absolute;
	right: 0;
	top: 0;
}

.center {
	height: 100vh;
	background: #78a5f1;
	/* 多出10px，是给左中右三栏留出10px间距 */
	margin-left: 210px;
	margin-right: 210px;
}
```

### 3. 利用表格（table/table-cell）布局
```
<!--HTML-->
<body>
  <section id="container">
    <!--注意！！.left/.center/.right/必须按照左中右顺序排序-->
    <aside class="left">left(定宽)</aside>
    <main class="center">center(宽度自适应)</main>
    <aside class="right">right(定宽)</aside>
  </section>
</body>
```

```
/***CSS***/
/* .left,.center,.right的父元素 */
#container {
	display: table;
	/* 一定要设置width: 100% */
	width: 100%;
}

/* .left,.center,.right三个元素 */
.left, .center, .right {
	display: table-cell;
}

.left {
	width: 200px;
	height: 100vh;
	background: #61daa5;
}

.right {
	width: 200px;
	height: 100vh;
	background: #ffa7e9;
}

.center {
	height: 100vh;
	background: #78a5f1;
}
```

### 4. 利用弹性和（flex-box）布局
```
<!--HTML-->
<body>
  <section id="container">
    <!--注意！！.left/.center/.right/必须按照左中右顺序排序-->
    <aside class="left">left(定宽)</aside>
    <main class="center">center(宽度自适应)</main>
    <aside class="right">right(定宽)</aside>
  </section>
</body>
```

```
/***CSS***/
#container {
	/* 设置父元素为弹性盒 */
	display: flex;
}

.left {
	width: 200px;
	height: 100vh;
	background: #61daa5;
}

.right {
	width: 200px;
	height: 100vh;
	background: #ffa7e9;
}

.center {
	height: 100vh;
	background: #78a5f1;
	/* 设置flex:1; 表示该元素占满剩余空间 */
	flex: 1;
	/* 左中右之间留出10px间距 */
	margin: 0 10px;
}
```

### 5. 利用栅格（grid）布局

关于grid布局的基础知识，大家可以参考[MDN-网格布局](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout)和[阮一峰-Grid 布局教程](http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)以及[张鑫旭-写给自己看的display: grid布局教程](https://www.zhangxinxu.com/wordpress/2018/11/display-grid-css-css3/)三篇文章深入学习。

```
<!--HTML-->
<body>
  <section id="container">
    <!--注意！！.left/.center/.right/必须按照左中右顺序排序-->
    <aside class="left">left(定宽)</aside>
    <main class="center">center(宽度自适应)</main>
    <aside class="right">right(定宽)</aside>
  </section>
</body>
```

```
/***CSS***/
#container {
	/* 设置父元素为grid */
	display: grid;
	/* 设置几个值代表布局为几行，每个值是行高 */
	grid-template-rows: 100vh;
	/* 设置几个值代表布局为几列，每个值是列宽 */
	grid-template-columns: 200px auto 200px;
}

.left {
	background: #61daa5;
}

.right {
	background: #ffa7e9;
}

.center {
	background: #78a5f1;
	/* 左中右之间留出10px间距 */ 
	margin: 0 10px;
}
```

[1]: /medias/article/coding/three-columns-layout/three-columns-layout.png