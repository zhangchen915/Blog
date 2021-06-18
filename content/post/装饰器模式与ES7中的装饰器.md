---
title: "装饰器模式与ES7中的装饰器"
categories: ["默认分类"]
tags: []
draft: false
slug: "254"
date: "2017-02-19 15:59:00"
---

装饰器能动态地给一个对象添加一些额外的职责，换句话说就像Chrome的插件，能够扩展浏览器的功能，想加什么功能下载一个插件来“装饰”浏览器就可以了。
所以装饰器的目的就是功能的复用，因为在传统的面向对象语言中，为对象添加功能通常会用继承的方式，但继承真的就那么完美吗，其实不然。当超类改变，子类也随之改变，耦合了；超类内部细节对子类可见，破坏封装了；功能复用，子类=对象*功能.....

#使用AOP装饰函数

```js
//前置装饰
Function.prototype.before = function (beforefn) {
    var __self = this; // 保存原函数的引用
    // 返回包含了原函数和新函数的"代理"函数
    return function () {
        // 执行新函数，且保证this 不被劫持，新函数接受的参数
        // 也会被原封不动地传入原函数，新函数在原函数之前执行
        beforefn.apply(this, arguments);
      
        // 执行原函数并返回原函数的执行结果，并且保证this 不被劫持
        return __self.apply(this, arguments);
    }
};

//A = A.before( B );
```

当然装饰器也不是完美的，多层装饰比较复杂，也可能会导致性能问题。

可以这么理解：继承是自上而下，装饰器是由内而外包裹的关系。

#ES7中的装饰器
装饰器由一个`@`紧接一个函数名称，ES7 中的 decorator 其实就是一种语法糖，它依赖于 ES5 的 Object.defineProperty 方法 。

##Object.defineProperty
Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个已经存在的属性， 并返回这个对象。
```js
Object.defineProperty(obj, "key", {
  enumerable: false,
  configurable: false,
  writable: false,
  value: "static"    //属性对应的值
});
\\Object.defineProperty(obj, prop, descriptor)
\\obj: 目标对象
\\prop: 属性名
\\descriptor: 针对该属性的描述符
```
##实现一个 decorator

```js
function readonly(target, key, descriptor) {
  descriptor.writable = false
  return descriptor
}

class Dog {
  @readonly
  bark () {
    return 'wang!wang!'
  }
}

let dog = new Dog()
dog.bark = 'bark!bark!'
// Cannot assign to read only property 'bark' of [object Object]
```

编译之后的代码：

```js
function Dog () {}

Object.defineProperty(Dog.prototype, 'bark', {
  value: function () { return 'wang!wang!' },
  enumerable: false,
  configurable: true,
  writable: true
})
```

ES7 的 decorator，作用就是返回一个新的 descriptor，并把这个新返回的 descriptor 应用到目标方法上。

更多的装饰器，可以参考[core-decorator][1]，它提供了一些常用的 decorator。

**参考阅读：**
1.[Object.defineProperty()][2]
2.[Decorators in ES7][3]


  [1]: https://github.com/jayphelps/core-decorators.js
  [2]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty
  [3]: http://hackll.com/2015/07/24/decorators-in-es7/
