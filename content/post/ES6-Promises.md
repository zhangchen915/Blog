---
title: "ES6 Promises"
categories: ["默认分类"]
tags: []
draft: false
slug: "ES6-Promises"
date: "2016-06-13 14:30:00"
---

1.Promise.resolve
---------------
new Promise的快捷方式也可以将 thenable 对象转换为promise对象。

    Promise.resolve(42);   //和下面等价
    new Promise(function(resolve){
        resolve(42);
    });
    
    var promise = Promise.resolve($.ajax('/json/comment.json'));// => promise对象
    promise.then(function(value){
       console.log(value);
    });

2.promise.then() 和 .catch()
-------------------
通过 .then 和 .catch 来注册回调函数，promise可以写成方法链的形式

    aPromise.then(function taskA(value){
        // task A
    }).then(function taskB(vaue){
        // task B
    }).catch(function onRejected(error){
        console.log(error);
    });

.catch 是 promise.then(undefined, onRejected); 方法的一个别名,它和 .then 没有本质区别。那为什么我们不用 then 还要引入catch 方法哪？其实是因为使用 promise.then(onFulfilled, onRejected) 的话在 onFulfilled 中发生异常的话，在 onRejected 中是捕获不到这个异常的。

3.Promise.all 和 Promise.race
----------------------------
前面都是处理一个 promise 对象，当需要同时处理多个 promise 对象时就涉及到了一个棘手的问题，每个对象完成的时间都不相同那什么时候调用 .then ？为解决这个问题，promise设计了这两个处理多个对象的方法。

Promise.all 和 Promise.race 接收一个 promise对象的数组作为参数，Promise.all当这个数组里的所有promise对象全部变为resolve或reject状态的时候，它才会去调用 .then 方法；Promise.race 则相反，只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理。

4.Deferred 和 Promise
--------------------

Deferred 其实是第三方库自己定义的东西，比如 jquery.Deferred
需要注意的是$.ajax()操作完成后，如果使用的是低于1.5.0版本的jQuery，返回的是XHR对象，是无法进行链式操作；如果高于1.5.0版本，返回的是deferred对象，可以进行链式操作。

    var dtd = $.Deferred(); // 生成Deferred对象
    var wait = function(dtd){
    var tasks = function(){
          alert("执行完毕！");
          dtd.resolve(); // 改变Deferred对象的执行状态
        };
        setTimeout(tasks,5000);
    };
        
    dtd.promise(wait);    //在wait对象上部署Deferred接口
        
    wait.done(function(){ alert("哈哈，成功了！"); })
        .fail(function(){ alert("出错啦！"); });
    
    wait(dtd);

在上面的jquery中没有提到的deferred对象的方法还有：
1.deferred.reject() 这个方法与deferred.resolve()正好相反，调用后将deferred对象的运行状态变为"已失败"，从而立即触发fail()方法。
2.$.when() 这个方法和 ES6 的 Promise.all类似
3.deferred.then()
done()和fail()合在一起写就是 then() 方法 .then(successFunc, failureFunc );

**拓展阅读：**
[1.jQuery的deferred对象详解][1]
[2.Deferred Object][2]
[3.JavaScript Promise迷你书（中文版）][3]
[4.通过 ES6 Promise 和 jQuery Deferred 的异同学习 Promise][4]


  [1]: http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html
  [2]: http://api.jquery.com/category/deferred-object/
  [3]: https://www.gitbook.com/book/wohugb/promise/details
  [4]: https://segmentfault.com/a/1190000003691961
