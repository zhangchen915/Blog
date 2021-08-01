---
title: "表驱动法（Table-Driven Approach）"
categories: ["前端","读书笔记"]
tags: ["编程模式"]
draft: false
slug: "772"
date: "2020-02-01 23:55:00"
---

<img src="https://img.zhangchen915.com/2020/02/4045386708.png" alt="1_l7Hjzbw8oHpa39gF8GkW9Q.png" />

> 表示原则：把知识叠入数据以求逻辑质朴而健壮。 ——《UNIX编程艺术》

**表驱动法是一种编程模式——从表里查找信息而不是使用逻辑语句。**
随着逻辑复杂性的增加，`if/else` 或switch中的代码将变得越来越肿，所以我们常说数据比程序逻辑更易驾驭。表驱动法就是将这些逻辑中的数据与逻辑分开，从而减少逻辑的复杂度。查表方式通常有如下几种：

### 直接访问

以一个月的天数为例，我们要写一串`if/else` 或者`switch/case` 来表达逻辑。
```js
  if(1 == iMonth) {iDays = 31;}
  else if(2 == iMonth) {iDays = 28;}
  else if(3 == iMonth) {iDays = 31;}
  else if(4 == iMonth) {iDays = 30;}
  else if(5 == iMonth) {iDays = 31;}
  else if(6 == iMonth) {iDays = 30;}
  else if(7 == iMonth) {iDays = 31;}
  else if(8 == iMonth) {iDays = 31;}
  else if(9 == iMonth) {iDays = 30;}
  else if(10 == iMonth) {iDays = 31;}
  else if(11 == iMonth) {iDays = 30;}
  else if(12 == iMonth) {iDays = 31;}

```

但是我们把数据存到一张表里，就不需要冗余的逻辑了。
```js
const month = {
  monthTable: [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
  get days(month, year) {
    // (year % 4 === 0) && (year % 100 !== 0 || year % 400 === 0) 闰年逻辑 
    return [month - 1];
  }
}
```


### 索引访问
有时通过一次键值转换（对象或者数组），依然无法找到键值。此时可将转换的对应关系写到一个索引表里，即索引访问。而且可以多重索引，或者使用正则匹配。

我们可以利用`Map`结构的key可以为基本类型这一特点：
```js
const actions = new Map([
  [{identity:'guest',status:1},()=>{/*do sth*/}],
  [{identity:'guest',status:2},()=>{/*do sth*/}],
  //...
])

const onButtonClick = (identity,status)=>{
  let action = [...actions].filter(([key,value])=>(key.identity == identity && key.status == status))
  action.forEach(([key,value])=>value.call(this))
}
```

进一步的我们也可以利用正则：
```js
const actions = ()=>{
  const functionA = ()=>{/*do sth*/}
  const functionB = ()=>{/*do sth*/}
  const functionC = ()=>{/*send log*/}
  return new Map([
    [/^guest_[1-4]$/,functionA],
    [/^guest_5$/,functionB],
    [/^guest_.*$/,functionC],
    //...
  ])
}

const onButtonClick = (identity,status)=>{
  let action = [...actions()].filter(([key,value])=>(key.test(`${identity}_${status}`)))
  action.forEach(([key,value])=>value.call(this))
}
```

特别的，对于布尔类型我们还可以使用二维数组或多维数组。
```js
const actions = [
    //!a a
    [1, 2],  // !b
    [3, 4]   //  b 
]

```

### 阶梯访问
对于一些无规则的数据，例如等级划分。我们没法使用简单的转换将数据转换为索引，但是我们可以使用一个循环，依次检查区间的上下限。

需要注意的细节
- 谨慎处理区间端点
- 可以采用二分/索引加速


```js
const grade = [59,79,84,89,94,100]; 
const level = ["F","E","D","C","B","A"];


const getLevel = g =>{
    for(let i = 0 ; i < grade.length ; i++){
        if(g <= grade[i]) return level[i];
    }
}
```


### 表驱动的优势
- 可读性更强，逻辑一目了然
- 数据与逻辑解耦，修改数据即可
- 逻辑可重用

###参考文章：
https://www.cnblogs.com/clover-toeic/p/3730362.html
https://juejin.im/post/5dbff51bf265da4d4e3001b2#heading-2
https://medium.com/javascript-in-plain-english/clean-up-your-code-by-removing-if-else-statements-31102fe3b083
