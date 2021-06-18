---
title: "关于base64"
categories: ["默认分类"]
tags: []
draft: false
slug: "20"
date: "2015-12-04 18:10:19"
---

之前做雪碧图，还记得就是为了减少http请求，哪你有没有脑洞过，如果不要请求直接把图片编码成字符串嵌入在html中，那不就更省事了吗，所以就有了题目中的base64编码。

先看完优点，在看她的缺点，这样我们才能正确选择使用它的场合，base64既然把图片变成了字符串，所以比一般比原文件大一些，而且在css里写的可是编码啊，要是一个大图代码量也太大了吧，所以这就决定了base64一般只用在像小图标这种指甲盖大小的图片上。

图片怎么转换成base64？
很简单，用你的chrom浏览器就够了，在source里查看你的图片，显示的就是base64编码了。另外用gzip还能对编码进行进一步的压缩。

怎么引入到css/html中？

    background: url(data:image/gif;base64,图片编码) no-repeat center;
    
    <img src="data:image/gif;base64,图片编码">


