---
title: "使用RxJS处理HTTP请求"
categories: ["前端"]
tags: ["HTTP","Angular","RxJS"]
draft: false
slug: "515"
date: "2018-04-01 14:22:00"
---

本文介绍用RxJS处理一些常见HTTP交互场景。

###经典的搜索github用户
首先我们需要将HTTP请求转换成可观察的流来提供给RxJS操作，这个过程需要通过`Rx.Observable.fromPromise`方法来实现。之后我们需要用map操作符对源 observable 的每个值即事件对象应用投影函数，将其转换成我们需要的值。这里我们先简单的获取输入框的内容。

```js
let userClicksSearchButton = Rx.Observable.fromEvent(
        $("#search-button"), 'click'
    ).map(event => { $("#search-box").val() });

userClicksSearchButton
    .flatMap((searchTerm) => {
        return Rx.Observable.fromPromise(
            $.get('https://api.github.com/users/' + searchTerm)
        )
    })
    .subscribe((response) => {
    });
```

 > flatMap会把原来的stream投射出的元素转换为另一个stream，然后最后 将这些stream合并为一个stream进行投射出来。

在这个例子中仅仅考虑到了错误处理，所以距离完美的程序我们还需要做很多，例如：

1. 错误处理
在catch语句中捕获错误，最后需要返回一个空的可观察流。
```js
userClicksSearchButton
    .flatMap((searchTerm) => {
        return Rx.Observable.fromPromise(
            $.get('https://api.github.com/users/' + searchTerm)
        ).catch((response) => {
            renderError(response.statusText);
            return Rx.Observable.empty();
        });
    })
    .subscribe((response) => {
    });
```

1. 错误重试
使用`retray`或`retryWhen`操作符。

1. 防抖
我们的应用程序在用户每输入一个字母时都会触发一个请求。使用RxJS，我们可以通过简单的额外函数调用`debounce`来解决这个问题。

1. 按请求顺序返回结果

将值映射成内部 observable，并按顺序订阅和发出。


###Loading
```js
Observable.fromEvent(button, "click").pipe(
  tap(() => this.loading = true),
  concatMap(() => {}),
  tap(() => this.loading = false),
).subscribe(() => {
  
});
```

⚠️：Rxjs5.5 后`to`操作符重命名为了`tap`

###无限滚动

```js
Observable.fromEvent(window, "scroll") 
   .map(() => window.scrollY)
   .filter(current => current >=  document.body.clientHeight - window.innerHeight)
   .debounceTime(200) //停止滚动200ms后，继续执行
   .distinct() //过滤掉重复的值
   .map(y => Math.ceil((y + window.innerHeight)/ (this.itemHeight * this.numberOfItems)));
```
