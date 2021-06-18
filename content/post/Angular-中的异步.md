---
title: "Angular 中的异步"
categories: ["默认分类"]
tags: []
draft: false
slug: "Angular-中的异步"
date: "2016-06-28 20:28:00"
---

之前已经了解过ES6中使用`Promise`，而在Angular中是通过 $q 服务实现的。

一、$q 的方法：
-------

1. $q.defer() 返回一个 deferred 对象。
2. $q.reject(reason) 包装一个错误，以使回调链能正确处理下去。
3. $q.when(value, [successCallback], [errorCallback], [progressCallback]) 返回一个 promise 对象，为了与ES6保持一致他还有一个别名 `resolve`。
4. $q.all(promises) 合并多个 promise 成一个，当所有的 promise 都完成时这个新的才算完成。
5. $q.race(promises) 合并多个 promise 成一个，只要有一个 promise 完成这个新的就算完成。

二、defer 和 promise 的方法
-----------

1.defer 对象有两个方法一个属性。

promise 属性就是返回一个 promise 对象的。
resolve() 成功回调
reject() 失败回调

2.promise 对象只有 then() 一个方法，注册成功回调函数和失败回调函数，再返回一个 promise 对象，以用于链式调用。
 $q.reject() vs deferred.reject()

**一个基本的例子：**

    var TestCtrl = function($q){
        var defer = $q.defer();
        var promise = defer.promise;
        promise.then(function(data){console.log('ok, ' + data)},
            function(data){console.log('error, ' + data)});
        //defer.reject('xx');
        defer.resolve('xx');
    }

首先通过 $q 服务得到一个 defer 实例，再通过这个实例的 promise 属性得到一个 promise 对象。promise 对象负责定义回调函数，defer 实例负责触发回调。

**拓展阅读：**
[1.广义回调管理][1]
[2.$q][2]


  [1]: https://checkcheckzz.gitbooks.io/angularjs-learning-notes/content/chapter11/11-2.html
  [2]: https://docs.angularjs.org/api/ng/service/$q
