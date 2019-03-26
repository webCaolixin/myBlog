---
title: ES6 - 解构赋值
date: 2019-03-18 00:41:12
categories: 编程
tags: ES6
img: /medias/article/featureimages/ES6.webp
---
### 解构赋值

> ES6允许按照**一定的模式**，从数组和对象中提取值，然后对变量赋值，这被称为`解构赋值`。

这听起来比较抽象。我们来用一个简单的例子，直观明了的解释什么是解构赋值。

在解构赋值之前，我们如果需要一次性声明并赋值多个变量，需要这样操作：

```
let a = 123;
let b = 'bbb';
let c = true;
```

这样，如果要声明初始化的变量很多（假设有十几二十个），这样写显得很麻烦，不够优雅。ES6中的解构赋值，就简化了这一操作。

上面的例子用解构赋值，可以像下面这样操作，同样可以达到相同的效果。
```
// 为了让大家先直观了解什么是解构赋值，我们先用数组的结构赋值来演示讲解。
let [a, b, c] = [123, 'bbb', true]
```
这样一次性声明赋值多个变量就方便了很多。其实，这种写法属于`“模式匹配”`，只要等号两边的模式相同，就能将左边模式对应的值赋值给最右边对应模式处的变量。

所以，本质上**解构赋值就是采用`模式匹配`的方法，从数组/对象中提取对应位置或者对应名称属性的值，对变量进行赋值**。

#### 一、数组的解构赋值

##### 1、常用的数组解构赋值
我们先来看看数组解构赋值都有哪些方式。

```
// 最基本的数组解构赋值
let [a, b, c] = [1, '2', false];
console.log(a, b, c);       // 1 '2' false

// 使用嵌套数组进行解构赋值
let [d, [e, f], g] = [1, [2, '3'], [4]];
console.log(d, e, f, g);    // 1 2 '3' [4]

// 左边（声明变量）的模式中，可以有缺省
let [ , h, , i] = ['one', [1, 2, 3], null, {hProperty: 'HHH'}];
console.log(h, i);          // [1, 2, 3] {hProperty: 'HHH'}

// 数组解构赋值可以使用rest参数
let [first, ...rest] = [1, 2, 3, 4, 5];
console.log(first, rest);   // 1 [2, 3, 4, 5]

// !!!这个例子需要特别注意!!!
let [x, y, ...z] = ['a'];
console.log(x, y, z);       // 'a' undefined []
/**
 * 1、使用数组解构赋值，如果rest参数（上面的例子为变量z）没有对应赋值匹配
 *    那么该变量会被赋值为空数组。
 * 2、如果有的变量模式匹配失败，上面的例子中变量y没有匹配到赋值，
 *    则会被赋值为 undefined
 */
```

##### 2、等号左侧（变量）模式匹配不成功
如果左侧模式匹配不成功，则变量会被赋值为`undefined`。

```
let [a] = [];       // a匹配失败
console.log(a);     // undefined

let [a, b] = ['AAA'];   // b匹配失败
console.log(a, b);      // 'AAA' undefined
```

##### 3、等号右侧（赋值）模式匹配不成功
如果等号左边（变量）的模式只匹配等号右边（赋值）的模式中的一部分，称为`不完全结构`。这样也是可以成功解构赋值的。

```
// 等号右边的值'string'并未匹配到左侧的变量
let [a, b] = [1, {bProperty: 'BBB'}, 'string'];
console.log(a, b);      // 1 {bProperty: 'BBB'}

// 等号右侧的值3，并未匹配到左侧的变量
let [c, [d], e] = [1, [2, 3], 4];
console.log(c, d, e);      // 1 2 4
```

以上的例子都是不完全结构，依旧可以正常运行，完成解构赋值。

##### 4、等号右侧不具备 Iterator 接口
如果等号的右边不是数组（或者严格地说，不具备 Iterator 接口），那么将会报错。下面的解构赋值都会报错！！

```
// 作为简单类型的值，解构赋值时，会将其转换为对象（基本包装类型）
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;

// 本身就不具备 Iterator 接口
let [foo] = {};
```

在解构赋值时，简单类型的值会被临时转换为对象（基本包装类型），它不具备 Iterator 接口，`{ }`本身也不具备 Iterator 接口，所以上面的解构赋值都会报错。


##### 5、数组解构赋值设置默认值

数组结构赋值时，允许为任何一个变量设置默认值。并且，**当且仅当**等号右侧的赋值严格等于（`===`，三个等号的严格等于）`undefined`时，默认值才会生效。

```
/**
 * 下面这个例子，首先是变量a匹配右侧失败，被设置为默认的 undefined
 * 因为a为 undefined，所以默认值生效
 */
let [a = 123] = [];
console.log(a);     // 123

let [b, c = 'CCC'] = [[1, 2]];
console,log(b, c);  // [1, 2] CCC

let [d = false] = [true];
console.log(d);     // true

let [e = {}, f = 100, g = {gProperty: 'GGG'}] = [false, 0, undefined];
console.log(e, f, g);       // false 0 {gProperty: 'GGG'}
```

还有一个细节需要明确！**默认值取值是`惰性求值`的**。也就是说，当一个默认值是一个JS表达式的时候，如果默认值不需要生效，那么这个表达式是不会运行计算求值的。

```
// 因为 null 并不严格等于 undefined，所以 b 的默认值不会生效
// 因此，a + 1是不会执行的。
let [a, b = a + 1] = [1, null]
console.log(a, b);      // 1 null

// 这个例子同上
function calDefault() {
    return 123
}
let [c, d = calDefault()] = [1, 2];
console.log(c, d);      // 1 2
```

#### 二、对象的解构赋值

对象的解构赋值和数组的类似，区别在于数组的解构赋值是有顺序的，是按照对应位置顺序来解构并赋值的。但是，对象的解构赋值不受顺序影响，是按照变量名和属性名相同的对应关系来解构并赋值的。

##### 1、常用的对象解构赋值
```
// 最简单的对象解构赋值
let {name, age} = {name: 'cao', age: 24};
console.log(name, age);     // 'world' 24

// 嵌套对象解构赋值
let person = {
    base: {
        name: 'cao',
        age: 24
    },
    address: "Xi'An"
}
let {base: {name, age}, address} = person;
console.log(name, age, address);        // 'cao' 24 "Xi'An"
```

##### 2、嵌套对象解构赋值的特别说明！！（重要，一定要理解！）
```
// 多层次嵌套
let node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};
let { loc, loc: { start }, loc: { start: { line }} } = node;

console.log(loc,);      // {start: {line: 1, column: 5}}
console.log(start);     // {line: 1, column: 5}
console.log(line);      // 1
```
**！！注意！！**这里要特别说明，对`line`进行解构赋值的时候，只有`line`是变量，`loc`和`start`都是模式，不是变量。

##### 3、等号左侧模式匹配不成功
和数组解构赋值类似，对象解构赋值中，如果等号左侧的变量名未匹配到右侧对象中相同的属性名，那么该变量默认赋值为`undefined`。

```
// 左侧 b 匹配失败
let {a, b} = {a: [1, 2], c: 'CCC'};
console.log(b);     // undefined

// 左侧c、d都匹配失败
let {c, d} = {};
console.log(c, d);      // undefined undefined
```

##### 4、等号右侧模式匹配不成功
等号右侧模式匹配不成功依旧可以成功解构赋值。

```
let val = {
    a: 'AAA',
    b: {
        c: 'CCC'
    },
    d: [1, 2, 3]
};
// 右侧属性 d 未匹配到左侧对应变量，不完全结构，不会报错
let {a, b: {c}} = val;  // 这里b是模式，不是变量。
console.log(a, c);      // 'AAA' 'CCC'
```

##### 5、变量名与属性名不一致时
从上面对象解构赋值可以看出，要声明的变量名和属性名相同时才能成功解构并赋值。但如果要声明的变量名和对象名不一致呢？或者说我们需要将和属性名相同而解构赋值得到的同名变量定义成别的名字呢？这种情况，**只能（只能）**像下面这样。

```
// 这相当于把右侧的属性n的值解构到左侧，并重新起名为变量name
let {n: name, a: age} = {n: 'cao', a: 24};
console.log(name, age);     // 'cao' 24
```

上面的例子，其实我们最终定义的是`name`、`age`两个变量。相当于把右侧对象的属性`n`的值解构到左侧变量`n`（实际不存在变量`n`），并重新起名为变量`name`。变量`age`同理。

##### 6、对象解构赋值设置默认值
和数组解构赋值类似，对象的解构赋值也可以指定默认值。默认值生效的条件是，**当且仅当**对象的属性值**严格等于**`undefined`。

```
let {a = 3} = {};
console.log(a);     // 3

let {b, c = 5} = {b: 1};
console.log(b, c);     // 1 5

let {c: d = 3} = {};
console.log(d);     // 3

let {c: e = 3} = {c: 5};
console.log(e);     // 5

let {message: msg = 'msg Content'} = {};
console.log(msg);     // 'msg Content'
```

#### 三、基本数据类型的解构赋值

##### 1、字符串解构赋值
字符串也可以解构赋值。因为解构时，字符串会被转换成了一个类似数组的对象。

```
const [a, b, c, d, e] = 'hello';
console.log(a, b, c, e);        // 'h' 'e' 'l' 'l' 'o' 

let {length} = 'hello';
console.log(length);        // 5
```

##### 2、数值、布尔值的解构赋值
解构赋值时，如果等号右边是数值和布尔值，则会先将其转换为对象（基本包装类型）。

```
let {toString: s1} = 123;
console.log(s1 === Number.prototype.toString);   // true

let {toString: s2} = true;
console.log(s2 === Boolean.prototype.toString); // true
```

##### 3、undefined 和 null 的解构赋值（报错！！）
**解构赋值的规则是，只要等号右边的值不是数组或者对象，就先将其转为对象**。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

```
let { x } = undefined;  // TypeError
let { y } = null;       // TypeError
```