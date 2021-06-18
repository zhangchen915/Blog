---
title: "深入了解包含then属性的对象的promise解析"
categories: ["前端"]
tags: []
draft: false
slug: "670"
date: "2019-09-06 01:15:00"
---

我们将包含then属性的对象称为thenable对象。

TC39规范
> - If Type(resolution) is not Object, then
>   Return FulfillPromise(promise, resolution).
> - Let then be Get(resolution, "then").
> - If then is an abrupt completion, then
>   Return RejectPromise(promise, then.[[Value]]).
> - Let thenAction be then.[[Value]].
> - If IsCallable(thenAction) is false, then
>    Return FulfillPromise(promise, resolution).
> - Perform EnqueueJob("PromiseJobs", PromiseResolveThenableJob, « promise, resolution, thenAction »).

让我们一步一步解释：

### resolve 一个非对象元素
```js
Promise.resolve('Hello').then(
  value => console.log(`Resolution with: ${value}`)
);

// log: Resolution with: Hello
```

### resolves 一个包含 abruptCompletion 的 then 对象
abruptCompletion 表示任何非正常的完成值。
```js
const value = {};
Object.defineProperty(
  value,
  'then',
  { get() { throw new Error('no then!'); } }
);

Promise.resolve(value).catch(
  e => console.log(`Error: ${e}`)
);

// log: Error: no then!
```

### resolves 包含 then 且其属性不为函数的对象
```js
Promise.resolve(
  { then: 42 }
).then(
  value => console.log(`Resolution with: ${JSON.stringify(value)}`)
);

// log: Resolution with: {"then":42}
```

### resolves 包含 then 且其属性为函数的对象
这部分是本文探讨的重点，也是递归promise链的基础。
当一个promise使用包含then方法的对象解析时，解析过程将then使用通常的promise参数调用resolve 和 reject。如果resolve没有被调用，promise将无法完成。
```js
Promise.resolve(
  { then: (...args) => console.log(args) }
).then(value => console.log(`Resolution with: ${value}`));

// log: [fn, fn]
//        |   \--- reject
//     resolve

// !!! No log of a resolution value
```

```js
Promise.resolve(
  { 
    then: (resolve) => { 
      console.log('Hello from then');
      resolve(42);
    }
  }
).then(value => console.log(`Resolution with: ${value}`));

// log: Hello from then
// log: Resolution with: 42
```

## 动态导入
当您使用动态import函数加载JavaScript模块时，import遵循相同的过程，因为它返回一个promise。导入模块的结构值将是一个包含所有导出值和方法的对象。

```js
// file.mjs
export function then (resolve) {
  resolve('Not what you expect!');
}

export function getValue () {
  return 42;
}

// index.mjs
import('./file.mjs').then(
  resolvedModule => console.log(resolvedModule)
);

// log: Not what you expect
```

##内存泄露的风险
参考co库的这个 issue https://github.com/tj/co/issues/180

原文地址：https://www.stefanjudis.com/today-i-learned/promise-resolution-with-objects-including-a-then-property/
