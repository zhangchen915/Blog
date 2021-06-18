---
title: "JS中的协程"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "335"
date: "2017-06-19 12:11:00"
---

##什么是协程
协程其实可以认为是比线程更小的执行单元。线程包含于进程，协程包含于线程。

##协程的优点
**协程允许我们写同步代码的逻辑，却做着异步的事**，避免了回调嵌套，使得代码逻辑清晰。
我们举一个读取文件的例子：

```js
function *asyncJob() {
  // ...其他代码
  var f = yield readFile(fileA);
  // ...其他代码
}
```
异步操作是为了避免IO操作阻塞线程。那么协程挂起的时刻应该是当前协程发起异步操作的时候，而唤醒应该在其他协程退出，并且他的异步操作完成时。所以例子中的函数执行顺序如下：

1. 协程asyncJob开始执行。
2. 协程A执行到一半，进入暂停，执行权转移到协程readFile。
3. 协程readFile执行完成后交还执行权。
4. 协程asyncJob恢复执行。

##协程的实现
JS中有Generator/yield和async/awite实现。
这两者差不多，其实async函数可以看成Generator函数的语法糖。

```js
var fs = require('fs');

var readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

写成`async`函数，就是下面这样。

```js
var asyncReadFile = async function (){
  var f1 = await readFile('/etc/fstab');
  var f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

**`async`函数对 Generator 函数的改进，体现在以下四点。**
> 1.内置执行器。`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样。不像Generator函数，需要调用`next`方法，或者用`co`模块，才能得到真正执行，得到最后结果。
> 2.更好的语义。`async`和`await`，比起星号和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。
> 3.更广的适用性。 `co`模块约定，`yield`命令后面只能是Thunk函数或Promise对象，而`async`函数的`await`命令后面，可以是Promise对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。
> 4.返回值是Promise。`async`函数的返回值是Promise对象，这比Generator函数的返回值是Iterator对象方便多了。你可以用`then`方法指定下一步的操作。

进一步说，`async`函数完全可以看作多个异步操作，包装成的一个Promise对象，而`await`命令就是内部`then`命令的语法糖。


**参考文献：**
[1.异步操作和Async函数][1]


  [1]: https://likebeta.gitbooks.io/es6tutorial/content/docs/async.html
