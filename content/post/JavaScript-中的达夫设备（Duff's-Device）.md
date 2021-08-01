---
title: "JavaScript 中的达夫设备（Duff's Device）"
categories: ["前端"]
tags: ["javascript","性能优化"]
draft: false
slug: "448"
date: "2017-09-23 11:12:00"
---

首先我们先要了解[**循环展开（Loop unwinding）**][1]
```js
for (i = 1; i <= 60; i++){
   a[i] = a[i] * b + c;
}
```
可以如此循环展开：

```js
for (i = 1; i <= 60; i+=3){
  a[i] = a[i] * b + c;
  a[i+1] = a[i+1] * b + c;
  a[i+2] = a[i+2] * b + c;
}
```
这被称为展开了两次。虽然看起来程序优化之后比以前还长了，但是循环的次数却减少了，同时循环中的条件判断（i <= 60）也相应减少了。

但很明显，如果总循环次数无法被循环增量整除时会有剩余，所以就有了达夫设备。
```js
let testVal = 0;
let n = iterations / 8;
let caseTest = iterations % 8;

do {
 switch (caseTest) {
 case 0:
  testVal++;
 case 7:
  testVal++;
 case 6:
  testVal++;
 case 5:
  testVal++;
 case 4:
  testVal++;
 case 3:
  testVal++;
 case 2:
  testVal++;
 case 1:
  testVal++;
 }
 caseTest = 0;
}
while (--n > 0);
```
若`iterations`无法为8整除，则仍有`iterations%8`项未处理；所以加入了`switch/case`语句，但这里没有使用`break`关键字，我们通过`fall through`特性来处理剩余数据（注：C#强制break，golang默认break）。

![Duff's Device.png][2]

[**Duff's device**][3]
这是在`jsperf`上的测试，可以看到循环展开是普通循环的三倍，不过要注意然各个版本的浏览器也存在一定差异。

**我认为在前端开发中，大多数情况下没有必要进行这种优化的，除非数据量很大，或者你在写的是组件库，并且做过测试证明真的需要它。**


  [1]: https://zh.wikipedia.org/wiki/%E5%BE%AA%E7%8E%AF%E5%B1%95%E5%BC%80
  [2]: https://img.zhangchen915.com/2017/09/2213218042.png
  [3]: https://jsperf.com/duffs-device
