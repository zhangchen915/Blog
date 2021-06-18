---
title: "banner 延迟加载"
categories: ["默认分类"]
tags: []
draft: false
slug: "101"
date: "2016-02-25 10:13:00"
---

通常banner图都很大，还好几张，一次性全下载下来很影响性能，而且页面加载时只要显示第一张，所以其实我们只要第一张图片直接加载，其他的图片只要等到轮播时再加载就行了。

下面做了一Demo，在Network 里可以看到延迟加载的效果。

<p data-height="500" data-theme-id="21453" data-slug-hash="QNLNyb" data-default-tab="result" data-user="zhangchen915" class='codepen'>See the Pen <a href='http://codepen.io/zhangchen915/pen/QNLNyb/'> FlexSlider Lazy</a> by zhangchen (<a href='http://codepen.io/zhangchen915'>@zhangchen915</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
