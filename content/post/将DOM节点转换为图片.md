---
title: "将DOM节点转换为图片"
categories: ["前端"]
tags: []
draft: false
slug: "593"
date: "2019-01-16 18:34:00"
---

如何将DOM转换为图片，这个问题可能有点大，一时想不到答案，但我们把问题缩小一下，转换为SVG你可能就想起来了。
SVG能直接嵌入到HTML中，那反过来应该也行。回忆一下，SVG和DOM是不是长得很像？因为他们都是XML的一种方言，所以我们把DOM塞到SVG里面就行了。但是，问题来了，XHTML和SVG都有一个<title>标签，SVG怎么区分他们？其实这就好比变量之于命名空间，XML有各种方言，所以也有命名空间开区分它们。

`<svg xmlns="http://www.w3.org/2000/svg">` 

SVG的开头一般长这个样子，这个`xmlns`就是xml name space的缩写。`http://www.w3.org/2000/svg`就是SVG的命名空间。
HTML的命名空间是`<html xmlns="http://www.w3.org/1999/xhtml">`

在SVG中引入其他命名空间要放在`foreignObject`标签内。

最后我们可以先将SVG转换为canvas，再通过`canvas`的`toDataURL`方法来将其转换为对应格式的bash64编码。

如果你要转换DOM节点的比较复杂，推荐使用`dom-to-image`库来实现。
