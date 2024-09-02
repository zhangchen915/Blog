---
title: "Javascript 中的 RAII？"
categories: ["前端"]
tags: []
draft: false
slug: "779"
date: "2020-02-14 18:11:00"
---

<img src="https://img.bi-bo.cn/2020/02/1147220378.png" >

RAII 的全称是 “Resource Acquisition is Initialization”  [^1]（资源获取即初始化），它是C++之父Bjarne Stroustrup提出的设计理念，目的是确保异常下的资源管理。换句话说，不管当前作用域以何种方式退出，都会执行资源释放等操作。

C++中 RAII 应用很广，C++11 中的两种基本的锁类型，lock_guard 和 unique_lock ，通过对lock和unlock进行一次薄的封装，实现了自动unlock。
> std::lock_guard其功能是在对象构造时将mutex加锁，析构时对mutex解锁，这样一个栈对象保证了在异常情形下mutex可以在lock_guard对象析构被解锁，lock_guard拥有mutex的所有权。

使用`try { ... } finally { ... }`语句也可以实现类似效果。

下面是在 Node 中写文件为例，我们需要显式关闭流。
```js
function hello(cb) {
  var stream = fs.createWriteStream('example.txt')
  stream.write('Hello World')
  stream.close()
}
```
如果在两者之间抛出异常，流将泄漏；至少直到下一次垃圾回收为止。

我们可以使用闭包或Promise，创建流然后调用处理程序，最后的finally确保我们可以正确关闭流。
```js
function usingWriteStream(name, handler) {
  var stream = fs.createWriteStream('example.txt')
  try {
    return handler(stream)
  } finally {
    stream.close()
  }
}

function hello() {
  usingWriteStream('example.txt', (stream) => {
    stream.write('Hello World')
  }
}
```
更进一步，Node中创建流的函数有很多，所以我们还能改造一下，把流的创建函数作为参数。

```js
var usingWriteStream = (name, handler) =>
  usingStream(fs.createWriteStream.bind(null, name), name)
```

多亏了闭包，我们可以在Javascript中构建类似于RAII的模式，以确保我们的资源不会泄漏。
即使在无法立即识别“资源”的情况下，这在许多情况下也很有用。

例如 Immutable.js 中的 [Map.withMutations()] 使用相似的模式：

```js
const { Map } = require('immutable')
const map1 = Map()
const map2 = map1.withMutations(map => {
  map.set('a', 1).set('b', 2).set('c', 3)
})
assert.equal(map1.size, 0)
assert.equal(map2.size, 3)
```
这里会创建2个而非4个新的Map，`可变 map`就是资源。这样可以确保此对象不会轻易泄漏到当前作用之外，并且可以正确地将其转换回不可变 Map。

在C#中有一个 `using` 语法 ，其实就是 finally 的语法糖。我们可以在TS中去模拟：

```ts
interface IDisposable {
    dispose();
}

function using<T extends IDisposable,
    T2 extends IDisposable,
    T3 extends IDisposable>(disposable: [T, T2, T3], action: (r: T, r2: T2, r3: T3) => void);
function using<T extends IDisposable, T2 extends IDisposable>(disposable: [T, T2], action: (r: T, r2: T2) => void);
function using<T extends IDisposable>(disposable: T, action: (r: T) => void);
function using(disposable: IDisposable[], action: (...r: IDisposable[]) => void)
function using(disposable: IDisposable | IDisposable[], action: (...r: IDisposable[]) => void) {
    let disposableArray = disposable instanceof Array ? disposable : [disposable];
    try {
        action(...disposableArray);
    } finally {
        disposableArray.forEach(d => d.dispose());
    }
}


// Usage
class UserNotify { dispose() { console.log("Done"); } }

class Other { dispose() { console.log("Done Other"); } }

using(new UserNotify(), userNotify => {
    console.log("DoStuff");
})
// It will type the arrow function parameter correctly for up to 3 parameters, but you can add more overloads above.
using([new UserNotify(), new Other()], (userNotify, other) => {
    console.log("DoStuff");
})
```

上面的种种还是没法和RAII比，特别是在释放的资源有多个的时候，只能在 finally 中挨个检查各个资源是否已经被申请。例如：
```js
  void f()
  {
    try {
      acquire resource1;
      … // #1
      acquire resource2;
      … // #2
      acquire resourceN;
      … // #N
    } finally {
      if(resource1 is acquired) release resource1;
      if(resource2 is acquired) release resource2;
      …
      if(resourceN is acquired) release resourceN;
    }
  }
```

[^1]: https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization

### 参考文章：
https://stackoverflow.com/questions/47788722/idisposable-raii-in-typescript/47792127#47792127
https://www.zhihu.com/question/20805826

