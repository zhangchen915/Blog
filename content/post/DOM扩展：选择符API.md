---
title: "DOM扩展：选择符API"
categories: ["默认分类"]
tags: []
draft: false
slug: "34"
date: "2015-12-07 22:26:00"
---

你知道吗，jQuery就是通过css选择符查询得到DOM来取得元素引用。
既然css查询这么好，浏览器也把它吸收成了自己内置的API(Selectors API)，所以相比一些js库性能有了很大提升。别担心IE8+都兼容。
Selectors API分为Level 1和Level 2
其中Selectors API Level 1的核心方法为两种：querySelector()和querySelectorAll().

**1.querySelector()方法**
querySelector()方法接受一个css选择符，返回第一个匹配元素，如果没有那就返回null。

    var body=document.querySelector('body');
    var id=document.querySelector('#id');
**2.querySelectorAll()方法**
很显然与之前的区别就是返回的不止一个元素，而是返回一个NodeList对象。
那怎么得到NodeList里保存的元素哪，我们可以使用item()方法，或者用下标遍历。

    var class=document.querySelector('.class').item();
    var class=document.querySelector('.class')[0];

注意在使用选择符API时要注意与jQuery区别。

    <body>
    <div id="test"><p>test</p></div>
    </body>
    var mydiv = document.getElementById("test");
    mydiv .querySelector("div p").length; // 1
    jQuery(ele).find("div p").length; // 0
    mydiv .querySelector("body p").length; // 1
    jQuery(ele).find("body p").length; // 0
