---
title: "Js localeCompare() 方法实现字符串数组排序"
date: 2023-07-31T19:29:27+08:00
keywords: ["localeCompare","js"]
description: "localeCompare()方法返回一个数字来指示一个参考字符串是否在排序顺序前面或之后或与给定字符串相同。"
categories: ["技术应用"]
tags: ["localeCompare","js"]
thumbnail: ""
banner: ""
---
`localeCompare()`方法返回一个数字来指示一个参考字符串是否在排序顺序前面或之后或与给定字符串相同。
```bash
语法：str.localeCompare(compareStr)
```
- 当 引用字符串 在 比较字符串 前面时返回 -1
- 当 引用字符串 在 比较字符串 后面时返回 1
- 相同位置时返回 0
```js
'a'.localeCompare('b')  // 返回-1
'b'.localeCompare('a')  // 返回 1
'a'.localeCompare('a')  // 返回 0
```
## 应用
可以结合sort()对字符串数组进行排序

排序前：['pear','apple','peach','banana']

要实现的排序效果：['apple','banana','peach','pear']
```js
var fruits = ['pear','apple','peach','banana']
var afterFruits = fruits.sort((a,b) => a.localeCompare(b))
console.log(afterFruits) // ["apple","banana","peach","pear"]
```
中文同样可以通过该方法去实现：
```js
var names = ['方回','陈一','李四','陈波','张三','陈啊']
var afterNames = names.sort((a,b) => a.localeCompare(b))
console.log(afterNames) // ["陈啊", "陈波", "陈一", "方回", "李四", "张三"]
```
对象数组实现:
```js
var names = [{"name":"bb","sex":"男"},{"name":"aa","sex":"男"},{"name":"cc","sex":"女"},{"name":"bb","sex":"女"}]
var afterNames = names.sort((a,b) => (a.name+a.sex).localeCompare(b.name+b.sex))
console.log(afterNames) // [{"name":"aa","sex":"男"},{"name":"bb","sex":"男"},{"name":"bb","sex":"女"},{"name":"cc","sex":"女"}]
```