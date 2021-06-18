---
title: "关于Cookie"
categories: ["默认分类"]
tags: []
draft: false
slug: "关于Cookie"
date: "2015-12-16 23:10:00"
---

HTTP协议是无状态的，所以即服务器不知道用户上一次做了什么，我们使用cookie就是为了让服务器记住状态。比如当我们登录一个网站时，可以勾选“下次自动登录”。如果勾选了，那么下次访问同一网站时，用户会发现没输入用户名和密码就已经登录了。这正是因为前一次登录时，服务器发送了包含登录凭据（用户名加密码的某种加密形式）的Cookie到用户的硬盘上。第二次登录时，（如果该Cookie尚未到期）浏览器会发送该Cookie，服务器验证凭据，于是不必输入用户名和密码就让用户登录了。

**定义：**
Cookie是Web服务器向用户浏览器发送的一段Ascii文本.一旦接受到cookie,浏览器会把cookie的信息片段以"键/值"对的形式保存在本地.这以后,每次想同一服务器发送请求的时候,Web浏览器都会发送站点以前存储在本地的cookie.浏览器和Web服务器的通讯是通过Http协议进行通讯的,而cookie就保存在Http协议的请求部分(Set-Cookie).

**Cookie格式：**

    Set－Cookie: NAME=VALUE; Expires=DATE; Path=PATH; Domain=DOMAIN_NAME; Max-Age = value; HttpOnly; Secure
    //一个cookie的例子
    Set-Cookie：cookiename=cookievalue; Path=/auth; Domain=example.com; Expires=Mon, 10 Jun 2013 01:50:58 GMT; Max-Age: 3600; HttpOnly; Secure

1.NAME是该Cookie的名称，VALUE是该Cookie的值。在字符串“NAME=VALUE”中，不含分号、逗号和空格等字符。
2.Expires=DATE：Expires变量是一个只写变量，它确定了Cookie有效终止日期。该属性值DATE必须以特定的格式来书写：星期几，DD－MM－YY HH:MM:SS GMT，如果Expires缺省，则Cookie的属性值不会保存在用户的硬盘中，而仅仅保存在内存当中，Cookie文件将随着浏览器的关闭而自动消失。
3.Path=PATH：Path属性定义了Web服务器上哪些路径下的页面可获取服务器设置的Cookie。一般如果用户输入的URL中的路径部分从第一个字符开始包含Path属性所定义的字符串，浏览器就认为通过检查。如果Path属性的值为“/”，则Web服务器上所有的WWW资源均可读取该Cookie。缺省时Path的属性值为Web服务器传给浏览器的资源的路径名。
4.Domain=DOMAIN－NAME:Domain该变量是一个只写变量，它确定了哪些Internet域中的Web服务器可读取浏览器所存取的Cookie，即只有来自这个域的页面才可以使用Cookie中的信息。这项设置是可选的，如果缺省时，设置Cookie的属性值为该Web服务器的域名。
5.Max-Age：用 Expires 设置过期时间比较麻烦，因此 RFC 的规范中后来增加了 Max-Age 选项。例如设置 Max-Age=3600 表示在 1 小时后删除该 cookie。但是这个选项 IE 不支持（即使是 IE9）。
6.HttpOnly：设置这个选项表示禁止通过 JavaScript 访问该 cookie。
7.Secure：设置这个选项表示仅在 HTTPS 连接时才发送该 cookie。

**读取Cookie**
使用JavaScript来读取cookie属性的时候，其返回的值是一个字符串，该字符串都是由一系列名值对组成，不同名值对之间通过“分号和空格”分开，其内容包含了所有作用在当前文档的cookie。但是，它并不包含其他设置的cookie属性。通过document.cookie属性可以获取cookie的值，但是为了更好的查看cookie的值，一般会采用split()方法，将cookie值中的名值对都分离出来。
把cookie的值分离出来之后，必须要采用相应的解码方式（取决于之前存储cookie值时采用的编码方式），把值还原出来。比方说，先采用decodeURIComponent()方法把cookie值解码出来，之后再利用JSON.parse()方法转化成json对象。

    // 代码来自《JavaScript权威指南》
    // 将document.cookie的值以一个名值对形式组成的对象返回
    // 假设cookie的值存储的时候是采用encodeURIComponent()函数编码的
    function getCookies() {
        var cookies = {};                     // 初始化最后要返回的对象
        var all = document.cookie;            // 获取所有的cookie值
        if (all === "")                       // 如果该cookie属性值为空
            return cookies;                     // 返回一个空对象
        var list = all.split("; ");             // 分离出名值对
        for(var i = 0; i < list.length; i++) {  // 循环每对数据
            var cookie = list[i];
            var p = cookie.indexOf("=");                // 查找第一个“=”符号
            var name = cookie.substring(0,p);           // 获取名字
            var value = cookie.substring(p+1);          // 获取对应的值
            value = decodeURIComponent(value);          // 对其值进行解码
            cookies[name] = value;                      // 将名值对存储到对象中
        }
        return cookies;
    }

**cookie的缺点：**
1.Cookie数量和长度的限制。每个domain最多只能有20条cookie，每个cookie长度不能超过4KB，否则会被截掉，对于现在的web应用这点空间可能有点少。
2.安全性问题。如果cookie被人拦截了，那人就可以取得所有的session信息。即使加密也与事无补，因为拦截者并不需要知道cookie的意义，他只要原样转发cookie就可以达到目的了。所以我们需要控制cookie的生命期，使之不会永远有效。
3.有些状态不可能保存在客户端。例如，为了防止重复提交表单，我们需要在服务器端保存一个计数器。如果我们把这个计数器保存在客户端，那么它起不到任何作用。
4.cookie会被附加在每个HTTP请求中，所以无形中增加了流量。
