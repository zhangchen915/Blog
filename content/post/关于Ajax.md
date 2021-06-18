---
title: "关于Ajax"
categories: ["默认分类"]
tags: []
draft: false
slug: "关于Ajax"
date: "2015-12-20 15:50:00"
---

什么是Ajax
-------

AJAX (Asynchronous JavaScript and XML)表示异步Javascript & XML，使用这项技术能让页面无需重新刷新就能像服务器发送请求。

Ajax技术的核心是XMLHttpRequest对象（简称XHR），注意IE10以下的浏览器不支持原生的XHR对象。

XHR用法
-----

    //抹平浏览器差异
    function creatXHR(){
        if (window.XMLHttpRequest) {
            return new XMLHttpRequest();
        } else if(window.ActiveXObject) {
            return new ActiveXObject("Microsoft.XMLHTTP");
        } else {
    　　  alert(‘此浏览器不支持 XHR ！’);
        }
    }
    
    var http_request=creatXHR;
    
    //提供指定处理响应的函数名
    http_request.onreadystatechange = youFunction;   
    //发送请求
    http_request.open('GET', 'http://www.example.org/some.file', true); 
    http_request.send(null);

true表示发送的是异步请求，也就是Ajax中的A
注意：URL不能跨域，即只能向同一个域且端口和协议都相同的URL发送请求
到现在我们用JavaScript发送了一个异步请求，然后我们还要对请求的状态进行判断，在判断之前首先要了解不同的状态代码的含义，也就是XHR对象的readyState属性：

 - 0 (未初始化)：尚未调用open方法
 - 1 (正在装载)：已经调用open方法，但还没用调用send
 - 2 (装载完毕)：已经调用send，但还未收到响应
 - 3 (交互中)：正在接收相应数据
 - 4 (完成)

只要readyState属性只发生变化，就会触发readystatechange时间，所以我们可以用它来检测每次变化之后的readyState属性值。

**处理服务器的响应**
------------

    function youFunction(){
    if (http_request.readyState == 4) {     //判断请求状态
        if (http_request.status == 200) {       //判断HTTP服务器响应的状态值
            alert(http_request.responseText);
           } else {
            alert('There was a problem with the request.');
           }
        }
    }

jQuery中的Ajax
------------

请求	                        描述
$().load(url,data,callback)	把远程数据加载到被选的元素中
$.ajax(options)	                把远程数据加载到 XMLHttpRequest 对象中
$.get(url,data,callback,type)	使用 HTTP GET 来加载远程数据
$.post(url,data,callback,type)	使用 HTTP POST 来加载远程数据
$.getJSON(url,data,callback)	使用 HTTP GET 来加载远程 JSON 数据
$.getScript(url,callback)	加载并执行远程的 JavaScript 文件

扩展阅读：

[开始Ajax][1]
[使用XMLHttpRequest][2]
[原生 AJAX 入门][3]


  [1]: https://developer.mozilla.org/zh-CN/docs/AJAX/Getting_Started
  [2]: https://developer.mozilla.org/zh-CN/docs/AJAX/Getting_Started
  [3]: https://segmentfault.com/a/1190000000490034
