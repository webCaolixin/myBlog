---
title: ES6-函数的扩展
date: 2019-03-22 20:35:25
categories: 编程
tags: ES6
img: /medias/featureimages/ES6.jpg
---
### 1、函数参数的默认值

在ES6之前，是不能直接给函数参数设置默认值的。如果需要设置默认值，就只能采用`||`（或）的短路原则来设置参数的默认值。

```
function say (x, y) {
    y = y || 'World';
    console.log(x + ' ' + y + '!');
}

say('Hello');           // 'Hello World!'
say('Hello', 'China');  // 'Hello China!'
say('Hello', '');       // 'Hello World!'
```

像上面这样定义函数默认值，虽然基本能达到想要的结果，但也存在一个最大的问题：如果想要传给函数的参数值本身就是`''`（空字符串,比如上面例子最后一次函数调用）、`0`、`false`、`null`这样的值，但使用`||`短路原则却会使默认值生效。

所以，我们需要在函数中加以判断，当某个参数确定未传值时，再赋默认值。

```
function say (x, y) {
    if (typeof x === 'undefined') {
        y = 'World'
    }
}
```

这样虽然解决了前面所说的问题，但是，当一个函数的参数较多时，尤其在函数参数值需要做多种数据类型判断的时候，可能需要写很多`if`语句来判断，或者`if`语句的条件会变得很复杂，这样也不利于阅读和维护。

所以，为了解决以上问题，ES6中允许在函数声明时在形参的位置就给函数参数设置默认值，直接将默认值赋值在形参后面即可。

```
function say (x, y = 'World') {
    console.log(x + ' ' + y + '!');
}

say('Hello');           // 'Hello World!'
say('Hello', 'China');  // 'Hello China!'

// 传入 ''、null、0、false都取值所传参数的值，不会使默认值生效
say('Hello', '');       // 'Hello !'
say('Hello', null);       // 'Hello null!'
say('Hello', 0);       // 'Hello 0!'
say('Hello', false);       // 'Hello false!'

// 只有参数未传值或者所传参数值严格等于undefined时，默认参数才生效
say('Hello', undefined);       // 'Hello World!'

// 构造函数也可以定义参数默认值
function Point(x = 0, y = 0) {
  this.x = x;
  this.y = y;
}
const p = new Point(1);
console.log(p);     // { x: 1, y: 0 }
```

显然，ES6这种定义函数参数默认值的方法要简单、清晰得多。**最重要的是，这种方法定义的参数默认值只有当参数未传值或者传值严格等于`undefined`的时候，默认值才会生效**。

这里，还有一些需要格外注意的地方！！

**① 函数参数（不论是否设置了默认值）都是JS内部默认声明过的，所以不能在函数体内再使用let/const重复声明。**否则，会报错！

```
function createArr(x, y = 0) {
    let x = 0;      // 报错
    let y = 0;      // 也会报错
    return [x, y];
}
```

**② 如果一个函数设置了参数默认值，则该函数任何一个参数（包括设置了默认值的参数）都不能重名！**

```
// 没有参数设置默认值，这不会报错
function fn1(x, x, y) {
  console.log(x, y);
}
// 注意，最终x输出为2。因为第二个参数覆盖了第一个
fn1(1, 2, 3);   // 2 3

// 参数设置了默认值，x又重名，所以会报错
function fn2(x, x, y = 1) {
  // ...
}
```

**③ 函数参数的默认值可以是任何JS表达式，但每调用一次函数，该表达式都会重新计算求值！**

```
let x = 99;
function cal(p = x + 1) {
  console.log(p);
}

cal();  // 100

x = 100;
cal();  // 101
```

上面的例子，每调用一次`cal`函数，参数`p`都会重新计算默认值`x + 1`，并不会记住上次调用所求得的`p`的默认值！

### 2、参数默认值可以和解构赋值结合使用

函数参数的默认值可以和解构赋值、解构赋值的默认值，结合起来使用。

#### 2.1 只使用解构赋值默认值

```
// 这里只使用了解构赋值的默认值，未使用参数默认值
function foo({x, y = 5}) {
  console.log([x, y]);
}

/**
 * 下面调用时传的参数，
 * 会与{x, y = 5}进行模式匹配，进行解构赋值
 */
foo({});        // [undefined, 5]
foo({x: 1});    // [1, 5]
foo({x: 1, y: 2});      // [1, 2]
foo();      // TypeError: Cannot read property 'x' of undefined
```

上面的例子，只使用了解构赋值的默认值，未使用参数默认值。如果将一个对象传给`foo`函数，那么这个对象就会和{x, y = 5}进行模式匹配，最终为参数解构赋值。但如果不给函数传参（例如最后一行——`foo()`），或者传参为`null`，那么就会报错。因为，`undefined`、`null`无法进行解构赋值。

上面的例子的报错问题，其实可以通过结合使用函数参数默认值的方法来避免。

#### 2.2 同时使用解构赋值默认值和参数默认值

```
// 这里不仅使用了解构赋值默认值，同时使用了函数参数默认值
function foo({x, y = 5} = {}) {
  console.log(x, y);
}

// 这样调用不会报错
foo();  // undefined 5
```

上面的例子，调用`foo`函数未传入参数。此时，首先会使函数参数的默认值（也就是声明函数时的` = {} `）生效。接下来，参数默认值`{ }`会与`{x, y = 5}`再进行解构赋值。其中解构赋值中，参数`y`具有默认值为`5`。


作为练习，请思考下面两种写法有什么区别？

```
// 写法一
function f1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function f2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

### 3、需要设置默认值的参数最好放在其余参数最后面

一般情况下，建议将需要设置默认值的参数放在其余参数最后面。原因主要有以下两点：
 * 因为这样比较容易看出来，到底省略了哪些参数。
 * 如果非尾部的参数设置默认值，实际上这个参数是没法省略的,必须显式传入`undefined`才能使默认值生效。

```
function f(x, y = 5, z) {
  return [x, y, z];
}

f();    // [undefined, 5, undefined]
f(1);   // [1, 5, undefined]
f(1, ,2);   // 省略第二个参数，会报错

// 需要显式传入undefined作为第二个参数值
f(1, undefined, 2);     // [1, 5, 2]
```

### 4、函数的rest参数

ES6引入了`rest`参数（形为`...变量名`）。这个变量名对应的值是一个数组，里面按参数传入的顺序存放着函数除了具名参数以外的剩余参数。

**剩余参数只能/必须是函数的最后一个参数**。但它有可能保存的是传入函数的所有参数值，也可能是传入函数的一部分参数值。

```
// 这个rest参数——param保存的就是所有传入参数的值
function r1(...param) {
    console.log(param);
}
r1(1, 2, 3, 4, 5);  // [1, 2, 3, 4, 5]

// 这个rest参数——param保存的就是除具名参数以外的参数值
function r2(param1, param2, ...param) {
    console.log(param1, param2, ...param)
}
r2();   // 1 2 [3, 4, 5]
```

我们可以使用`rest`参数来代替函数的`arguments`对象。最重要的是，`rest`参数是一个数组，可以使用所有数组的方法。但是，`arguments`只是一个类数组对象，很多数组的方法无法使用。

### 5、函数的length、name属性

首先，要明确一点：所有函数都是Function类型的一个实例对象。所以每个函数都有一些自己的属性和方法。最后我们就来了解一下函数对象的`length`和`name`属性。

#### 5.1、length属性

函数的`length`属性一般情况下表示函数期望接收到的参数的个数。但在设置了参数默认值或者rest参数时，会有一些变化。

```
(function (a) {}).length;           // 1

(function (a = 0, b, c) {}).length; // 0
(function (a, b = 1, c) {}).length; // 1
(function (a, b, c = 5) {}).length; // 2

(function(arg1, ...args) {}).length;      // 1  
(function(...args) {}).length;      // 0
```

可以看出：
 * 一般情况下，`length`属性返回的是函数声明时所定义的的参数的个数；
 * 当设置了参数默认值时，`length`属性返回的是**第一个设置了默认值的参数之前的参数个数，之后的参数不再计入**；
 * 当设置了`rest`参数，因为`rest`参数只能是函数的最后一个参数。所以`length`返回的是`rest`参数之前的参数个数。

#### 5.2、name属性

函数的`name`属性，返回的是该函数的函数名。

```
// 函数声明
function fun1() {}
console.log(fun1.name);  // 'fun1'（ES5/ES6）

// 函数表达式
let fun2 = function() {};
// ES5：''(空字符串)
// ES6：'fun2'
console.log(fun2.name);

// 将具名函数赋值给一个变量
let temp = function fun3() {};
console.log(temp.name);
// 'fun3'(ES5/ES6)

// Function()构造函数——返回'anonymous'
console.log((new Function).name);   // 'anonymous'

// .bind()方法返回的函数，name属性值会加上'bound '（带个空格）前缀。
function fun4() {};
console.log(fun4.bind({}).name);     // 'bound fun4'

console.log((function(){}).bind({}).name);  // 'bound '
```