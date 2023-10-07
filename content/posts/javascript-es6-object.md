---
title: "JavaScript ES6中Object的新增方法"
date: 2023-10-06T20:39:41+08:00
keywords: ["JavaScript","es6"]
description: "在 ES6 中，添加了Object.is()、Object.assign()、Object.keys()、Object.values()、Object.entries()等方法。"
categories: ["技术文档"]
tags: ["JavaScript","es6"]
thumbnail: ""
banner: ""
---
在 ES6 中，添加了`Object.is()`、`Object.assign()`、`Object.keys()`、`Object.values()`、`Object.entries()`等方法。
## 1. Object.is()
`Object.is()`方法用来判断两个值是否为同一个值。

使用语法如下，其中，value1和value2是比较的两个值。
```js
Object.is(value1, value2);
```
`Object.is()`方法返回一个布尔类型的值，若满足以下条件则两个值相等：

- 都是undefined
- 都是null
- 都是true或都是false
- 都是字符串，且相同长度、相同字符、相同顺序
- 是相同对象（每个对象有同一个引用）
- 都是数字，且满足以下任意一个
  - 都是0
  - 都是+0
  - 都是-0
  - 都是NaN
  - 都非零，且非NaN，且是同一个值

例如，如下代码，先定义了两个空对象obj1和obj2，虽然他们都是空对象，但对象是引用数据类型，存储的是对象的地址，所以它们是不相等的。
```js
const obj1 = {};
const obj2 = {};
console.log(Object.is(obj1, obj2)); // false
```
但如果将同一个对象赋值给两个变量，那么两个变量中存储的都是同一个地址，所以他们是相等的。
```js
const obj1 = {};
const value1 = obj1;
const value2 = obj1;
console.log(Object.is(value1, value2)); // true
```
`Object.is()`方法与===不同的地方在于，===运算符将+0和-0视为相等，而将NaN与NaN视为不相等。
```js
console.log(+0 === -0); // true
console.log(NaN === NaN); // false
```
同样的两个值，在`Object.is()`方法中的结果：
```js
console.log(Object.is(+0, -0)); // false
console.log(Object.is(NaN, NaN)); // true
```
## 2. Object.assign()
`Object.assign()`方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象，并返回目标对象。

使用语法如下，target是目标对象，sources是源对象：
```js
Object.assign(target, ...sources)
```
如下代码，将源对象obj2合并到目标对象obj1上，打印结果显示完成了合并。

同时，通过判断res === obj1为true，也说明了`Object.assign()`改变了第一个参数的对象，即目标对象，也说明返回值是合并后的目标对象。
```js
const obj1 = { a: 1 };
const obj2 = { b: 2 };

const res = Object.assign(obj1, obj2);
console.log(res); // { a: 1, b: 2 }
console.log(res === obj1); // true
```
如下代码，如果目标对象和源对象中有相同的属性，那么源对象的属性将覆盖目标对象的属性：
```js
const obj1 = { a: 1, b: 2 };
const obj2 = { a: 2 };

const res = Object.assign(obj1, obj2);
console.log(res); // { a: 2, b: 2 }
```
## 3. Object.keys()
`Object.keys()`方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致 。

语法如下，obj是待枚举的对象：
```js
Object.keys(obj)
```
如下代码，遍历对象obj的属性，并返回一个数组：
```js
const obj = { a: 1, b: 2, c: 3, d: 4 };

const res = Object.keys(obj);
console.log(res); // [ 'a', 'b', 'c', 'd' ]
```
注意，`Object.keys()`只能遍历到可枚举属性，有如下代码，通过Object.defineProperty给对象obj添加了一个属性e，但没有添加配置项enumerable为true，那么这个新添加的属性e就是不可枚举的，使用`Object.keys()`方法是遍历不到e的：
```js
const obj = { a: 1, b: 2, c: 3, d: 4 };

Object.defineProperty(obj, 'e', {
    value: 5,
});

const res = Object.keys(obj);
console.log(res); // [ 'a', 'b', 'c', 'd' ]
```
有关Object.defineProperty的更多说明，请看「JavaScript中的Object.defineProperty()方法」。
## 4. Object.values()
`Object.values()`方法返回一个给定对象自身的所有可枚举属性值的数组，排列顺序和正常循环遍历该对象时返回的顺序一致 。

语法如下，obj是待枚举的对象：
```js
Object.values(obj)
```
如下代码所示，`Object.values()`也只能遍历可枚举属性的值：
```js
const obj = { a: 1, b: 2, c: 3, d: 4 };

Object.defineProperty(obj, 'e', {
    value: 5,
});

const res = Object.values(obj);
console.log(res); // [ 1, 2, 3, 4 ]
```
## 5. Object.entries()
`Object.entries()`方法相当于`Object.keys()`和`Object.values()`的结合，它返回一个给定对象自身可枚举属性的键值对数组。
语法如下，obj是待枚举的对象：
```js
Object.entries(obj)
```
如下代码所示，`Object.entries()`也只能遍历可枚举属性的键值对：
```js
const obj = { a: 1, b: 2, c: 3, d: 4 };

Object.defineProperty(obj, 'e', {
    value: 5,
});

const res = Object.entries(obj);
console.log(res); // [ [ 'a', 1 ], [ 'b', 2 ], [ 'c', 3 ], [ 'd', 4 ] ]
```