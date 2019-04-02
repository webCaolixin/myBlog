---
title: ES6 - Promise
date: 2019-03-25 22:10:52
categories: 编程
tags:
- ES6
- Promise
img: /medias/article/featureimages/ES6-Promise.webp
---
> Promise是面试中最常见的问题之一，也是ES6中很有用、很核心的一个新特性。尤其在如今异步操作越来越多、越来越复杂的情况下，Prmoise更是显示出了它强大而又优雅的本质。这篇文章，我们来系统地讲解一下Promise相关的核心知识点。

本文，我们按照以下的思路来逐步深入Promise：
 一、什么是Promise？(What)
 二、为什么需要Promise？或者说Promise的用途是什么？(Why)
 三、如何使用Promise？(How)
 四、Promise的其他方法。

 ### 一、什么是Promise

`Promise`是一种异步编程的解决方案，它可以将异步操作以同步的流程表达出来，它比传统的使用回调函数和事件来处理异步问题更加合理，更符合人们线性处理问题的逻辑。
从语法上说，`Promise`是一个对象，它里面保存着一个将来才会发生的事情（一般是一个异步操作）的状态和结果。

听起来，有些抽象，全是概念性的东西。那接下来我们看看为什么ES6中会出现`Promise`？通过具体示例，可以帮助我们更好的理解什么是`Promise`。

### 二、为什么需要Promise？它的用途是什么？

在ES6出现Promise之前，我们要处理一个异步请求，通常是这样的：
```
// 利用回调函数来处理异步请求结果
// 很多异步请求方法也会设计一些事件，在事件中处理异步请求结果
asyncRequest(function(resData) {
    // 处理请求结果
});
```

这样看着没什么问题，但需求总是各种各样甚至是变态的。如果我们需要在第一个请求返回结果后再发起第二个请求呢？再如果，第二个请求结果返回后后，我们需要再发起第三个请求呢？之后，再是第四个...第五个......此时，代码应该会变成这样：
```
asyncRequest1(function(resData1) {
    asyncRequest2(function(resData2) {
        asyncRequest3(function(resData3) {
            asyncRequest4(function(resData4) {
                asyncRequest5(function(resData5) {
                    // ......
                    // 处理请求结果
                });
            });
        });
    });
});
```

这时，代码嵌套层次太深，再加上每次请求结束我们应该还需要做一些适当的逻辑处理，这样每个处理请求结果的地方还需要额外的代码，这样整个代码块显得很臃肿，一点也不优雅！最主要的是，这样的代码很容易出错，而且出错后不容易定位错误，阅读和维护起来十分费劲。

这就是异步编程最让人头疼和无语的地方：由于异步操作嵌套层次过深而导致的“**回调地狱**”！

出现这种情况，就需要思考新的异步编程的处理方法。有没有什么方法能在满足上面例子的需求的同时又能解决这种嵌套式的回调地狱呢？能不能不使用嵌套式回调，而使用链式回调呢？肯定是有的，这也就是`Promise`出现的原因。同时，能不能最好不使用回调的方式来处理异步请求呢？当然也是可以的，这就是我们后面文章会讲的`async/awiat`。

我们来看看，上面的例子，如果使用Promise来实现是什么样子？应该是这样：
```
new Promise(asyncRequest1)
    .then(asyncRequest2(resData1))
    .then(asyncRequest3(resData2))
    .then(asyncRequest4(resData3))
    .then(asyncRequest5(resData4))
    .catch(handleError(errorMsg))
```
上面的例子，只有每个一步请求`asyncRequest`成功返回结果，才会进入下一个`.then()`方法中，从而进行下一个异步请求......以此类推。当任何一个请求出错时，就会进入`.catch()`方法中，可以在这里处理错误。这样的链式回调，既满足前面例子的需求，同时又避免了嵌套回调，从而避免了“回调地狱”的出现。

这里具体的语法看不懂，不要紧！不要慌！这个例子只是为了说明`Promise`是如何用链式回调来解决嵌套回调地狱的。接下来，我们就来说说如何使用`Promise`，讲讲它的基本语法。

### 三、如何使用Promise（基本语法）

#### 1、创建一个Promise对象实例
ES6中规定，`Promise`是一个构造函数，可以用来实例化一个`Promise`对象。下面是一个简单的例子：
```
// Promise构造函数接收一个函数作为参数
let promise = new Promise(Function);
```
#### 2、Promise的状态及其改变
文章最开始介绍什么是Promise的时候说过：`Promise`是一个对象，它里面保存着一个将来才会发生的事情（一般是一个异步操作）的状态和结果。

我们先来看看Promise代表的异步操作的状态有哪几种？——一共只有三种状态：
 1. pending（进行中）
 2. fulfilled（已成功）
 3. rejected（已失败）

这三种状态，不会共存，`Promise`只会处于其中某一种状态。当异步请求开始并且未结束（没有返回结果）时，处于`pending`状态。当异步请求返回结果后，可以根据请求返回的结果将`Promise`的状态修改为`fulfilled`或者`rejected`。并且，一旦`Promise`的状态第一次改变，就再也不能更改为其它任何状态。所以，`Promise`的状态改变过程**只有**两种情况：
 1. pending --> fulfilled（进行中 --> 已成功）
 2. pending --> rejected（进行中 --> 已失败）

那么，如何修改`Promise`的状态呢？这就需要了解调用`Promise`构造函数时，传递给构造函数的`Function`参数了。`Promise`会为这个函数设置两个参数，`resolve`、`reject`。这两个参数是两个函数，由JavaScript引擎提供，不用自己部署。

`resolve()`函数，可以将`Promise`的状态由`pending`改变为`fulfilled`。`reject()`函数，可以将`Promise`的状态由`pending`改变为`rejected`。

**这里有两点需要注意的地方！！**
 1. 在`Promise`内部只有用`resolve()`、`reject()`才能改变它的状态。`return`任何值（包括一个Error实例）都不会改变它的状态。
 2. `resolve()`、`reject()`和`return`的意义不同。它们只是改变了`Promise`的状态，并不会结束代码执行。也就是说`resolve()`、`reject()`之后的代码依旧会执行。（虽然不建议在他们后面再有代码出现）
 3. 在定义`Promise`时，参数函数中异步操作之外的同步代码都会立即执行。

来看一个示例，简单明了地理解上面的文字。
```
let promise = new Promise(function(resolve, reject) {
    // 下面两行代码会立即执行，不会等待异步操作结果返回、状态改变
    let a = '123';
    console.log(a); // '123'

    // 一个异步请求
    asyncRequest(function(resData) {
        if (/* 异步操作成功 */){
            // 将Promise的状态改为fulfilled（已成功）
            resolve(resData);   // resData一般是异步操作的结果
        } else {
            // 将Promise的状态改为rejected（已失败）
            reject(resData);    // resData一般是一些错误信息
        }
    });
});
```
上面的例子，在`Promise`内部发起了一个异步请求，当请求完成，拿到返回值`resData`时，我们可以根据具体的业务需求修改`Promise`的对应状态。

#### 3、Promise保存异步操作的结果
细心的同学会发现，上面的例子中，我们在`resolve()`和`reject()`函数中传入了参数`resData`，这是在干什么？还记得么？`Promise`不仅能保存异步操作的状态，还能保存异步操作的结果。我们将将异步操作的结果`resData`传给这两个函数，就是将其保存到了`Promise`对象中。

#### 4、获取Promise中的状态和结果（.then()/.catch()）
那么，`Prmoise`对象中保存了异步操作的最终状态和结果，我们如何获取呢？换句话说，我们怎么知道异步操作的状态和结果分别是什么呢？

其实，每个`Promise`的对象实例都会有一个`.then()`和`.catch()`方法，这两个方法都接收一个函数作为参数，这个函数会被`Promise`传入一个参数，这个参数就是传入`resolve()`、`reject()`方法中的异步请求的结果（上个例子中的`resData`）。当`Promise`内部状态变为`fulfilled`时，就会进入`.then()`方法中，执行里面的回调函数。同理，当`Promise`内部状态变为`rejected`时，就会进入`.catch()`方法中，执行里面的回调函数。

```
/***接着上面例子***/
promise.then(function(resData) {
    // promise状态变为fulfilled，执行这里
    console.log(resData);
}).catch(function(resData) {
    // promise状态变为rejected，执行这里
    console.log(resData);
});
```

像上面这样，当执行进入`.then()`中，就说明`Promise`的状态是`fulfilled`。进入`.catch()`中，就说明状态是`rejected`，一般会在这里进行错误处理。同时，异步操作的结果会被传入定义在`.then()`、`.catch()`内部的函数中，我们可以直接访问使用。

#### 5、.then()/.catch()的链式调用

**在`.then()/.catch()`的返回值依旧是一个`Promise`实例。**也就是说，在`.then()/.catch()`中`return`任何值，都会被转化成一个`Promise`实例。所以`.then()`后面可以链式继续调用`.then()/.catch`，`.catch()`后面同样也可以。于是，就有可能出现下面这样的代码：
```
// 这样的代码是完全没有问题的。
promise.then(function(resData) {
    // 一些代码
}).then(function(resData) {
    // 一些代码
}).catch(function(error) {
    // 一些代码
}).then(function(resData) {
    // 一些代码
}).catch(function(error) {
    // 一些代码
});
```

**这里有一些需要注意的地方！！**
 1. 前一个`.then()`中`return`任何值（包括一个`Error`实例），都会进入后面最邻近的`.then()`。
 2. 前一个`.then()`中`throw`任何值或者内部代码报错，都会进入后面最邻近的`.catch()`。
 3. 同理，上述原理在`.catch()`中的情况与`.then()`中完全一致。

### 四、Promise的其他方法

#### 1、 Promise.resolve()
`Promise.resolve`方法接收一个任意值作为参数，可以将其转换为Promise对象。

该方法对参数的处理，可以分为以下四种不同的情况：
##### （1）参数是一个Promise实例
此时，`Promise.resolve`方法将不会做任何转换，直接原封不动的返回这个实例。

##### （2）参数是一个thenable对象
`thenable`对象是指对象内部实现了`then`方法的对象。此时，`Promise.resolve`方法会先将该对象转换为`Promise`对象，然后立即执行参数对象自己的`then`方法。

**最终，转换成的`Promise`对象的状态完全依赖于它内部`then`方法的具体实现，不一定是`fulfilled`状态，也有可能是`rejected`。**
```
// 定义一个thenable对象
let thenable = {
    then: function(resolve, reject) {
        resolve(42);
        // 如果换成执行下面一行代码，后面将会进入.catch()中
        // reject('error');
    }
};
let p1 = Promise.resolve(thenable);
p1.then(function(value) {
    console.log(value);       // 42
}).catch(function(value) {
    console.log(value);     // 'error'
});
```

##### （3）参数不是thenable对象，或者不是一个对象
如果参数不是`thenable`对象，或者不是一个对象，`Promise.resolve`方法返回一个新的`Promise`对象，**状态为`fulfilled`**，对象保存的值就是这个参数值。

```
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'));

let p = Promise.resolve('Hello');
p.then(function (s){
    console.log(s);   // 'Hello'
});
```

##### （4）不传任何参数
`Promise.resolve`方法允许调用时不带参数，直接返回一个`fulfilled`状态的`Promise`对象，对象保存的值为`undefined`。

```
let p = Promise.resolve();

p.then(function (value) {
  console.log(value);   // undefined
});
```

#### 2、Promise.reject()
`Promise.reject`方法也会返回一个新的`Promise`实例。**不论传入的参数是什么数据类型，有没有`thenable`方法，该实例的状态一定为为`rejected`，且返回的`Promise`对象中保存的值就是传入`Promise.reject`方法时原封不动的参数值**。
```
例子一
let p = Promise.reject('error');
// 等同于
let p = new Promise((resolve, reject) => reject('error'));

// 例子二
let thenable = {
    then(resolve, reject) {
        /**
         * 不论执行下面的哪一行，
         * 最后Promise对象的状态都是rejected，
         * 都会进入.catch中
         *
        reject('error');
        // resolve('fulfilled');
    }
};

Promise.reject(thenable).then(data => {
    // 不会进入这里！！
    console.log('进入then！');
}).catch(e => {
    console.log('进入catch！');
    // ！注意！这里的e的值是传入Promise.reject()方法的thenable对象
    console.log(e === thenable);  // true
})
```

#### 2、Promise.all()
`Promise.all`方法用于将多个`Promise`实例，包装成一个新的`Promise`实例。
```
let p = Promise.all([p1, p2, p3]);
```

`Promise.all`方法接收一个数组作为参数，数组元素`p1`/`p2`/`p3`都是`Promise`实例。如果不是，就会先调用`Promise.resolve()`方法，将参数转为`Promise`实例，再进一步处理。

最终，`p`的状态由`p1`/`p2`/`p3`共同决定，分成两种情况：
 1. 只有`p1`/`p2`/`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`。此时`p1`/`p2`/`p3`的返回值组成一个数组，传递给`p`的回调函数。
 2. 只要`p1`/`p2`/`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`。此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

#### 2、Promise.race()
`Promise.race`方法同样是将多个`Promise`实例，包装成一个新的`Promise`实例。
```
let p = Promise.race([p1, p2, p3]);
```

`Promise.race`方法接收的参数与`Promise.all`方法一样，如果不是 `Promise` 实例，就会先调用`Promise.resolve()`方法，将参数转为 `Promise` 实例，再进一步处理。

上面的例子中，只要`p1`/`p2`/`p3`之中任意一个实例率先改变状态，不论变为哪种状态，`p`的状态就跟着改变。那个率先改变状态的`Promise`实例的返回值，就传递给`p`的回调函数。