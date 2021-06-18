---
title: "Angular 中的 $watch , $watchGroup() 与 $watchCollection()"
categories: ["前端"]
tags: ["Angular"]
draft: false
slug: "Angular中的-watch-watchGroup-与-watchCollection"
date: "2016-07-04 17:45:00"
---

一、$watch
--------

在Angular中大部分指令都依靠watch函数来监听Model变化，watch也是Angular的“脏检查”的核心之一。

`$watch(watchExp, listener, objectEquality)`

**1.watchExp** 很灵活，可以是一个$scope上的一个属性名，也可以是一个函数或一个字符串形式的表达式；
**2.listener** 是检测到变化时执行的函数,这个函数接受 newValue, oldValue 两个参数；
**3.objectEquality** 是一个可选布尔值，决定比较的深浅，默认是false，此时 watch 使用 === 进行浅比较，所以它对数组或 Object 进行比较是检查的是引用，也叫导致了数组或对象内容改变是并不会触发回调，同时两个内容相同的表达式也会判定为不同。当此项设置为 true 是，watch 会用 angular.equals() 进行深比较，这种比较方法会遍历数组或对象，这也导致了性能会比较差。

二、$watchGroup() 与 $watchCollection()
------------------------------------

因为 watch 深比较性能较差，所以 Angular 还提供了 `$watchGroup([watchExp], listener)` 和 `$watchCollection(obj, listener)` 方法来分别监听数组和对象。
`$watchGroup` 其实是使用 watch 监听一组 watchExp ,所以 watchGroup 不支持深比较
`$watchCollection` 比 $watch 进一步，但是基于性能考虑它只向内关注 1 层，对数组重新赋值，或是对数组元素进行新增、删除、修改时，回调会被调用，**注意只要是修改就会调用，如果给数组赋的值和之前一样也会触发回调**。如果某个数组元素内部的某个属性被更新时，回调不会被调用。

**参考阅读：**
[1.在 AngularJS 中用 $watch / $watchCollection 监听数组变化的研究][1]
[2.Angular文档：$rootScope.Scope][2]
[3.How to use a $watchGroup with object equality or deep-watch the properties in the group?][3]


  [1]: http://anjianshi.net/post/yan-jiu-bi-ji/ng-watch
  [2]: https://docs.angularjs.org/api/ng/type/$rootScope.Scope
  [3]: http://stackoverflow.com/questions/25712888/how-to-use-a-watchgroup-with-object-equality-or-deep-watch-the-properties-in-th?rq=1
