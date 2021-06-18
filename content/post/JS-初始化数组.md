---
title: "JS 初始化数组"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "548"
date: "2018-05-12 15:40:00"
---

### 直接使用Array

- 单个**正整数**参数，表示返回的新数组的长度

```js
  Array(3)      // [undefined × 3]
```
  这里需要注意的是只能接受正整数，而且返回的是数组长度，`[undefined × 3]`与 
  `[undefined, undefined, undefined]`是不同的，前者只有`length`属性，严格来说并不能称其为数组而是**类数组对象**。所以它也不能用`map`或`forEach`方法来遍历。

- 多参数时，所有参数都是返回的新数组的成员
```js
  Array(3,1)    // [3, 1] 
```

### 使用ES6中的Array.from方法
`Array.from()`方法从一个类数组或可迭代对象中创建一个新的数组实例。所以我们可以把直接使用Array得到的类数组作为参数，来得到一个真正的数组。ES5中可以使用`Array.apply`代替。

```js
Array.from(Array(3));  // [undefined, undefined, undefined]
Array.apply(null, Array(3));  //ES5
```

### 使用ES6中的Array.fill方法填充数据
```js
Array(3).fill(1);
```
这里需要注意`fill`方法的第一个参数是值，如果你用`fill(new Foo)`，那么`Foo`对象只会构造一次，如果你的类中有随机数的话就不能用`fill`，而是用`map`方法。

```js
Array.from(Array(100)).map(() => new Foo())
```

###创建每个元素的值等于它的下标的数组
利用keys方法返回的可迭代对象。
```js
Array.from(Array(3).keys())
[...Array(3).keys()]
```



