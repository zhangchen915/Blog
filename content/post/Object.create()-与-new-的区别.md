---
title: "Object.create() 与 new 的区别"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "590"
date: "2018-09-02 14:19:00"
---

我们先看一下二者具体做了些什么：
#### new Test():

- create new Object() obj
- set obj.__proto__ to Test.prototype
- return Test.call(obj) || obj;

**最后一步如果构造函数未返回对象，则将该新对象返回，即this，如返回了对象，则替换this。**

#### Object.create( Test.prototype )

- create new Object() obj
- set obj.__proto__ to Test.prototype
- return obj;


所以说二者区别：
`new X= Object.create( X.prototype )+ X.constructor() `

看下面一个例子：
```js
function Dog1(name){
   return {dogName: name};
}

function Dog2(name){}

Dog1.prototype.setName = Dog2.prototype.setName = function(name){
    this.name = name;
};

let pet1 = new Dog1('rocky'); 
console.log(pet1.dogName);           //rocky  
pet1.setName('skye');                //error

pet1 = new Dog2();
pet1.setName('skye');
console.log(pet1.name);              //skye            

let pet2 = Object.create(Dog1.prototype);
pet2.setName('skye');                //ok
console.log(pet2.name);              //skye
console.log(pet2.dogName);           //undefined
```

Dog1方法返回一个对象，所以替换了this，而我们使用Dog2就能访问原型上的方法了。

参考文章：
[Understanding the difference between Object.create() and new SomeFunction()][1]


  [1]: https://stackoverflow.com/questions/4166616/understanding-the-difference-between-object-create-and-new-somefunction
