---
title: "AngularJS中的Provider"
categories: ["默认分类"]
tags: []
draft: false
slug: "AngularJS中的Provider"
date: "2016-06-25 11:20:00"
---

在学习Angular时总是说依赖注入依赖注入，那注入进去的到底是什么东西？

首先需要认识一下`$provide`即“供应商”，和constant、value、service、factory、provider方法，这些方法你之前应该听说过了，但是一定改没搞清楚他们的区别，其实他们都是创建“供应商”的方法。

AngularJS用$provide去**定义**一个供应商,这个`$provide`有5个用来**创建**供应商的方法：

**1.constant(obj)**
这个从字面上就能看出他是定义常量用的，他可以被注入到任何地方，但不可以使用装饰器（decorator）修饰。

**2.value(obj)**
这个也很简单就是定义值，和constant相比，value定义的值能够被改变，它不能被注入到config中，但是它可以被decorator装饰。

**3.factory(fn)**
factory应该最常用了，Angular调用factory时只是调用普通的function，所以factory可以返回任何东西。

**4.service(class)**
service和factory差别不多，只是他service接收的是一个构造函数，当第一次使用service的时候，angular会new Foo() 来初始化这个对象。

**5.provider(provider)**
看这个名字应该就能感觉这个应该是最强大的了，没错，以上除了constant其实都是对provider的封装。也是唯一能够注入到`.config()`的“提供商”。
`provider`必须有一个$get方法，$get函数会返回所有我们希望在控制器中进行访问的方法和属性。
注意：注入到config中不能直接写名称，需要用驼峰命名法写成 name + Provider

**拓展阅读：**
[1.AngularJS中的Provider们：Service和Factory等的区别][1]
[2.AngularJS 之 Factory vs Service vs Provider][2]


  [1]: https://segmentfault.com/a/1190000003096933
  [2]: http://www.oschina.net/translate/angularjs-factory-vs-service-vs-provider
