---
title: "Webpack、Browserify 、seajs、requirejs、Gulp和Grunt的都是什么？"
categories: ["默认分类"]
tags: []
draft: false
slug: "114"
date: "2016-03-01 22:58:00"
---

一、Gulp 和 Grunt
--------------

这两个是一种前端自动化工具，像自动刷新页面、压缩混淆js、编译less，coffeejs等等。简单来说，就是使用Gulp/Grunt，然后配置你需要的插件，就可以把以前需要手工做的事情让它帮你做了。现在一般用 gulp 的人比较多些。

二、seajs 和 requirejs
-------------------

seajs / require : 是一种在线"编译"模块的方案，相当于在页面上加载一个 CMD/AMD 解释器。这样浏览器就认识了 define、exports、module 这些东西。也就实现了模块化。

三、webpack 和 browserify
----------------------

browserify / webpack : 是一个预编译模块的方案，也就是说在他们都用在服务端，正因如此相比于浏览器端的模块化，这个方案更加智能。你可以在本地直接写各种各样的代码，不管是 AMD / CMD / ES6 风格的模块化，还是不同的文件如sass、less、jade，它都能认识，并且编译成统一的代码。

四、总结
----

Gulp、grunt是一个工具，而webpack等等是模块化方案。Gulp也可以配置seajs、requirejs甚至webpack的插件。

https://segmentfault.com/a/1190000002491282
