---
title: "使用 calc 优化页面出现滚动条时的效果"
categories: ["前端"]
tags: []
draft: false
slug: "296"
date: "2017-04-18 23:40:00"
---

# 一、防止出现滚动条
如果页面中有限制高度的表格，那么表格内外都可以滚动，这样的就会感觉有些怪异，而且windows原生的滚动条比较丑。在页面布局不是很复杂的情况下，可以让表格的高度自适应，让整个页面正好是视窗高度，这样就只会存在一个滚动条了。

如何做到自适应？
`height: calc(100vh - 530px);`

# 二、防止出现滚动条时页面出现偏移
由于浏览器滚动条默认是overflow:auto类型的，所以页面高度小于视窗时没有滚动条；超过才会出现滚动条。所以当页面从上至下渲染时，页面的滚动条会有一个从无到有的过程，但是滚动条也会占据宽度，这就会导致页面偏移一个滚动条的宽度。
使用如下css就能解决这个问题：
```css
html {
    overflow-y: scroll;
    margin-right: calc(100vw - 100%);
    margin-left: 0;
}
```
原理也很简单，vh和vm都是视窗高度，相当于window.innerWidth，它是包括滚动条的，减去100%就是滚动条宽度了。

**注意：Safari和iOS Safari (包括6和7) 不支持视窗单位 (vw, vh) 在 calc()中使用。**

参考阅读：
[Fix 'jumping scrollbar' issue using only CSS][1]


  [1]: https://aykevl.nl/2014/09/fix-jumping-scrollbar
