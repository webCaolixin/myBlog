---
title: JavaScript - Function
date: 2019-03-12 10:46:11
categories: 编程
tags: JavaScript
img: /medias/featureimages/JavaScript.jpg
---
> JavaScript中的函数一直是一个很有讨论性的话题点。函数本身不仅仅是一个引用类型——所有函数都是Function类型的实例，更重要的是因为函数还会涉及到执行环境、作用域、闭包以及让人头疼的this等等...这篇文章我们来回顾一下与函数有关的一些值得注意的地方。

### 函数的定义

定义函数有三种方法：
1、函数声明 2、函数表达式 3、使用Function构造函数

#### 函数声明

```
function sum (num1, num2) {
  return num1 + num2;
}
```

#### 函数表达式

用函数表达式的方式定义函数，就是将一个匿名函数赋值给一个变量。
文章开头说道，所有函数都是Function类型的一个实例，所以，这个变量（引用类型）最终就是一个指向该匿名函数实例的指针。

```
var sum = function (num1, num2) {
  return num1 + num2;
}
```

以上两种函数定义方式，从效果上看是相同的，几乎相差无几，但它们之间还是存在一些细微的差别，我们稍后再讲。

#### 使用Function构造函数

因为所有函数都是Function类型的实例，所以调用`new Function()`构造函数，也可以定义函数。

```
// 构造函数中最后一个参数始终都被看成是函数体，而前面的所有参数则是枚举出的函数的形参。
var sum = new Function("num1", "num2", "return num1 +num2")
```

实际开发中，很少使用这种方法定义函数。原因主要有两点：
1. 定义不方便。如果函数体代码过长，使用这种方法，因为最后一个参数是字符串格式，不易换行高亮等。
2. 这种方式会导致解析两次代码，影响性能。第一次是解析常规JS代码，第二次是解析传入构造函数中的字符串。

#### 函数声明和函数表达式的区别（重要）

虽然函数声明和函数表达式都可以定义函数，但是解析器在处理他们的时候还是存在着一些差别。

当解析器进入一个执行环境并向环境中加载数据的时候，对于用函数声明定义的函数，解析器会率先读取他们，保证在执行任何代码之前，就可以访问调用这些函数。

```
console.log(sum(10, 20));   // 30

// ...省略很多代代码

// 函数声明定义函数
function sum (num1, num2) {
  return sum1 + sum2;
}
```

上面的例子，先调用函数`sum`，在之后的代码中再声明该函数，完全没有问题。因为，**在执行`sum(10, 20)`以及其他任何代码之前，JS解析器会先将`sum`函数的声明定义提升到当前执行环境的代码树的最顶端，保证之后随时可以访问调用该函数。这个过程叫做——`函数声明提升`。**

而用函数表达式定义函数的方式，不会存在函数声明提升的过程。也就是说，必须等到解析器执行到函数表达式定义所在的代码行之后，函数才算被定义成功，之后才可以访问调用该函数。

像下面这样调用`sum`函数式，代码运行后会报错，因为**用函数表达式定义函数不会存在函数声明提升的过程**，必须在函数表达式定义函数代码之后才能调用。

```
console.log(sum(10,10));    // 这里会报错

// ...省略很多代代码

// 函数表达式定义函数
var sum = function(num1, num2){ 
  return num1 + num2; 
};
```

**对于函数声明和函数表达式定义函数的区别，还有一点一定要更加注意：**

**首先，JavaScript是支持动态创建函数的。**也就是根据不同的需求或条件来动态定义函数。但是，动态创建须采用函数表达式的方式定义，使用函数声明方式会导致意想不到的错误。

来看下面的例子：

```
function createFunction () {
  // 像下面这样根据某个条件来动态声明一个函数很容易导致问题或出错！！！

  // arguments是函数中内置的类数组对象，本文后面会讲到
  if (arguments.length > 1) {
    function sum() {
      // ...一种定义
    }
  } else {
    function() {
      // ...另一种定义
    }
  }
}
```

实际上，上面的动态定义函数的方式，在ECMAScript中属于无效语法，JavaScript引擎会尝试修正错误，将其转换为合理的状态。但问题是浏览器尝试修正错误的做法并不一致。大多数浏览器会返回第二个声明，忽略`arguments.length`条件；Firefox会在`arguments.length > 1`时返回第一个函数声明，因此这种使用方式很危险！！！

但是利用函数表达式的方式来动态定义函数，是可以达到预想的目的的！！

上面的例子改写成下这样，就不会出现问题。

```
var sum = null;   // 也可以不赋初始值
function createFunction () {
  // arguments是函数中内置的类数组对象，本文后面会讲到
  if (arguments.length > 1) {
    sum = function() {
      // ...一种定义
    }
  } else {
    sum = function() {
      // ...另一种定义
    }
  }
}
```

仔细想想，这样能够完美达到目的也很好理解。我们先定义了一个`sum`变量，最后根据`arguments.length`是否大于0定义两个不同的匿名函数并赋值给`sum`变量，这和根据不同条件来为一个变量赋不同的值本质是完全一样的，当然不会有任何问题。

### JS中函数没有重载（重要）

不论是以哪种方式定义的函数，**函数名实际就是一个指向函数实例的指针，本质上函数名和普通变量没什么区别。**这也就好理解为什么JS中没有函数重载的概念。
看下面的例子：
```
function sum(num){ 
  return num + 100; 
} 
function sum(num) { 
  return num + 200; 
} 
console.log(addSomeNumber(100));    // 300 
```

上面这个例子中声明了两个同名`sum`函数，而结果则是后面的函数定义覆盖了前面的函数，更准确的说`sum`现在指向后面定义的函数。

为什么会这样？就是因为JS中函数不存在重载，其实上面的代码实际上与下面的代码没有什么区别。
```
var sum = function (num){ 
  return num + 100; 
}; 
sum = function (num) { 
  return num + 200; 
}; 
console.log(sum(100));    // 300 
```

原来，当第一次定义`sum`之后，`sum`指向第一个函数实例，当再次定义第二个函数时，`sum`会重新指向第二个函数实例。

### 函数可以作为值传递

我们多次强调函数名实际就是一个指向函数实例的指针，本质上函数名和普通变量没什么区别。既然是变量，那么函数也就可以作为另一些函数的参数和返回值来传递。

#### 函数作为参数
```
function add (num) {
  return num + 100;
}

function cal(calFun, num) {
  return calFun(num);
}

cal(add, 100);    // 200
```
上面的例子中，函数`cal`接收一个函数和一个数值作为参数，最终返回值是第二个参数（数值）在第一个参数（函数）中的执行结果。而第一个参数——函数中具体做的事情，就是将传入的数值参数与100求和。

#### 函数作为返回值

此外，函数也可以作为另一个函数的返回值。对上面例子进行稍微的改造，也可达到相同的目的。

```
function cal() {
  function add (num) {
    return num + 100;
  }
  // 这里返回的是add函数名，而不是函数的调用
  return add;
}

let res = cal();

res(100);     // 200
```

上面的例子，当执行`cal()`时，该函数返回了内部声明的`add`函数，我们将返回值再赋值给`res`变量，此时，`res`也就指向了`add`所指向的函数实例。之后我们再调用`res`函数，并传入参数，得到的结果也是`200`。

### 函数的内部属性

#### arguments

函数中包含一个内置的`arguments`对象，它是一个类数组对象，包含着所有传入函数中的参数，你可以用数组下标的方式访问其中的元素。

```
function show (a, b, c, d, e, f) {
  console.log(arguments[4]);    // 输出[333, 444]
}

show(1, '222', true, null, [333, 444], {aa: 'aaa', b: function() {}});
```

`arguments`对象自己还有一个`callee`属性，该属性是一个指针，指向拥有这个`arguments`对象的函数，也就是这个`arguments`所属的函数。

#### arguments.callee

在递归函数中，我们可能会经常使用到`arguments.callee`这一属性。以一个经典的阶乘函数为例:

```
// 递归阶乘的经典例子
function factorial(num) {
  if (num <=1) {
    return 1;
  } else {
    return num * factorial(num-1);
  }
}

// 递归阶乘的优化例子
function factorial(num) {
  if (num <=1) {
    return 1;
  } else {
    // 这里的arguments.callee又指向factorial函数
    return num * arguments.callee(num-1);
  }
}
```

这样写的好处是，当指向该递归函数的函数名发生变化时，递归函数也能顺利执行完成。如果采用上面经典例子的写法，当出现如下代码逻辑时，就会出错。
```
// 将另一变量指向递归阶乘函数
var trueFactorial = factorial;

// 改写原递归阶乘函数
factorial = function() {
  return 0;
};

console.log(trueFactorial(5));
console.log(factorial(5));
```

如果以递归阶乘的经典例子定义`factorial`函数，那么`trueFactorial(5)`这一行就会出错。因为执行到`trueFactorial`函数内部`return num * factorial(num-1);`代码时，`factorial`实际已经指向了`function() {return 0;}`，计算结果就会出错。但用递归阶乘的优化例子就不会出现这个问题，因为执行`trueFactorial(5)`语句时，`arguments.callee`指向`trueFactorial`函数。

#### arguments.callee.caller

`arguments.callee.caller`这个属性中保存着调用当前函数的函数的引用。也就是说我们可以通过该属性来获得`arguments`所属于的那个函数（`arguments.callee`）是在哪个函数（`arguments.callee.caller`）中被调用的。

需要注意的是：如果是在全局作用域中调用的当前函数，`arguments.callee.caller`的获取到的值为**`null`**。

ES5后，我们也可以直接通过函数实例对象的caller属性来直接访问这一属性。
```
function outer(){
  inner();
}

function inner(){
  console.log(inner.caller);    // 打印出outer函数的源码。因为inner是在outer中被调用的
}

outer();
```

也可以这么写：

```
function outer(){
  inner();
}

function inner(){
  alert(arguments.callee.caller);   // 打印出outer函数的源码。
}

outer();
```


#### this
函数中另一个有意思又令人头疼的属性——this，由于this指向涉及的内容较多，情况稍微复杂一些，我们将会专门用一篇文章来探讨this相关问题。
但概括来说，**this引用的是函数据以执行的环境对象**。当this所在的函数在全局作用域中调用时，它指向window对象；当作为对象的方法调用时，它指向该对象。