---
title: 布局篇 - 两栏式布局（左侧定宽，右侧自适应）
date: 2019-04-02 13:56:59
categories: 编程
tags:
- CSS
- 布局
---
> 面试中，另外一种经常被问到一种常用布局方式 —— `两栏式布局`（左侧栏定宽，右侧一栏宽度自适应），这样的布局方式用`CSS`应该如何来实现呢？今天我们来总结一下实现这种布局方式的`5种`不同方法。

我们先来看看最终要实现的效果。

![两栏式布局][1]

### 1. 利用浮动（float）布局
```
<!--HTML-->
<body>
	<div id="container">
		<aside class="left">left(定宽200px)</aside>
		<main class="main">main(宽度自适应)</main>
	</div>
</body>
```

```
/***CSS***/
.left {
	width: 200px;
	background: #61daa5;
	
	/* 左侧定宽栏左浮动 */
  float: left;
}

main {
	background: #78a5f1;
	
	/* 多余10px，设置左右栏间隔 */
  margin-left: 210px;
}
```

### 2. 利用绝对定位（position: absolute）布局
```
<!--HTML-->
<body>
	<div id="container">
		<aside class="left">left(定宽200px)</aside>
		<main class="main">main(宽度自适应)</main>
	</div>
</body>
```

```
/***CSS***/
#container {
  /* 设置父元素相对定位 */
  /* 目的是为了让左侧栏子元素想对他进行绝对定位 */
  position: relative;
}

.left {
	width: 200px;
	background: #61daa5;
	
	/* 左侧栏绝对定位 */
  position: absolute;
  top: 0;
  left: 0;
}

main {
	background: #78a5f1;
	
  /* 多余10px，设置左右栏间隔 */
	margin-left: 210px;
}
```

### 3. 利用table/table-cell布局

注意，这种办法中，如果要设置左右栏的间隔，`margin`/`padding`在`table`/`table-cell`元素中不起作用。需要使用`border-collapse`/`border-spacing`设置。具体可以参考[这篇文章](https://muki.tw/tech/fix-display-table-cell-not-affected-by-margin/)。

```
<!--HTML-->
<body>
	<div id="container">
		<aside class="left">left(定宽200px)</aside>
		<main class="main">main(宽度自适应)</main>
	</div>
</body>
```

```
/***CSS***/
#container {
  /* 设置左右两栏元素的父元素 */
	display: table;
  /* 一定要设置宽度100%*/
	width: 100%;
}

.left, .main {
  /* 设置左右两栏元素 */
	display: table-cell;
}

.left {
	width: 200px;
	height: 100vh;
	background: #61daa5;
}

.main {
	height: 100vh;
	background: #78a5f1;
}
```

### 4. 利用弹性盒（flex）布局
```
<!--HTML-->
<body>
	<div id="container">
		<aside class="left">left(定宽200px)</aside>
		<main class="main">main(宽度自适应)</main>
	</div>
</body>
```

```
/***CSS***/
/* 设置父元素为弹性盒布局*/
#container {
	display: flex;
}

.left {
	width: 200px;
	background: #61daa5;
}

main {
	background: #78a5f1;
	
  /* 设置flex: 1，表示该元素占满所有剩余空间*/
	flex: 1;
  /* 左右栏间隔10px */
  margin-left: 10px;
}
```

### 5. 利用栅格（Grid）布局

关于grid布局的基础知识，大家可以参考[MDN-网格布局](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout)和[阮一峰-Grid 布局教程](http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)以及[张鑫旭-写给自己看的display: grid布局教程](https://www.zhangxinxu.com/wordpress/2018/11/display-grid-css-css3/)三篇文章深入学习。

```
<!--HTML-->
<body>
	<div id="container">
		<aside class="left">left(定宽200px)</aside>
		<main class="main">main(宽度自适应)</main>
	</div>
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
	grid-template-columns: 200px auto;
}


.left {
	height: 100vh;
	background: #61daa5;
}

.main {
	height: 100vh;
	background: #78a5f1;
	
  /* 左右栏间隔10px */
	margin-left: 10px;
}
```

[1]: /medias/article/coding/two-columns-layout/two-columns-layout.png

### 结语

关于两栏式布局最常用的方法就是这些。本文总结的是左侧定宽，右侧宽度自适应的情况。而右侧定宽，左侧宽度自适应的实现方法与上面很类似。有兴趣的同学可以参考上面的方法试一试。