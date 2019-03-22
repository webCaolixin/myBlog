---
title: ES6-字符串的扩展
date: 2019-03-21 20:03:51
categories: 编程
tags: ES6
img: /medias/featureimages/ES6.jpg
---
### 一、模板字符串

> 模板字符串是增强版的字符串，用两个反引号（\` \`）包含来表示。

#### 1、当做普通字符串使用

在\` \`中如果只包含简单的字符串字面量，那么它和用引号（单/双引号）定义的字符串没有什么区别。

```
let s = `Hello World !`;
console.log(s);     // 'Hello World !'

// 注意！！如果在模板字符串中本身就要输出反引号 ` ,请使用反斜杠（\）转义。
let greeting = `\`Hello\` World!`;
console.log(greeting);      // '`Hello` World!'
```

#### 2、模板字符串可以保留字符串的格式

使用模板字符串来定义多行字符串，包括换行、缩进、多个空格等都会保留其中的格式。
最常见的就是用来定义一个HTML模板字符串，在编码时可以很好地保留HTML字符串的换行、缩进等...，也便于阅读维护。

```
let htmlStr = `
    <ul>
        <li>first</li>
        <li>second</li>
    </ul>
`;
console.log(htmlStr);

let s = `
    He   l  l o,
        W or    l d!
`
console.log(s);
```
以上两个例子，定义了带有格式（换行、缩进、多个空格）的模板字符串，模板字符串会保留所有的格式。在控制台输出的结果如下：
![模板字符串输出结果][1]

#### 3、模板字符串可以嵌入变量
在以前，如果我们需要用一些固定的字符串与一些动态的变量值来拼接形成一个最终字符串，最常用的做法是像下面这样：

```
let name = 'cao';
let age = 24;
let result = 'Hello ' + name + ',' + ' You are ' + age + ' years old !'；

// 拼接一个HTML字符串
let basket = {
    count: 10,
    onSale: 3
};
let resultHTML = 
    'There are <b>' + basket.count + '</b> ' +
    'items in your basket, ' +
    '<em>' + basket.onSale +
    '</em> are on sale!'
```

像上面这样拼接字符串显然很麻烦。例子中还是较为简单的字符串，如果字符串内容过于复杂，甚至像HTML文本字符串带有缩进和结构，上面这样显然很容易出错——因为不仅要保证引号配对无误，还要保证拼接符（+）没有遗漏，这样显然不已阅读和维护。然而，使用模板字符串会方便而且清晰得多。

上面的例子使用模板字符串也可以达到相同的效果，且方便、清晰。**模板字符串中嵌入的变量必须写在 `${ }` 的大括号中，大括号之外的字符都会被当做一般的字符串。**

```
let name = 'cao';
let age = 24;
let result = `Hello ${name}, You are ${age} years old !`；

// 拼接一个HTML字符串
let basket = {
    count: 10,
    onSale: 3
};
let resultHTML = 
    `There are <b> ${basket.count} </b>
    items in your basket,
    <em> ${basket.onSale}
    </em> are on sale!`;
```

`${ }`大括号内部可以放入任意的JS表达式，可以进行运算、引用对象属性或者调用函数。**如果表达式计算结果不是一个字符串，那就会自动将其转换为字符串**。比如，大括号中是一个对象，将默认调用对象的toString方法。

```
let x = 1;
let y = 2;
console.log(`${x} + ${y} = ${x + y}`);      // "1 + 2 = 3"
console.log(`${x} + ${y * 2} = ${x + y * 2}`);      // "1 + 4 = 5"

let obj = {x: 1, y: 2};
console.log(`${obj.x + obj.y}`);        // "3"

function fn() {
  return "Hello World";
}
console.log(`foo ${fn()} bar`);      // "foo Hello World bar"
```

### 二、标签模板

> 模板字符串可跟在函数后面，表示将模板字符串作为参数来调用该函数。这杯称作`“标签模板”`功能。

```
alert`Hello World !`;
// 相当于alert('Hello World !');
```

**标签模板其实不是模板，而是函数调用的一种特殊形式。** “ 标签 ” 指的就是函数，紧跟在后面的模板字符串就是它的参数。

使用标签模板调用函数大致分为两种情况：
 1. 模板字符串中只有一般字符串；
 2. 模板字符串中有嵌入的变量

#### 1、模板字符串中只有一般字符串
标签模板调用函数时，如果模板字符串中只有一般字符串，那么这个字符串就直接被当做参数传给调用的函数。

```
function sayHello(name) {
    return `Hello ${name} !`;
}

sayHello`world`;        // 'Hello world !'
// 等同于 sayHello（'world');
```

#### 2、模板字符串中有嵌入的变量
如果模板字符里面有嵌入的变量，就不是简单的调用了。JS会自动将模板字符串先处理转换成多个参数，再调用函数。具体转换方式如下：

```
let a = 5;
let b = 10;

function tag(strArr, val1, val2) {
    let s = `${strArr[0]}-${val1}-${strArr[1]}-${val2}-${strArr[2]}`;
    console.log(s);
}

// 标签模板调用
tag`Hello ${ a + b } world ${ a * b }`;

// 等同于下面
// ！！注意：字符串中的空格是会被保留下来的
tag(['Hello ', ' world ', ''], 15, 50);

// 以上两个调用输出结果都是：'Hello -15- world -50-'
```
也就是说，用标签模板调用函数的时候，如果模板字符里面嵌有变量，JS会将嵌入变量${...}以外的字符串（包括空格）按顺序组合成一个字符串数组，作为函数的第一个参数；再将嵌入的变量值依次作为第二个参数、第三个参数......。


[1]: /medias/article/coding/template-string/console-templateStr.png