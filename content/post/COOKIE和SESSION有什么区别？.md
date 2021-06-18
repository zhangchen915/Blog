---
title: "COOKIE和SESSION有什么区别？"
categories: ["默认分类"]
tags: []
draft: false
slug: "93"
date: "2016-01-29 10:11:00"
---

**cookie保存在客户端，目的可以跟踪会话，也可以保存用户喜好或者保存用户名密码**
**session保存在服务器端，用来跟踪会话**

session是由cookie衍生出来的，从上面二者的区别也能看出，session主要是基于安全方面的考虑。

“通常session cookie是不能跨窗口使用的，当你新开了一个浏览器窗口进入相同页面时，系统会赋予你一个新的sessionid，这样我们信息共享的目的就达不到了，此时我们可以先把sessionid保存在persistent cookie中，然后在新窗口中读出来，就可以得到上一个窗口SessionID了，这样通过session cookie和persistent cookie的结合我们就实现了跨窗口的session tracking（会话跟踪）。”


session虽然是由cookie衍生出来的，但是session并不完全依赖于cookie，当用户禁用cookie的时候会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。

[COOKIE和SESSION有什么区别？][1]


  [1]: https://www.zhihu.com/question/19786827
