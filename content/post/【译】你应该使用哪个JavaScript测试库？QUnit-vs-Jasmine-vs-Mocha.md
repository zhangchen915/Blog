---
title: "【译】你应该使用哪个JavaScript测试库？QUnit vs Jasmine vs Mocha"
categories: ["前端"]
tags: ["测试"]
draft: false
slug: "217"
date: "2016-11-11 19:24:00"
---

不论你是在写浏览器端javascript还是后端的nodejs，总存在那么一个问题：我该使用什么单元测试库去确保我的代码如预期的运行呢？有一些流行的框架可供选择。如果你正在考虑Qunit、Jasmine或者Mocha，那么恰好我这有一些他们的优缺点信息介绍，你可能感兴趣。

![qunit_logo.png][1]

qunit肯定是我列表中最古老的三个，2008年第一次正式发布。因此，多年来它已经获得了良好的用户基础。它在jQuery中流行使用，并有很多地方有很好的支持。

它发展的如何呢？真的不让人满意...
##优点：
>从Q＆A到CI服务器有很多地方支持

##缺点：
>缺乏流利的语法
配置很头痛，必须不断维护
使得包括第三方库（如断言库）相对困难
异步测试可能有点头痛
没有与原生的命令行支持

![Jasmine.png][2]

Jasmine稍微更新一些，在qUnit之后2年发布。它已经有足够的时间成熟，同时还学习了其他JavaScript测试框架的经验教训。 它的建立是很容易设置和使用在几乎任何情况下。在大多数情况下在大多数情况下它需要一个运行者，如Karma或Chutzpah，但一些发行版（如jasmine-node)有内建的runner。

它怎么样？ 这对于大多数情况下是你可能想要的，异步代码是主要的问题。

##优点：
>对于node来说通过jasmine-node是很好安装的
运行测试无需浏览器用户界面
内置流畅漂亮的语法，完美兼容其它测试库
有许多CI服务器（如TeamCityp，CodeShip等）和一些本身不支持插件的服务器支持（jenkins有一个Maven插件）
可描述性的BDD范例

##缺点：
>异步测试可能有点头痛
所有测试文件都有个确切的后缀（默认*spec.js)

![mock.png][3]

Mocha是专为测试nodeJs模块而开发的，它是2012年发布的第一个主要版本，它的API与Jasmine的相似，添加了一些语法糖，使其具有更广的应用场景，如BDD。内建有runner，所以你不用担心runner了。它与Jasmine不同，它对测试异步方法有非常好的支持，使用done()函数：如果你的测试使用它，测试不会传递直到done()被调用，如果它不使用它，当测试到达测试方法结束时，测试将通过。

我的印象？非常适合我！

##优点：
>易于安装
运行测试无需浏览器用户界面
允许任何能够抛出失败异常测试库的使用
部分CI服务器和其它插件的支持
功能上更多是面向行为驱动开发或者行为面向测试驱动开发
高扩展性
轻而易举的进行异步测试

##缺点：
>较新的领域，因此在某些领域可能缺乏支持

#TL;DR
看看上面的三个框架，我的选择是明确的：Mocha和其他相比最灵活、最可用和最简单的配置。开箱即用，它提供了你需要的一切，并如此优雅。

如果你有兴趣看到这三个框架的例子，请查看我在研究时创建的repo：
https://github.com/thegrtman/javascript-test-framework-comparison

[原文地址][4]


  [1]: http://img.zhangchen915.com/2016/11/4125582959.png
  [2]: http://img.zhangchen915.com/2016/11/2968338635.png
  [3]: http://img.zhangchen915.com/2016/11/4006474259.png
  [4]: http://www.techtalkdc.com/which-javascript-test-library-should-you-use-qunit-vs-jasmine-vs-mocha/
