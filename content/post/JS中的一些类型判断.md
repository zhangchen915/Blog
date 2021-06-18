---
title: "JS中的一些类型判断"
categories: ["默认分类"]
tags: ["javascript"]
draft: false
slug: "JS中的一些类型判断"
date: "2016-10-03 20:48:00"
---

一、判断Array
---------

一般来说我们判断类型会使用 `typeof` 操作符来判断就足够了，但是如果你想判断用typeof判断数组，他只会返回一个'object '。你要是问问什么，那就要好好温习一下基础知识了，最新的JS定义了七种数据类型：

**六种 原型 数据类型:**

* Boolean.  布尔值，true 和 false.
* null. 一个表明 null 值的特殊关键字。 JavaScript 是大小写敏感的，因此 null 与 Null、NULL或其他变量完全不同。
* undefined.  变量未定义时的属性。
* Number.  表示数字，例如： 42 或者 3.14159。
* String.  表示字符串，例如："Howdy"
* Symbol ( 在 ECMAScript 6 中新添加的类型).。一种数据类型，它的实例是唯一且不可改变的。
* Object

Array并不属于JS基本类型，所以对数组的判断ES5给出了一个单独的`isArray()`方法。
另外还可以使用`Object.prototype.toString()`来判断，下面给出了对应表格。

    Value               toString   typeof
    "foo"               String     string
    new String("foo")   String     object
    1.2                 Number     number
    new Number(1.2)     Number     object
    true                Boolean    boolean
    new Boolean(true)   Boolean    object
    new Date()          Date       object
    new Error()         Error      object
    [1,2,3]             Array      object
    new Array(1, 2, 3)  Array      object
    new Function("")    Function   function
    /abc/g              RegExp     object
    new RegExp("meow")  RegExp     object
    {}                  Object     object
    new Object()        Object     object 

关于这种方法的原理不再过多叙述，可以参考这篇文章[JavaScript:Object.prototype.toString方法的原理][1]

二、判断空对象
-------

    function isEmptyObject(e) {  
        var t;  
        for (t in e)  
            return !1;  
        return !0  
    }  
这是jQuery的源码，原理就是使用 for in 来遍历对象中的属性名.如果一个对象有属性名则return false，否则return true;

三、判断 Null
---------

你也许会想到使用 typeof ，但是他只会返回一个 object

> 在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是0。由于
> null 代表的是空指针(大多数平台下值为0x00)，因此，null的类型标签也成为了0，typeof
> null就错误的返回了"object"

其实判断Null是最简单的，只需要用`===`即可

**参考文章**
[1.typeof][2]


  [1]: http://www.cnblogs.com/ziyunfei/archive/2012/11/05/2754156.htmlhttp://www.cnblogs.com/ziyunfei/archive/2012/11/05/2754156.html
  [2]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof
