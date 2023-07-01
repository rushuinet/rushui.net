---
title: "JavaScript 删除数组中指定元素(5种方法)"
date: 2023-06-28T19:44:37+08:00
keywords: ["JavaScript"]
description: "在 JavaScript 中，数组是一种常见的数据类型，可以存储多个元素。有时候，我们需要从数组中删除某些特定的元素。本文将介绍如何使用 JavaScript 删除数组中指定的元素。"
categories: ["技术应用"]
tags: ["JavaScript"]
thumbnail: ""
banner: ""
---

## JavaScript 删除数组中指定元素
在 JavaScript 中，数组是一种常见的数据类型，可以存储多个元素。有时候，我们需要从数组中删除某些特定的元素。本文将介绍如何使用 JavaScript 删除数组中指定的元素。

## 1.使用splice()方法删除元素
JavaScript 中的 splice() 方法可用于在数组中添加或删除元素。如果我们需要删除数组中的元素，可以使用 splice() 方法。该方法接受两个参数，第一个参数指定要删除的元素的位置，第二个参数指定要删除的元素个数。例如，我们可以使用以下代码删除数组中的第二个元素：
```bash
let myArray = ["apple", "banana", "orange", "grape"];
myArray.splice(1, 1);
console.log(myArray); // ["apple", "orange", "grape"]
```
输出结果为：["apple", "orange", "grape"]

## 2.使用filter()方法删除元素
除了使用 splice() 方法，我们还可以使用 filter() 方法来删除数组中的元素。该方法可用于创建一个新的数组，其中包含符合特定条件的元素。我们可以使用以下代码删除数组中的所有 “banana” 元素：
```bash
let myArray = ["apple", "banana", "orange", "grape"];
myArray = myArray.filter(function(item) {
  return item !== "banana"
});
console.log(myArray); // ["apple", "orange", "grape"]
```
输出结果为：["apple", "orange", "grape"]

## 3.使用pop()和shift()方法删除元素
pop() 和 shift() 方法可用于删除数组的最后一个元素和第一个元素。如果我们想删除数组中的特定元素，可以使用这些方法与 indexOf() 方法结合使用。例如，以下代码可以删除数组中的第二个元素：
```bash
let myArray = ["apple", "banana", "orange", "grape"];
let index = myArray.indexOf("banana");
if (index !== -1) {
  myArray.splice(index, 1);
}
console.log(myArray); // ["apple", "orange", "grape"]
```
输出结果为：["apple", "orange", "grape"]

## 4.使用slice()方法删除元素
slice() 方法是一个纯函数，它不会改变原始数组，而是返回一个新的数组，该数组包含从开始到结束（不包含结束）的元素。我们可以使用以下代码删除数组中的第二个元素：
```bash
let myArray = ["apple", "banana", "orange", "grape"];
let newArray = myArray.slice(0, 1).concat(myArray.slice(2));
console.log(newArray); //["apple", "orange", "grape"]
```
输出结果为：["apple", "orange", "grape"]

## 5.使用ES6的filter()方法删除元素
ES6 中的 filter() 方法也可以用于删除数组中的元素。我们可以使用以下代码删除数组中的所有 “banana” 元素：
```bash
let myArray = ["apple", "banana", "orange", "grape"];
myArray = myArray.filter(item => item !== "banana");
console.log(myArray); // ["apple", "orange", "grape"]
```
输出结果为：["apple", "orange", "grape"]

## 总结
以上是 JavaScript 删除数组中指定元素的多种方法。我们可以根据需求选择适合自己的方法来删除数组中的元素。希望这篇文章能够帮助你更好地掌握 JavaScript 中的数组操作。