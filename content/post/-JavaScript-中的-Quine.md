---
title: " JavaScript 中的 Quine"
categories: ["默认分类","代码之外"]
tags: []
draft: false
slug: "769"
date: "2020-01-25 14:29:00"
---

**Quine：一个能够打印自己源代码的程序**

你可以试试执行以下
```js
!function $(){console.log('!'+$+'()')}()
```

为什么起作用？上面的代码使用了一些技巧。

获取源代码最简单的方式是使用String

```js
function foo() { return "abc" }
String(foo)

// function foo() { return "abc" }
```

所以我们第一个版本 可以是这样

`function $() { console.log(String($)) }` ,但是我们需要再执行 `$()` 才可以。
所以我我们使用IIFE继续进行改造。

```js
!function $() { console.log(String($)) }()

//function $() { console.log(String($)) }
//true （!undefined的结果为true）
```
最后加上感叹号即可。

看看下面这个，意思也一样`((((function $(){console.log('(((('+$+'()))))')}()))))`

更多有意思的例子：

- `(function _(){return'('+_+')()'})()`
- ```f=_=>`f=${f};f()`;f()```


### **参考文章：**
http://rosettacode.org/wiki/Quine#JavaScript
https://2ality.com/2012/09/javascript-quine.html

