---
title: "JS 监听对象变化的几种方式"
categories: ["前端"]
tags: ["设计模式"]
draft: false
slug: "581"
date: "2018-08-06 13:00:00"
---

##一、使用Object.defineProperties 

`Object.defineProperty` 实现对对象的监听，我们是在对象上设置`getter/setter`，所以也就仅限于监听对象属性的修改。

```js
var obj = {
  foo: 1,
  bar: 2
};
var obj = Object.defineProperties({}, {
    "foo":{
        get:function(){
            console.log("Get:"+this.value);
        },
        set:function(val){
            console.log("Set:"+val);
            this.value = val;
        }
    },

    "bar":{         
        get:function(){
            console.log("Get:"+this.value);
        },
        set:function(val){
            console.log("Set:"+val);
            this.value = val;
        }
    }
 });
```

Vue就用这种方式实现的双向绑定。
> 当你把一个普通的 JavaScript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。

但这种方式也有他的问题：
- 用索引直接设置一个数组的项不会触发变更检测
- 对象属性的增删

```js
Vue.set(vm.items, indexOfItem, newValue)
Vue.set(object, key, value)
```

##二、Proxy
ES6代理实现数据绑定，实际上我们依然是使用 getter/setter 只不过我们没有在对象上设置，而是代理了 getter/setter 的行为。
`Proxy` 一般与 `Reflect` 一起使用。`Reflect` 对象的方法与`Proxy`对象的方法一一对应。`Reflect`上获取默认行为，`Proxy`修改默认行为，`Reflect`一般作为的`Proxy`的基础。
```js
var proxied = new Proxy(obj, {
  get: function(target, prop) {
    console.log({ type: 'get', target, prop });
    return Reflect.get(target, prop);
  },
  set: function(target, prop, value) {
    console.log({ type: 'set', target, prop, value });
    return Reflect.set(target, prop, value);
  }
});
```
[nx-observe][1]使用代理机制实现了一个完整的数据绑定解决方案。
注意：Proxy不是语法糖，所以bable无法将其转换成ES5。

##三、Observable
理解观察者模式
**Observable是一个接受观察者并返回函数的函数。**
```js
   class Observable {
        constructor(subscribe) {
            this._subscribe = subscribe;
        }

        subscribe(observer) {
            return this._subscribe(observer);
        }
    }
```

观察者`observer`:一个带有`next`，`error`和`complete`方法的对象。
```js
const observer = {
  next: x => console.log(x),
  error: err => console.error(err),
  complete: () => console.log('done'),
}
```

将`Promise`转化为`Observable`
```js
static fromPromise(promise) {
  return new Observable(observer => {
    promise.then(val => {
      observer.next(val); observer.complete();
    })
    .catch(e => {
      observer.error(val); observer.complete();
    });
  })
}
```
测试：
```js
const promise1 = new Promise((res, rej) => {
    setTimeout(() => {
        res(1)
    }, 1000)
})

Observable.fromPromise(promise1).subscribe(observer);
```

使用RxJS5时，可以使用`BehaviorSubject`监听对象。
- `BehaviorSubject` 可以給予初始值
- 每一个 `Observer` 都可以在注册在当下，立刻取得目前 `BehavoirSubject` 的值（规observable仅在收到onnext时触发）

参考文章：
[BehaviorSubject vs Observable?][2]


  [1]: https://github.com/nx-js/observer-util
  [2]: https://stackoverflow.com/questions/39494058/behaviorsubject-vs-observable
