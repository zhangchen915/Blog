---
title: "Promise 版本的数组遍历"
categories: ["前端"]
tags: ["javascript","Promise"]
draft: false
slug: "567"
date: "2018-07-09 22:51:00"
---

我们想要对数组中的值分别进行异步操作，但是数组对象的遍历方法只支持同步方法，如果传入异步函数后其结果往往超出预期，
所以我们就要自己来实现这些遍历方法的异步版本。

这里指的遍历方法包括：map、reduce、reduceRight、forEach、filter、some、every。
我们的需求有三种：串行，并行，当其中一个Promise完成或拒绝后退出。

这里我们以一个简单的异步函数为例：
```js
let multi = num => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (num) {
        resolve(num ** 2)
      } else {
        reject(new Error('num not specified'))
      }
    }, 1000)
  })
}
```

###map
```js
[1, 2, 3].map(async item => multi(item))
// > [Promise, Promise, Promise]
```
因为map能够返回Promise数组，所以我们用Promise.all将返回的数组包起来就可以了。

```js
await Promise.all([1, 2, 3].map(multi(item))
// > [1, 4, 9]
```

###forEach
直接使用forEach会并行执行，如果需要串行的话就需要我们自己来实现一个新方法。
```js
Array.prototype.forEachSync = async function (callback, thisArg) {
  for (let [index, item] of Object.entries(this)) {
    await callback(item, index, this)
}
```

### some 和 every
some在匹配到第一个true之后就会终止遍历，every反之。
```js
Array.prototype.someSync = async function (callback, thisArg) {
  for (let [index, item] of Object.entries(this)) {
    if (await callback(item, index, this)) return true
  }
  return false
}
await [1, 2, 3].someSync(async item => multi(item))
// > true
```

###Reduce
`Promise.all` 虽然能并发多个请求，但它有一个特点：**错误优先**。一旦出现一个`reject`，那么`Promise.all`也会`reject`。但大多数情况下，如果某一个请求出现问题，我们希望他不要影响接下来的请求。

```js
[1, 2, 3].map(async item => multi(item)).reduce((task, item) => {
  return task.then(() => item);
}, Promise.resolve())
```
这里执行`map`之后全部异步请求已经发起了，我们只需要顺序处理其返回的Promise数组。`task`的初始值为`Promise.resolve()`，它相当于一个中间变量，储存当前的 `promise`。

###控制并发数
我们实现了顺序请求，还有并发请求，但是着两种方案都是两种极端，考虑一下如果我们需要发出成百上千个请求，顺序执行时间太长，并发执行对机器压力太大，所以我们需要一种折中方案，控制并发请求。

`worker`函数每次取出`tasks`中的第一个值，然后执行异步函数，完成后递归执行。默认我们有三个`worker`，每个`worker`完成后都会从目前`tasks`再取出一个继续执行，直到`tasks`为空。
```js
function mockAPI(result, time = 1000) {
    time = time * result;
    return new Promise((resolve, reject) => {
        setTimeout(() => {
             console.log(result, time);
            resolve(result);
        }, time);
    });
}

function loadLimit(tasks, promiseFunction, MAX_CURRENCY = 3) {
    const promises = result = [];

    function worker(tasks) {
        const task = tasks.shift();
        if (!task) return;
        return promiseFunction(task).then(res => {
            result.push(res);
            return worker(tasks);
        });
    }

    for (let i = 0; i < MAX_CURRENCY; i += 1) {
        promises.push(worker(tasks));
    }

    return result;
}

loadLimit([5, 2, 3, 1, 2, 3, 1, 2, 2, 3, 3], mockAPI)
```

