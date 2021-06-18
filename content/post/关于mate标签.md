---
title: "关于mate标签"
categories: ["默认分类"]
tags: []
draft: false
slug: "91"
date: "2016-01-10 23:45:00"
---

W3C上就一句话：`<meta>` 元素可提供有关页面的元信息（meta-information），比如针对搜索引擎和更新频度的描述和关键词。
MDN上的定义更玄幻：HTML Meta 元素用来表达任何其他 HTML 元相关元素 (`<base>, <link>, <script>, <style>` 或者 `<title>`) 等无法表达的信息。
这无法表达的是什么鬼，先看看mate标签的属性和值。

一、mate标签的属性
-----------

content属性（必须）
-------------
取值：some text
描述：定义与http-equiv或name属性相关的元信息，content一般与name或http-equiv连用。

http-equiv属性
------------
取值：
**1.content-type：规定文档的字符编码**
`<meta http-equiv="content-type" content="text/html; charset=UTF-8">`
上面是老式写法，在 HTML 5 中定义了一个新的属性charset，所以现在规定编码只需要如下代码：
`<meta charset=UTF-8">`

**2.expires**
表示存在时间，允许客户端在这个时间之前不去检查（发请求），等同max-age的效果。但是如果同时存在，则被Cache-Control的max-age覆盖。
格式：
Expires = "Expires" ":" HTTP-date
例如
Expires: Thu, 01 Dec 1994 16:00:00 GMT （必须是GMT格式）

**3.refresh**
规定自动刷新时间间隔或重定向
`<meta http-equiv="refresh" content="300">`
`<meta http-equiv="refresh" content="5;url= ">`

name属性
------
把 content 属性起个名字，name和content是最常用的，而且经常一起使用的属性。
取值：
**1.keywords**
定义页面的搜索关键词，每个网页应具有描述该网页内容的一组唯一的关键字，注意标记不应超过 874 个字符。
目前Google已经不在对mate标签赋予权重，不过Baidu依然在用，所以还是要写。
`<meta name="keywords" content="凤凰金融，p2p理财，理财产品，互联网金融，p2p网贷">`

**2.description**
页面描述，每个网页都应有一个不超过 150 个字符且能准确反映网页内容的描述标签。
<meta name="description" content="150 words" />  

**3.viewport**
viewport能优化移动浏览器的显示。
对于移动开发最常用的就是下面这段代码了：
`<meta name="viewport" content="width=device-width, initial-scale=1.0,maximum-scale=1.0, user-scalable=no"/>`
**注意**：`width=device-width` 会导致 iPhone 5 添加到主屏后以 WebApp 全屏模式打开页面时出现黑边，如果不是响应式网站，不要使用initial-scale或者禁用缩放。 

**其他取值**
author（定义作者），generator，revised，others

三、mate标签的其他应用

**转码申明：**用百度打开网页可能会对其进行转码（比如贴广告），避免转码可添加如下meta
`<meta http-equiv="Cache-Control"content="no-siteapp" /> `

**浏览器内核控制：**国内浏览器很多都是双内核（webkit和Trident），webkit内核高速浏览，IE内核兼容网页和旧版网站。而添加meta标签的网站可以控制浏览器选择何种内核渲染。参考文档
<meta name="renderer" content="webkit|ie-comp|ie-stand">

在 HTML 5 中，不再支持 scheme 属性。
