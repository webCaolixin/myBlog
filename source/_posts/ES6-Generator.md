---
title: ES6 - Generator函数
date: 2019-03-28 20:13:32
categories: 编程
tags:
- ES6
- Generator函数
img: /medias/article/featureimages/ES6.webp
---

### 一、什么是Generator函数
`Generator`函数是ES6标准中提出的一种异步编程的解决方案。这种函数与普通函数最大的区别在于它可以暂停执行，又可以从暂停的位置恢复继续执行。

从语法上看，`Generator`函数就是一个状态机，封装了许多内部状态。

从实质上看，`Generator`函数就是一个遍历器对象生成器。（关于遍历器对象，可以参考阮一峰老师的[这篇文章](http://es6.ruanyifeng.com/#docs/iterator)）`Generator`函数返回一个遍历器对象，遍历这个对象，就可以依次得到函数内部的每一个状态。


### 二、基本语法
#### 1、定义Generator函数
定义一个Generator函数和定义一个普通函数的区别在于：
 * function关键字和函数名之间有一个 `*`（星号）。
 * 函数内部使用`yield`来定义每一个函数内部的状态。
 * 如果函数内部有`return`语句，那么他就是函数内部的最后一个状态。

来看一个简单的例子：
```
// 定义
function* sayHello() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
// 调用
// 注意，hw获取到的值是一个遍历器对象
let g = sayHello();
```
上面的例子，定义了一个名为`sayHello`的Generator函数，它内部有两个`yield`表达式和一个`return`表达式。所以，该函数内部有三个状态：`hello`，`world` 和 `return`语句（结束执行）。最后，调用这个函数，得到一个遍历器对象并赋值给变量`g`。

Generator函数的调用方法与普通函数完全一样，`函数名()`。不同的是：
 * 函数调用后，内部代码（从第一行开始）都不会立即执行。
 * 函数调用后会有一个返回值，这个值是一个指向内部状态的指针对象，实质就是一个包含函数内部状态的遍历器对象。

Generator函数调用后不会立即执行，那么，我们如何让它开始执行内部的代码呢？又如何获取它内部的每一个状态呢？此时，我们必须调用返回的生成器对象的.next()方法，才能开始代码的执行，并且使得指针移向下一个状态。

以上面的例子为例：
```
g.next();
// { value: 'hello', done: false }
g.next();
// { value: 'world', done: false }
g.next();
// { value: 'ending', done: true }
g.next();
// { value: undefined, done: true }
```
上面的代码中，一共调用了四次`g`这个遍历器对象的`.next()`方法。第一次调用，`sayHello`这个Generator函数开始执行，直到遇到第一个`yield`表达式就会暂停执行。`.next()`方法会返回一个对象，它的`value`属性就是当前`yield`表达式的值`hello`，`done`属性的值`false`，表示遍历还没有结束。

第二次再调用`.next()`，就会执行到第二个`yield`表达式处，并暂停执行，返回对应的对象。

第三次调用`.next()`，函数执行到最后的`return`语句，此时标志着遍历器对象`g`遍历结束，所以返回的对象中`value`属性值就是`return`后面所跟的值`ending`，`done`属性值为`true`，表示遍历已经结束。

第四次以及后面在调用.next()方法，返回的都会是`{value: undefined, done: true }`。


#### 2、yield表达式
由Generator函数返回的遍历器对象，只有调用`.next()`方法才会遍历到下一个内部状态，所以这其实是提供了一种可以暂停执行的函数，`yield`表达式就是暂停标志。

遍历器对象的`.next()`方法的运行逻辑如下。
 1. 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
 2. 下一次调用`.next()`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
 3. 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
 4. 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

**值得注意的是：**

 1. **`yield`关键字只能出现在Generator函数中，出现在别的函数中会报错。**
```
  // 出现在普通函数中，报错
  (function () {
    yield 'hello';
  })()

  // forEach不是Generator函数，报错
  [1, 2, 3, 4, 5].forEach(val => {
    yield val
  });
```

 2. **`yield`关键字后面跟的表达式，是惰性求值的。** 只有当调用`.next()`方法、内部状态暂停到当前`yield`时，才会计算其后面跟的表达式的值。这等于为JavaScript提供了手动的“惰性求值”的语法功能。
```
function* step() {
  yield 'step1';

  // 下面的yield后面的表达式不会立即求值，
  // 只有暂停到这一行时，才会计算表达式的值。
  yield 'step' + 2;

  yield 'setp3';
  return 'end';
}
```

 3. **`yield`表达式本身是没有返回值的，或者说它的返回值为`undefined`**。使用.next()传参可以为其设置返回值。（后面会讲到）
```
function* gen() {
  for (let i = 0; i < 5; i++) {
    let res = yield;  // yield表达式本身没有返回值
    console.log(res); // undefined
  }
}
let g = gen();
g.next();   // {value: 0, done: false}
g.next();   // {value: 1, done: false}
g.next();   // {value: 2, done: false}
```

**yield与return的异同：**

相同点:
 * 两者都能返回跟在其后面的表达式的值。

不同点：
 * yield表达式只是暂停函数向后执行，return是直接结束函数执行。
 * yield表达式可以出现多次，后面还可以有代码。return只能出现一次，后面的代码不会执行，在一些情况下还会报错。
 * 正常函数只能返回一个值，因为只能执行一次return。Generator函数可以返回一系列的值，因为可以有任意多个yield。


#### 3、.next()方法传参
前面我们说到过，`yield`表达式自身没有返回值，或者说返回值永远是`undefined`。但是，我们可以通过给`.next()`方法传入一个参数，来设置**上一个（是上一个）**`yield`表达式返回值。

来看一个例子：
```
function* conoleNum() {
  console.log('Started');
  console.log(`data: ${yield}`);
  console.log(`data: ${yield}`);
  return 'Ending';
}
let g = conoleNum();

g.next();      // 控制台输出：'Started'

g.next('a');   // 控制台输出：'data: a'
// 不传入参数'a'，就会输出'data: undefined'

g.next('b');   // 控制台输出：'data: b'
// 不传入参数'a'，就会输出'data: undefined'
```

上面的例子，需要强调一个不易理解的地方。

第一次调用`.next()`，此时函数暂停在代码第三行的`yield`表达式处。记得吗？`yield`会暂停函数执行，**此时打印它的`console.log()`，也就是代码第三行的console，由于暂停并没有被执行，所以不会打印出结果**，只输出了代码第二行的'Started'。

当第二次调用`.next()`方法时，传入参数`'a'`，函数暂停在代码第四行的`yield`语句处。此时参数`'a'`会被当做**上一个`yield`表达式**的返回值，也就是代码第三行的`yiled`表达式的返回值，所以此时控制台输出`'data: a'`。而代码第四行的`console.log()`由于暂停，没有被输出。

第三次调用，同理。所以输出`'data: b'`。


#### 4、Generator.prototype.throw()
Generator函数返回的遍历器对象，都有一个`.throw()`方法，**可以在函数体外抛出错误，然后在Generator函数体内捕获**。
```
function* gen() {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var g = gen();
// 下面执行一次.next()
// 是为了让gen函数体执行进入try语句中的yield处
// 这样抛出错误，gen函数内部的catch语句才能捕获错误
g.next();

try {
  g.throw('a');
  g.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
```
上面例子中，遍历器对象`g`在`gen`函数体外连续抛出两个错误。第一个错误被`gen`函数体内的`catch`语句捕获。`g`第二次抛出错误，由于`gen`函数内部的`catch`语句已经执行过了，不会再捕捉到这个错误了，所以这个错误就会被抛出`gen`函数体，被函数体外的`catch`语句捕获。

**值得注意的是：**
 * 如果Generator函数内部没有部署`try...catch`代码块，那么遍历器对象的`throw`方法抛出的错误，将被外部`try...catch`代码块捕获。
 * 如果Generator函数内部和外部都没有部署`try...catch`代码块，那么程序将报错，直接中断执行。

**遍历器对象的`throw`方法被捕获以后，会附带执行一次`.next()`方法，代码执行会暂停到下一条`yield`表达式处**。看下面这个例子：
```
function* gen(){
  try {
    yield console.log('a');
  } catch (e) {
    console.log(e);   // 'Error'
  }
  yield console.log('b');
  yield console.log('c');
}
var g = gen();

g.next();   // 控制台输出：'a'

g.throw('Error');  // 控制台输出：'b'
// throw的错误被内部catch语句捕获，
// 会自动在执行一次g.next()

g.next();   // 控制台输出：'c'
```


#### 5、Generator.prototype.return()
Generator函数返回的遍历器对象，还有一个`.return()`方法，可以返回给定的值，并且直接结束对遍历器对象的遍历。
```
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}
var g = gen();

g.next();        // { value: 1, done: false }

// 提前结束对g的遍历。尽管yield还没有执行完
// 此时done属性值为true，说明遍历结束
g.return('foo'); // { value: "foo", done: true }

g.next();        // { value: undefined, done: true }
```

如果`.return()`方法调用时，不提供参数，则返回值的`value`属性为`undefined`。


#### 6、yield* 表达式
`yield*` 用来在一个Generator函数里面执行另一个Generator函数。

如果在一个Generator函数内部，直接调用另一个Generator函数，默认情况下是没有效果的。
```
function* gen1() {
  yield 'a';
  yield 'b';
}
function* gen2() {
  yield 'x';
  // 直接调用gen1()
  gen1();
  yield 'y';
}
// 遍历器对象可以使用for...of遍历所有状态
for (let v of gen2()){
  只输出了gen1的状态
  console.log(v);   // 'x' 'y'
}
```
上面的例子中，`gen1`和`gen2`都是Generator函数，在`gen2`里面直接调用`gen1`，是不会有效果的。

这个就需要用到 `yield*` 表达式。
```
function* gen1() {
  yield 'a';
  yield 'b';
}
function* gen2() {
  yield 'x';
  // 用 yield* 调用gen1()
  yield* gen1();
  yield 'y';
}

for (let v of gen2()){
  输出了gen1、gen2的状态
  console.log(v);   // 'x' 'a' 'b' 'y'
}
```

### 小节
本文主要讲解Generator函数的基本语法和一些细节，Generator函数的定义、`yield`表达式、`.next()`方法及传参、`.throw()`方法、`.return()`方法以及 `yield*` 表达式。

文章开头讲到，Generator函数时ES6提出的异步编程的一种解决方案。在实际应用中，**一般在`yield`关键字后面会跟随一个异步操作，当异步操作成功返回后调用`.next()`方法，将异步流程交给下一个yield表达式。**具体关于Generator函数的异步应用，大家可以参考阮一峰老师的[这篇文章](http://es6.ruanyifeng.com/#docs/generator-async)，或参考其他网上资料，继续深入学习。