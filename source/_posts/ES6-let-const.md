---
title: ES6 - let、const、var的区别
date: 2019-03-15 22:40:21
categories: 编程
tags: ES6
img: /medias/article/featureimages/ES6.jpg
---
> 为了使JavaScript语言可以用来编写复杂的大型应用程序，成为企业级开发语言，`ECMAScript 6.0`（简称`ES6`）在标准中添加了很多新的特性。我们将用几篇文章总结一下ES6标准中一些常用的新特性。本片文章主要讲解ES6中的`let`、`const`命令，并区分其与`var`命令的区别。

### let 与 var 的区别

#### 一、let声明的变量只在其所在的块级作用于有效

所谓块级作用域是指：将多个代码语句封装在一起，通常是包含在一个大括号中，没有返回值。比如：
```
if (true) {  // 块级作用域  }

for (let i = 0; i < 10; i++) {  // 块级作用域  }

while (true) {  // 块级作用域  }

switch (case) {  // 块级作用域  }
```

以上例子，大括号（{...}）中形成的都属于块级作用域。

众所周知，在ES6之前，JavaScript中只有全局作用域和局部（函数）作用域，不存在块级作用域。而且也只能使用关键字`var`来声明变量。所以用`var`声明的变量要么是属于全局作用域的全局变量，要么就是属于局部（函数）作用域的局部变量。

在ES6标准中，添加了使用`let`声明变量的方式。使用`let`声明的变量只在块级作用域中有效，在其外层作用域访问时就会报错。

```
if (true) {
    // 这个用let声明的变量a，只在当前块级作用域中有效
    let a = 123;
    // 这个用var声明的变量b，在全局作用域中都有效
    var b = '123';

    console.log(a);     // 123
    console.log(b);     // '123'
}

console.log(a);     // 报错 —— ReferenceError: a is not defined.
console.log(b);     // '123'
```

上面的例子中，因为变量`a`是使用`let`声明的，它只在其所在的块级作用域——`if`后面的大括号({...})之中有效，在块级作用域外层访问时就会报错。而用`var`声明的变量`b`，不受块级作用域的约束，可以跨块级作用域访问。这个例子中，变量`b`实际是属于全局作用域的全局变量。

那么，为什么ES6中需要引入块级作用域的概念呢？为什么要增加使用`let`来声明变量的方式呢？

因为，如果没有块级作用域会导致一些不合理的情形出现。

1、 内层变量可能会覆盖外层变量。
```
var a = 'Global';

function inner() {
    if (true) {
        console.log(a);     // undefined
        var a = 'inner';
        /**
         * 以上两行代码相当于
         * var a;
         * console.log(a);
         * a = 'inner'; 
         * 再次使用var声明同名变量a，会覆盖全局变量a
         */
    }
}

inner();
```
这个例子，当在函数`inner`内部`if`代码块内首先访问变量`a`时，却得到的是`undefined`。这是因为紧随其后`var`声明的同名变量`a`会变量提升并覆盖全局变量`a`。所以打印出`a`的值为`undefined`。

2、计数的循环变量会泄露为全局变量
```
for (var i = 0; i < 10; i++) {
    // 一些循环操作
}

console.log(i);     // 10
```

上面的例子，`for`循环中的循环变量按道理来说应该只属于`for`循环体，循环结束就不能再访问。但实际这样用`var`声明的`i`，属于外层作用域中的变量，也就是说`i`泄露为全局变量。所以当执行到`console.log(i)`时，因为`i`经过循环已经增加到`10`，所以打印出`i`的值为`10`。

#### 二、let声明的变量不存在变量提升过程

用`var`声明的变量，会在其作用域中**发生`变量提升`的过程**。变量会被提升到作用域顶部，JS默认给变量一个`undefined`值。在使用`var`声明一个变量前访问它，得到的值永远是`undefined`。

但是，在ES6中**使用`let`声明的变量，不存在`变量提升`过程**。也就是说，不能在使用`let`声明任何一个变量前访问它，否则都会报错。

```
console.log(a);     // 报错——ReferenceError: a is not defined

let a = 'Hello World!'；
```

#### 三、let声明的变量存在“暂时性死区”

只要使用`let`声明了一个变量，那这个变量就“绑定”到了这个作用域（全局/局部/块级），该变量就不再受外层作用域的影响。

ES6明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为`“暂时性死区”`（temporal dead zone，简称 TDZ）。

```
let g = 'Global';

if (true) {
    g = 'Block';    // 报错——ReferenceError: g is not defined
    let g;
}
```
上面的例子中，`if`代码块最顶部一直到`let`声明变量`g`之前，都是`g`的“暂时性死区”。在该范围内访问`g`都会报错。

#### 四、let声明的变量不允许再次重复声明

使用`var`声明变量，可以多次重复声明一个同名变量。最终变量的值为最后一次声明赋值的结果。

```
var a = 123;
var a = 'Hello World!';

console.log(a);     // 'Hello World!'
```

但是，**在同一作用域（全局/局部/块级）中不允许使用let重复声明变量。或者说不允许存在与用`let`声明的变量同名的变量**。以下代码都会报错！

```
// 先var，后let
var a = 123;
// ...一些代码
let a = 'Hello World!';     // 报错——Uncaught SyntaxError: Identifier 'a' has already been declared

// 先let，后var
let b = 123;
// ...一些代码
var b = 'Hello World!';     // 报错——Uncaught SyntaxError: Identifier 'a' has already been declared

// 先let，再let
let c = 123;
// ...一些代码
let c = 'Hello World!';     // 报错——Uncaught SyntaxError: Identifier 'a' has already been declared
```

#### 五、let声明的全局变量不会作为window对象的一个属性

使用`var`声明的全局变量，会被JS自动添加在全局对象`window`上，作为该对象的一个属性。

```
var myVar = 'myName';

console.log(window.myVar);      // 'myName'
console.log(window.hasOwnProperty('myVar'));    // true
```

但是，使用let声明的全局变量不会作为window对象的一个属性。

```
let yourVar = 'yourName';

console.log(window.yourVar);      // undefined
console.log(window.hasOwnProperty('yourVar'));    // false
```

这个例子可以看出，`let`声明的全局变量`yourVar`，并没有被添加到`window`对象上，没有作为`window`的一个属性。

### let 与const 的区别

在ES6中，上述所有`let`所具有的特性，对于`const`来说同样存在。但`const`与`let`、`var`的区别在于`const`是用来声明**常量**的。

常量具有以下特点：

#### 一、常量值不可修改

一个常量，一旦声明，任何时间、任何地点都不能修改它的值。

```
const PI = 3.1415926;

console.log(PI);    // 3.1415926

PI = 3; // 报错——Uncaught TypeError: Assignment to constant variable.
```

#### 二、常量在声明时必须必须立即初始化（赋初始值）

不能只声明一个常量名，但不对其进行初始化赋值。否则在声明常量时就会报错。

```
const PI;   // 报错——Uncaught SyntaxError: Missing initializer in const declaration

PI = 3.1415926;
```

#### 三、常量的值不可修改的实质（重要！！）

实际上，常量的值不变，是指常量指向的那个内存地址中所保存的数据不可更改。对于简单的数据类型（数值，字符串、布尔值），他们本身具体的值就保存在常量所指向的那个内存地址中，所以不能修改改简单类型的数据值。

但是，如果一个常量的值是一个引用类型值，那么常量所指向的内存地址中实际保存的是指向该引用类型值的一个指针（也就是引用类型值在内存中的地址）。所以const只能保证该引用类型地址不变，但该地址中的具体数据是可以变化的。

下面的例子，代码不会报错，可以正常运行！

```
// !!!常量OBJ中实际保存的是后面的对象在内存中的地址!!!
const OBJ = {};

/**
 * !!!!!!!!!!
 * 修改OBJ.prop1，实际只是修改了对象的属性，
 * 但并没有改变该对象在内存中的地址，
 * 所以常量OBJ并没有发生变化
 * !!!!!!!!!!
 */
OBJ.prop1 = 123;
OBJ.prop2 = 'Hello World!'

/**
 * !!!!!!!!!!
 * 下面这一行就会报错,
 * 因为此时OBJ指向了另一个对象，OBJ中保存的地址发生了变化
 * !!!!!!!!!!
 */
OBJ = {};   // 报错——Uncaught TypeError: Assignment to constant variable.
```

下面的例子和上面同理。

```
const ARR = [];
ARR.push('Hello');  // 可执行
ARR.length = 0;     // 可执行
ARR = ['Dave'];     // 报错，因为ARR重新指向了数组['Dave']所在的内存地址
```