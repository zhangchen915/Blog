---
title: "JS 元编程（Metaprogramming）"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "493"
date: "2017-11-24 19:45:00"
---

程序可以访问、检测和修改它本身状态或行为的一种能力。我们也把这种能力称为“自省”。
这个名词感觉挺陌生，离我们很远，其实你之前已经不知不觉接触过元编程。最简单 eval，这个在ES1中就已经存在了，它被称为代码生成（Code Generation）

ES6 带来了三个全新的 API：Symbol、Reflect、以及 Proxy。
符号（symbol）是指包含在实现中的反射 - 通过向已有的类和对象中添加符号来改变其行为。
反射（refect）是指通过内省来实现反射 - 用于从代码中发现更低层次的信息。
代理（proxy）是通过拦截实现反射 - 包装对象，然后通过设置陷阱来拦截它们的行为。

反射这种特性，无外乎是出于为了提高生产力、提高编程时的灵活性等考量。

自省的一个简单例子是获取函数的名字
```
var a= function() {};
var b= function def() {}

a.name; // "a"    
b.name; // "def"
```
ES6中新加入了symbol proxy refect，都是实现元编程的一种方式。


###Symbols
Javascript 预定义了些内置的symbols，比如@@iterator。

这些内置的symbols主要是为了暴露一些特殊的元属性来让你对 Javascript 的行为有更多的控制权。

###Proxies

Proxy是为其他的一些普通的对象最一层拦截，你能够其上注册一些特殊的回调，外界对该对象的访问，都会通过这层拦截，并触发回调。因此提供了一种机制，可以对外界的访问进行过滤和改写，为目标对象添加自己额外的逻辑。

#### 代理的前置与后置

这些代理处理程序的元编程的好处应该是显而易见的。几乎完全拦截我们可以（和覆盖）对象的行为，这意味着我们可以在一些非常强大的方式超出核心 Javascript 对象行为。


###Reflect

Reflect没有构造函数。你不能将其与一个new运算符一起使用，或者将Reflect对象作为一个函数来调用。Reflect的所有属性和方法都是静态的（就像Math对象）。

你在控制台打印一下你会发现Reflect与大部分Object的方法都一样，那ES6添加Reflect对象的目的是什么?

- 将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上。
   ```
   Function.prototype.apply.call(Math.floor, undefined, [1.75]) // 1
   Reflect.apply(Math.floor, undefined, [1.75]) // 1
   ```

- Object对象上的方法在遇到错误是会抛出异常，而Reflect上的方法会返回false


