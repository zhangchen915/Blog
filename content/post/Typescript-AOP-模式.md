---
title: "Typescript AOP 模式"
categories: ["前端"]
tags: []
draft: false
slug: "601"
date: "2019-05-16 15:04:00"
---

AOP（Aspect Oriented Program）表示面向切面编程，它是OOP的补充,主要作用是将多个OOP模块中的通用代码抽取出来。切面的意思是系统中的一些通用功能，例如日志记录，缓存或验证。

例如，下面代码包含了日志记录等功能全部耦合在代码中，难以维护。
```js
function sample(arg: string) {
    console.log("sample: " + arg);
    if(!isUserAuthenticated()) {
        throw new Error("User is not authenticated");
    }

    if(cache.has(arg)) {
        return cache.get(arg);
    }

    const result = 42; // TODO complex calculation
    cache.set(arg, result);
    return result;
}
```

通过[TypeScript装饰器][1]可以改写如下形式：
```ts
@log
@authorize
@cache
function sample(arg: string) {
    const result = 42;
    return result;
}
```

在JS中使用装饰器，可以参考[该提案][2]


  [1]: http://www.typescriptlang.org/docs/handbook/decorators.html
  [2]: https://github.com/tc39/proposal-decorators
