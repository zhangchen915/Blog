---
title: "关于js的回调函数"
categories: ["默认分类"]
tags: []
draft: false
slug: "46"
date: "2015-12-12 20:36:00"
---

**先看看啥叫回调函数：**
Wiki上是这样定义的：
> 回调函数是指通过函数参数传递到其它代码的，某一块可执行代码的引用。

说白了就是把一个函数当作参数传给另一函数。

    var friends = ["Mike", "Stacy", "Andy", "Rick"];
    friends.forEach(function (eachName, index){
    console.log(index + 1 + ". " + eachName);
    }); // 1. Mike, 2. Stacy, 3. Andy, 4. Rick

在上面的代码中我们传递了一个匿名函数给forEach方法，作为forEach的参数，这个匿名函数就是回调函数。

**回调函数是闭包**
当作为参数传递一个回调函数给另一个函数时，回调函数将在包含函数函数体内的某个位置被执行，就像回调函数在包含函数的函数体内定义一样。这不就是闭包吗，所以回调函数可以访问包含函数作用域。
当然在闭包中使用this的问题在回调函数中也存在，具体就觉办法在之前关于this的文章中。

这就是回调函数，很简单，应用也很广泛，jQuery中的回调函数比比皆是，使用好回调能很好抽象我们的代码，还能节省不少工作。

