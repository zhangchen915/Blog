---
title: "ES6 迭代协议"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "475"
date: "2017-10-25 22:12:00"
---

ES6引入了一种用于遍历数据的新机制：迭代。
下面两个概念是迭代的核心：

- 一个可迭代的是一个数据结构，希望使其元素可供公众访问。它通过实现一个关键的方法来实现Symbol.iterator。该方法是迭代器的工厂。
- 一个迭代器是用于遍历数据结构的元素的指针。用TypeScript的接口来表示，这些角色如下所示：

```ts
interface Iterable {
    [Symbol.iterator]() : Iterator;
}
interface Iterator {
    next() : IteratorResult;
}
interface IteratorResult {
    value: any;
    done: boolean;
}
```

## 可迭代协议
**可迭代的数据源：数组，字符串，Map，Set，DOM数据结构。**
**注意：对象不可迭代。**
你可以在控制台输入`new Map`然后在`__proto__`属性中就能找到如下内容：
```
Symbol(Symbol.iterator):ƒ entries()
```

ES6引入了接口Iterable。数据消费者使用它，数据源实现它：
![iteration.png][1]

我们可以把这个方法拿出来，看看他是如何执行的。

```js
const arr = ['a','b','c'];
const iter = arr[Symbol.iterator]();
```
根据上面的接口，迭代器有个`next()`方法，我们可以重复调用它来检索数组中的“内部”项：

```js
iter.next()
{value：'a'，done：false}
iter.next()
{value：'b'，done：false}
iter.next()
{value：'c'，done：false}
iter.next()
{value：undefined，done：true}
```

Iterable迭代器是所谓的协议（接口加上使用它们的规则）的一部分，用于迭代。该协议的一个关键特征是它是顺序的：迭代器一次返回一个值。

###为何对象不可迭代
你也可以在控制台试一下`new Object`，你会发现Object上根本没有实现`Symbol.iterator`方法，所以没法用`for...of`遍历。其实这是标准故意为之的，因为迭代一个对象要考虑很多种情况，比如原型的来源包不包括原型链上的，原型是否可枚举，键的类型是String还是包括Symbol。所以想要遍历对象需要使用`for...in`，由你自己来定义具体的迭代方式。

## 迭代器协议

我们参照上面的接口手动实现一个迭代器：

```js
const iterable = {
    [Symbol.iterator]() {
        let step = 0;
        const iterator = {
            next() {
                if (step <= 2) {
                    step++;
                }
                switch (step) {
                    case 1:
                        return { value: 'hello', done: false };
                    case 2:
                        return { value: 'world', done: false };
                    default:
                        return { value: undefined, done: true };
                }
            }
        };
        return iterator;
    }
};
```
我们试一下，其实是可以迭代的：

```js
for (const x of iterable) {
    console.log(x);
}
// Output:
// hello
// world
```


### 使用迭代器的语句
- 通过Array模式进行解构
- for-of 循环
- Array.from()
- 展开操作符 (...)
- Maps和Sets的构造方法
- Promise.all()，Promise.race()
- yield*

##参考文献
[迭代协议][2]
[21. Iterables and iterators][3]
[为什么es6里的object不可迭代？][4]


  [1]: https://img.zhangchen915.com/2017/10/117367234.png
  [2]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols
  [3]: http://exploringjs.com/es6/ch_iteration.html#sec_iterating-language-constructs
  [4]: https://www.zhihu.com/question/50619539
