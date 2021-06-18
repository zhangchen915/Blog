---
title: "IE下ajax失效的解决或返回空数据"
categories: ["前端"]
tags: ["兼容"]
draft: false
slug: "92"
date: "2016-01-15 17:03:00"
---

最近遇到了一个奇葩的问题，页面用ajax获取数据，但是这个数据在chrome一些现代浏览器下正常，但是在ie下不是返回空数据就是报错，查了半天终于找到问题原因。
原来ajax的地址里有中文.......

在Ajax调用中，IE总是采用GB2312编码（操作系统的默认编码），而其他现代浏览器都是采用utf-8编码。

在js处理代码中加入先对地址进行编码转换，就正常了。

Javascript语言用于编码的函数：
**1. escape()**  **（已废弃，请用 encodeURI 或 encodeURIComponent 代替）**
该方法不会对 ASCII 字母和数字进行编码，也不会对下面这些 ASCII 标点符号进行编码： * @ - _ + . / 。其他所有的字符都会被转义序列替换。

**2. encodeURI()**
该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。
该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,#

**3. encodeURIComponent()**
encodeURIComponent 转义除了字母、数字、-、_、.、!、~、*、'、(和)之外的所有字符。

第一个函数年代太古老，已经不被推荐使用了，那剩下两个怎么选择，其实从字面上看encodeURIComponent()只是多了一个component（零件）单词，所以encodeURI()编码整个URI，而encodeURIComponent() 将文本字符串编码为一个统一资源标识符 (URI) 的一个**组件**。那组建与整个URI又有什么区别，其实就是“/”会被encodeURIComponent()编码，当这个编码结 
果被作为请求发送到 web 服务器时将是无效的。
