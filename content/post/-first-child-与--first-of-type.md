---
title: ":first-child 与 :first-of-type"
categories: ["前端"]
tags: ["CSS"]
draft: false
slug: "109"
date: "2016-02-29 15:20:00"
---

看起来好像这两个伪元素好像都是选择第一个元素的，先来看看下面的代码吧。

    p:first-child{color:#f00;}
    
    <div>
    	<h2>我是一个标题</h2>
    	<p>我是一个p</p>
    </div>

亲自试试才发现，好像选择器失效了。
其实`E:first-child`选择符，E必须是它的兄弟元素中的第一个元素，换言之，E必须是父元素的第一个子元素，`E:last-child`也是如此。

所以，一般我们不用 `:first-child` 而是用 `:first-of-type`。
`:first-of-type`匹配同类型中的第一个同级兄弟元素，E的父元素最高是html，该选择符总是能命中父元素的第1个为E的子元素，而不论第1个子元素是否为E，所以这个要比 `:first-child` 好用。
