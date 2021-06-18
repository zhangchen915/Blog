---
title: "Express入门笔记"
categories: ["默认分类"]
tags: ["express"]
draft: false
slug: "59"
date: "2015-12-16 21:27:00"
---

通过 npm init 命令为你的应用创建一个 package.json 文件
$ npm install express --save //安装Express并将其添加到package以来列表
$ node app.js //启动应用

    //引入静态文件（html、css、js）
    app.use(express.static('public'));
    
    //项目依赖
    var express = require('express');
    var app = express();
    //监听端口
    var server = app.listen(3000, function () {
      var host = server.address().address;
      var port = server.address().port;
    
      console.log('Example app listening at http://%s:%s', host, port);
    });

**基本路由**

app.METHOD(path, [callback...], callback)， app 是 express 对象的一个实例， METHOD 是一个 HTTP 请求方法， path 是服务器上的路径， callback 是当路由匹配时要执行的函数。
1.路由方法：路由方法源于 HTTP 请求方法，和 express 实例相关联。
2.路由路径：路由路径和请求方法一起定义了请求的端点，它可以是字符串、字符串模式或者正则表达式。
3.路由句柄：可以为请求处理提供多个回调函数，其行为类似 中间件。唯一的区别是这些回调函数有可能调用 next('route') 方法而略过其他路由回调函数。可以利用该机制为路由定义前提条件，如果在现有路径上继续执行没有意义，则可将控制权交给剩下的路径。路由句柄有多种形式，可以是一个函数、一个函数数组，或者是两者混合。

响应方法：res.send，res.download()，res.end()等等

    // 对网站首页的访问返回 "Hello World!" 字样
    app.get('/', function (req, res) {
      res.send('Hello World!');
    });
    // 网站首页接受 POST 请求
    app.post('/', function (req, res) {
      res.send('Got a POST request');
    });
