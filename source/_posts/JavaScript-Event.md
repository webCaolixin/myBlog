---
title: JavaScript - 事件（基础篇）
date: 2019-03-04 10:17:13
categories: 编程
tags: JavaScript
img: /medias/article/featureimages/JavaScript.jpg
---
### 什么是事件

> 事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间。——《JavaScript高级程序设计》
> 事件就是用户或者浏览器自身执行的某种动作。——《JavaScript高级程序设计》

通俗地讲，事件就是用户与文档页面、浏览器交互时做的事情，或者浏览器自身做的某些事情。比如：当用户点击页面中某个元素触发的click事件，在文本框输入文字时的input事件，关闭当前页面的unload事件，调整浏览器窗口大小的resize事件等等...


### 事件的作用

> 事件最早是在 IE3 和 Netscape Navigator 2 中出现的，当时是作为分担服务器运算负载的一种手段。——《JavaScript高级程序设计》
> JS与HTML之间的交互就是通过事件来实现的。——《JavaScript高级程序设计》

所以很明显，总结起来，事件的核心作用有两点：
1. 把一些允许的业务逻辑处理，数据计算在前端完成，分担服务器的运算负载。（在事件处理程序中完成）
2. 完成JS代码和页面HTML的一些交互。

我们都知道，一个HTML文档是一个树状的DOM结构。那么，试想这样一个DOM情况：

```
<div id="grand">
  <div id="parent">
    <div id="child"></div>
  </div>
</div>
```

上面这三个依次嵌套的div元素，当点击最内层的div#child元素时，是否也算点击了嵌套在它外面的#parent、#grand元素呢？如果是，那么是先点击了哪个元素呢？到底应该是哪个元素的click事件会先触发呢？这就涉及到接下来要说的事件流的概念。

### 事件流

> 事件流是用来描述从页面中接收事件的顺序。——《JavaScript高级程序设计》

针对上面提出的问题，事件流就是来描述：当点击了#child时，到底是#child、#parent、#grand中哪个元素先触发click事件。

**事件流分为`捕获流（事件捕获）`、`冒泡流（事件冒泡）`两种。**

#### 事件冒泡

> 事件由最具体（嵌套层次最深）的元素最先接收，逐级向上传播，直到传播到`document`对象为止（大部分浏览器会将事件一直冒泡到`window`对象上）。

上面的例子，事件冒泡的过程就如下图所示：
![事件冒泡流][1]

#### 事件捕获

> 事件由最不具体（嵌套层次最外层）的元素最先接收，逐级向下传播，直到事件的目标元素为止。

事件捕获的用意在于：在事件到达目标元素前捕获（拦截）它。上面的例子，事件捕获的过程就如下图所示：

![事件捕获流][2]

**“DOM2级事件”规范要求：事件捕获的起始对象和事件冒泡的终止对象为`document`对象，但大部分浏览器都把这一对象设置为`window`。**

#### DOM事件流

> “DOM2级事件”规定的事件流包括三个阶段：`事件捕获阶段、处于目标阶段、事件冒泡阶段`。

所以上面的例子在“DOM2级事件流”规范中，事件触发的事件流就变成了这样：

![DOM事件流][3]

**这里有一些值得注意的地方：**
1. “事件捕获阶段”到事件的目标元素紧邻的父级元素（上面的例子中就是到第**⑤**步div#parent元素）就结束了，之后事件流进入“处于目标元素阶段”，目标元素的事件被触发。
2. 目标元素的事件处理（也就是执行事件处理程序）又被看成是“冒泡阶段”的一部分。这也就是为什么上面的图中阶段**⑥**会显示在事件冒泡阶段这一侧的原因。
3. 之后，事件流进入事件“冒泡阶段”。

尽管“DOM2级事件”规范要求：捕获阶段不会触发目标元素的事件，但大部分浏览器都会在捕获阶段也触发目标元素的事件。这样，主流浏览器的DOM事件流就变成了这样：

![主流浏览器中的DOM事件流][4]

这样的事件流，就导致一个结果：**在“事件捕获阶段”和“事件冒泡阶段”都可以对目标元素的事件进行处理。**

我们不仅需要了解元素的事件以什么顺序触发，何时出发，更重要的是，当元素的事件被触发时，我们经常需要做点什么，这就是我们下来要讲的事件处理程序。

### 事件处理程序

> 当某事件发生时，响应事件的函数就叫做事件处理程序（或事件侦听器）。——《JavaScript高级程序设计》

通俗地说，事件处理程序就是事件发生时我们响应事件对应要执行的JS代码。

事件处理程序名由 "on"+事件名组成。如：onclick，onload...

那如何为一个元素指定（定义/注册）事件处理程序呢？有三种方式：
1. HTML事件处理程序（行内式）
2. DOM0级事件处理程序
3. DOM2级事件处理程序

下面，我们对这三种方式的核心与值得注意的地方做以说明。

#### HTML事件处理程序（行内式）

> 所有元素支持的事件都可以用与事件处理程序名相同的HTML属性来指定，属性值就是需要执行的JS代码。

如：
```
<button onclick="alert('你好！')"></button>
```

当事件处理程序的逻辑比较复杂，代码较多时，可以将代码定义成一个函数，将属性值赋值为`函数的调用`。

如：
```
<button onclick="sayHello()"></button>

<script>
  function sayHello() {
    alert('你好！');
  }
</script>
```

**以这种方式指定事件处理程序值得注意的是：**
1. 函数是运行在元素的作用域中的，所以函数中的this指向绑定该事件的元素本身。
2. 事件处理程序中的代码执行时，有权访问全局作用域中的任何代码。

**这种方法也存在问题和局限性：**
1. 时差问题。当绑定事件的元素渲染到页面上时，其对应的事件处理程序脚本可能还未加载，此时触发事件，可能会导致出错。
2. HTML和JS脚本紧密耦合，不方便维护。

正是存在以上原因，许多开发人员摒弃了这一方法，转而使用JavaScript的方式来定义事件处理程序。

#### DOM0级事件处理程序

> 将一个函数赋值给目标元素对象的事件处理程序属性。——《JavaScript高级程序设计》

通俗地讲，我们获取一个DOM元素的引用（DOM对象），该对象具有一个和事件处理程序名相同的属性（或者叫方法）。将要执行的事件处理程序定义成函数，并将函数赋值给这一属性。
如：
```
let btn = document.getElementById("btn"); 
btn.onclick = function(){ 
  alert("你好！"); 
}; 
```

**以这种方式指定事件处理程序值得注意的是：**
1. 函数运行在元素的作用域中，所以函数中的this指向绑定该事件的元素。（上面的例子中，this指向#btn元素）
2. 该方法指定的事件处理程序是在`事件冒泡阶段`被调用执行的。
3. DOM0级方式不能为同一元素指定多个事件处理程序，如果为同一元素指定了多个事件处理程序，触发事件只会执行最后一次指定的事件处理程序，因为后指定的事件处理程序会覆盖前面指定的。

如果要移除以DOM0级事件处理程序方式指定的事件，将元素对象的事件属性值置为`null`即可。如：
```
btn.onclick = null;
```

#### DOM2级事件处理程序
> “DOM2级事件”中，用addEventListener()方法和 removeEventListener()方法来为DOM元素注册和移除事件处理程序。

这两个方法都接受三个参数，依次是：
1. 事件名。（String，如：'click'...）
2. 事件处理函数。（Funtion）
3. 是否在事件捕获阶段触发调用。（Boolean，true——捕获阶段执行，false——（默认值）冒泡阶段执行）

下面是一个简单的例子：
```
let btn = document.getElementById("myBtn");
// 注意，第二个参数为事件名"click"，没有"on"
btn.addEventListener("click", function(){
  alert('你好！');
}, false);
// false表示事件在冒泡阶段被触发。
```

**以这种方式指定事件处理程序值得注意的是：**
1. 函数运行在元素的作用域中，所以函数中的this指向绑定该事件的元素。（上面的例子中，this指向#btn元素）
2. 用addEventListener()添加的事件处理程序`必须且只能`用removeEventListener()来移除。
3. 用removeEventListener()移除事件处理程序时，第二个参数事件处理函数，必须是添加时那个事件处理函数的引用。

对于上述第3点，下面是一个错误的例子：
```
let btn = document.getElementById("myBtn");
btn.addEventListener("click", function(){
  alert('你好！');
}, false);

...

/**
 * 错误！！！
 * 像下面这样移除前面为#btn元素注册的click事件是无效的，
 * 因为虽然这里的第二个参数与上面添加事件时定义的函数内容相同，
 * 但是这是两个相互独立的匿名函数，并不是同一个函数的引用。
 **/
btn.removeEventListener("click", function(){
  alert('你好！');
}, false); 
```

正确的移除方法，应该是这样：

```
let btn = document.getElementById("myBtn");
let handler = function(){
  alert('你好！');
};
btn.addEventListener("click", handler, false);

...

// 第二个参数handler是对同一事件处理函数的引用，有效！
btn.removeEventListener("click", handler, false);
```

**讲到这里，想来说说DOM0级和DOM2级事件处理程序之间存在的一些差别：**
1. DOM0级方式，不能为同一元素指定多个事件处理程序，DOM2级可以指定多个。
2. 如果采用DOM0级方式为同一元素指定了多个事件处理程序，触发事件只会执行最后一次指定的事件处理程序，因为后面指定的事件处理程序会覆盖前面指定的。
3. DOM2级方式指定的多个事件处理程序，事件触发时会按照其添加的顺序依次执行。

最后，我们来讲讲让很多前端开发者都头疼的IE浏览器 —— IE浏览器中的事件处理程序

#### IE事件处理程序

> IE 实现了与 DOM 中类似的两个方法：attachEvent()和 detachEvent()来为元素添加和移除事件处理程序。

这两个方法都接受两个参数，依次是：
1. 事件处理程序名。（String，如：'onclick'...，这和DOM2级不同！！！）
2. 事件处理函数。（Funtion）

上面的例子，在IE中代码如下：
```
let btn = document.getElementById("btn"); 
// 注意！！！这里第一个参数和DOM2级不同！！！为"onclick",DOM2为"clcik"
btn.attachEvent("onclick", function(){ 
  alert("你好！"); 
}); 
```

**IE事件处理程序中值得注意的是：**
1. 由于 IE8 及更早版本只支持事件冒泡，所以通过attachEvent()添加的事件处理程序都会被添加到`事件冒泡阶段`。
2. 通过attachEvent()添加的事件处理程序`运行在全局作用域`（而不像DOM0级和DOM2级那样运行在元素作用域）。所以，函数中this指向`window`对象。
3. 与 addEventListener()类似，attachEvent()方法也可以为同一个元素添加多个事件处理程序。
4. 但是，attachEvent()方法添加的多个事件处理程序，事件触发时会按照与添加顺序相反的顺序来执行。
5. 与removeEventListener()类似，用detachEvent()移除事件处理程序，第二个参数——事件处理函数，必须是添加时那个事件处理函数的引用。

[1]: /medias/article/coding/event/event-bubbling.jpg
[2]: /medias/article/coding/event/event-capturing.jpg
[3]: /medias/article/coding/event/dom-event-flow.jpg
[4]: /medias/article/coding/event/dom-event-flow-browser.jpg