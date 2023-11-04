---
title: "typescript !感叹号用法"
date: 2023-11-04T18:22:02+08:00
keywords: ["typescript"]
description: ""
categories: ["技术文档"]
tags: ["typescript"]
thumbnail: ""
banner: ""
---
## 1. 非空断言操作符
非空断言操作符!可以用来告诉编译器一个值一定不为null或undefined。这可以帮助你避免一些空值错误，但同时也要谨慎使用，因为如果你使用了一个实际上为null或undefined的值，那么会抛出运行时异常。

例如，如果你有一个字符串变量，但是你确定它不为null或undefined，你可以在变量后面加上!，如下所示：
```ts
const myString: string | null = "hello world";
const myNonNullString: string = myString!;
```
在这个例子中，我们在myString变量后面加上了!，表示我们确信这个变量不为null或undefined。然后，我们将这个非空断言的值赋值给了一个新的字符串变量myNonNullString。

## 2. 可选属性的强制转换
当你有一个对象，其中某些属性可能是可选的，但你想强制转换它们为必需属性时，你可以使用感叹号!。

例如，假设你有一个User接口，其中有一个address属性是可选的：
```ts
interface User {
  name: string;
  age: number;
  address?: {
    street: string;
    city: string;
    country: string;
  };
}
```
如果你想使用address属性中的street属性，但是你确信address属性已经存在并且不为null或undefined，你可以使用感叹号!来强制转换它：
```ts
function getStreet(user: User) {
  return user.address!.street;
}
```
在这个例子中，我们在user.address后面加上了!，表示我们确信user.address不为null或undefined。然后，我们返回了user.address.street，这里不需要再加上!，因为我们已经通过非空断言操作符将user.address强制转换为必需属性。

## 3. 非空类型断言
TypeScript中还有一种类型断言，叫做非空类型断言，使用方式为在变量名后面加上!。这个操作符告诉编译器，这个变量不可能是null或undefined。这个操作符只能用于类型断言，而不能用于强制转换可选属性为必需属性。

例如，假设你有一个函数，它接受一个可能为null或undefined的数字，但是你确定这个数字不为null或undefined，你可以使用非空类型断言：
```ts
function double(num: number | null | undefined) {
  return num! * 2;
}
```
在这个例子中，我们使用非空类型断言告诉编译器，num不可能是null或undefined，这样就可以直接将num乘以2了。

## 4. 类型守卫
感叹号!还可以在类型守卫中使用。类型守卫是一种在条件语句中使用类型检查的技巧，可以帮助你在代码中判断变量的类型，从而避免一些类型错误。

例如，假设你有一个函数，它接受一个字符串或undefined类型的参数，但是你需要确保这个参数一定是字符串类型，你可以使用类型守卫：
```ts
function print(str?: string) {
  if (str != null) {
    console.log(str.toUpperCase());
  }
}
```
在这个例子中，我们首先使用!= null判断参数str不是null或undefined，然后使用类型守卫str，这样编译器就知道str一定是字符串类型，可以使用字符串方法toUpperCase()。

总之，感叹号!是TypeScript中的一个重要操作符，除了前面提到的两种常见用法外，还可以用于非空类型断言和类型守卫等场景。在使用时要注意谨慎，避免出现运行时异常。