---
title: ES6 - 箭头函数
date: 2019-03-24 15:44:41
categories: 编程
tags:
- ES6
- Function
- 箭头函数
img: /medias/article/featureimages/arrow-function.webp
---
> 这篇文章我们来了解一下ES6中的箭头函数。首先会介绍一下箭头函数的基本语法，因为基本语法比较好理解，我们用示例做简单介绍即可。之后，我们重点来讨论一下箭头函数与普通函数之间的区别。

### 一、基本语法
ES6中允许使用箭头`=>`来定义箭头函数，具体语法，我们来看一个简单的例子：
```
// 箭头函数
let fun = (name) => {
    // 函数体
    return `Hello ${name} !`;
};

// 等同于
let fun = function (name) {
    // 函数体
    return `Hello ${name} !`;
};
```

可以看出，定义箭头函在数语法上要比普通函数简洁得多。箭头函数省去了`function`关键字，采用箭头`=>`来定义函数。函数的参数放在`=>`前面的括号中，函数体跟在`=>`后的花括号中。

**关于箭头函数的参数：**
 **①** 如果箭头函数没有参数，直接写一个空括号即可。
 **②** 如果箭头函数的参数只有一个，也可以省去包裹参数的括号。
 **③** 如果箭头函数有多个参数，将参数依次用逗号(,)分隔，包裹在括号中即可。

```
// 没有参数
let fun1 = () => {
    console.log(111);
};

// 只有一个参数，可以省去参数括号
let fun2 = name => {
    console.log(`Hello ${name} !`)
};

// 有多个参数
let fun3 = (val1, val2, val3) => {
    return [val1, val2, val3];
};
```

**关于箭头函数的函数体：**
 **①** 如果箭头函数的函数体只有一句代码，就是简单返回某个变量或者返回一个简单的JS表达式，可以省去函数体的大括号{ }。
```
let f = val => val;
// 等同于
let f = function (val) { return val };

let sum = (num1, num2) => num1 + num2;
// 等同于
let sum = function(num1, num2) {
  return num1 + num2;
};
```

 **②** 如果箭头函数的函数体只有一句代码，就是返回一个对象，可以像下面这样写：
 ```
// 用小括号包裹要返回的对象，不报错
let getTempItem = id => ({ id: id, name: "Temp" });

// 但绝不能这样写，会报错。
// 因为对象的大括号会被解释为函数体的大括号
let getTempItem = id => { id: id, name: "Temp" };
 ```

 **③** 如果箭头函数的函数体只有一条语句并且不需要返回值（最常见是调用一个函数），可以给这条语句前面加一个`void`关键字
```
let fn = () => void doesNotReturn();
```

箭头函数最常见的用处就是简化回调函数。
```
// 例子一
// 正常函数写法
[1,2,3].map(function (x) {
  return x * x;
});

// 箭头函数写法
[1,2,3].map(x => x * x);

// 例子二
// 正常函数写法
var result = [2, 5, 1, 4, 3].sort(function (a, b) {
  return a - b;
});

// 箭头函数写法
var result = [2, 5, 1, 4, 3].sort((a, b) => a - b);
```


### 二、箭头函数与普通函数的区别

#### 1、语法更加简洁、清晰
从上面的基本语法示例中可以看出，箭头函数的定义要比普通函数定义简洁、清晰得多，很快捷。

#### 2、箭头函数不会创建自己的this（重要！！深入理解！！）
我们先来看看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)上对箭头函数`this`的解释。

> 箭头函数不会创建自己的`this`，所以它没有自己的`this`，它只会从自己的作用域链的上一层继承`this`。

箭头函数没有自己的`this`，它会捕获自己在**定义时**（注意，是定义时，不是调用时）所处的**外层执行环境的`this`**，并继承这个`this`值。所以，箭头函数中`this`的指向在它被定义的时候就已经确定了，之后永远不会改变。

来看个例子：
```
var id = 'Global';

function fun1() {
    // setTimeout中使用普通函数
    setTimeout(function(){
        console.log(this.id);
    }, 2000);
}

function fun2() {
    // setTimeout中使用箭头函数
    setTimeout(() => {
        console.log(this.id);
    }, 2000)
}

fun1.call({id: 'Obj'});     // 'Global'

fun2.call({id: 'Obj'});     // 'Obj'
```

上面这个例子，函数`fun1`中的`setTimeout`中使用普通函数，2秒后函数执行时，这时函数其实是在全局作用域执行的，所以`this`指向`Window`对象，`this.id`就指向全局变量`id`，所以输出`'Global'`。
但是函数`fun2`中的`setTimeout`中使用的是箭头函数，这个箭头函数的`this`在定义时就确定了，它继承了它外层`fun2`的执行环境中的`this`，而`fun2`调用时`this`被`call`方法改变到了对象`{id: 'Obj'}`中，所以输出`'Obj'`。

再来看另一个例子：
```
var id = 'GLOBAL';
var obj = {
  id: 'OBJ',
  a: function(){
    console.log(this.id);
  },
  b: () => {
      console.log(this.id);
  }
};

obj.a();    // 'OBJ'
obj.b();    // 'GLOBAL'
```

上面这个例子，对象`obj`的方法`a`使用普通函数定义的，**普通函数作为对象的方法调用时，`this`指向它所属的对象**。所以，`this.id`就是`obj.id`，所以输出`'OBJ'`。
但是方法`b`是使用箭头函数定义的，箭头函数中的`this`实际是继承的它定义时所处的全局执行环境中的`this`，所以指向`Window`对象，所以输出`'GLOBAL'`。（**这里要注意，定义对象的大括号`{}`是无法形成一个单独的执行环境的，它依旧是处于全局执行环境中！！**）

#### 3、箭头函数继承而来的this指向永远不变（重要！！深入理解！！）
上面的例子，就完全可以说明箭头函数继承而来的`this`指向永远不变。对象`obj`的方法`b`是使用箭头函数定义的，这个函数中的`this`就**永远指向**它定义时所处的全局执行环境中的`this`，即便这个函数是作为对象`obj`的方法调用，`this`依旧指向`Window`对象。

#### 4、.call()/.apply()/.bind()无法改变箭头函数中this的指向
`.call()`/`.apply()`/`.bind()`方法可以用来动态修改函数执行时`this`的指向，但由于箭头函数的`this`定义时就已经确定且永远不会改变。所以使用这些方法永远也改变不了箭头函数`this`的指向，虽然这么做代码不会报错。

```
var id = 'Global';
// 箭头函数定义在全局作用域
let fun1 = () => {
    console.log(this.id)
};

fun1();     // 'Global'
// this的指向不会改变，永远指向Window对象
fun1.call({id: 'Obj'});     // 'Global'
fun1.apply({id: 'Obj'});    // 'Global'
fun1.bind({id: 'Obj'})();   // 'Global'
```

#### 5、箭头函数不能作为构造函数使用
我们先了解一下构造函数的new都做了些什么？简单来说，分为四步：
 ① JS内部首先会先生成一个对象；
 ② 再把函数中的this指向该对象；
 ③ 然后执行构造函数中的语句；
 ④ 最终返回该对象实例。

但是！！因为箭头函数没有自己的`this`，它的`this`其实是继承了外层执行环境中的`this`，且`this`指向永远不会随在哪里调用、被谁调用而改变，所以箭头函数不能作为构造函数使用，或者说构造函数不能定义成箭头函数，否则用`new`调用时会报错！

```
let Fun = (name, age) => {
    this.name = name;
    this.age = age;
};

// 报错
let p = new Fun('cao', 24);
```

#### 6、箭头函数没有自己的arguments
箭头函数没有自己的`arguments`对象。在箭头函数中访问`arguments`实际上获得的是外层局部（函数）执行环境中的值。

```
// 例子一
let fun = (val) => {
    console.log(val);   // 111
    // 下面一行会报错
    // Uncaught ReferenceError: arguments is not defined
    // 因为外层全局环境没有arguments对象
    console.log(arguments); 
};
fun(111);

// 例子二
function outer(val1, val2) {
    let argOut = arguments;
    console.log(argOut);    // ①
    let fun = () => {
        let argIn = arguments;
        console.log(argIn);     // ②
        console.log(argOut === argIn);  // ③
    };
    fun();
}
outer(111, 222);
```
上面例子二，①②③处的输出结果如下：
![箭头函数的arguments][1]
很明显，普通函数`outer`内部的箭头函数`fun`中的`arguments`对象，其实是沿作用域链向上访问的外层`outer`函数的`arguments`对象。

**可以在箭头函数中使用rest参数代替arguments对象，来访问箭头函数的参数列表！！**

#### 7、箭头函数没有原型prototype
```
let sayHi = () => {
    console.log('Hello World !')
};
console.log(sayHi.prototype); // undefined
```

#### 8、箭头函数不能用作Generator函数，不能使用yeild关键字

这一点等到讲解Generator函数的时候我们在深入讨论。


[1]: /medias/article/coding/arrow-function/arrow-function-arguments.png