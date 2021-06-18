---
title: "了解BFC"
categories: ["默认分类"]
tags: []
draft: false
slug: "了解BFC"
date: "2015-12-20 18:33:07"
---

什么是BFC
------

BFC(Block Formatting Context)，块级元素上下文。简单讲，它是提供了一个独立布局的环境，就相当于一个抽屉，杂七杂八的盒子都能放里面，而不影响外面的元素。

先看标准是如何规定的：非块级盒子的浮动元素、绝对定位元素及块级容器(比如inline-blocks，table-cells和table-captions)，以及overflow属性是visible之外任意值的块级盒子，都会创建了一个BFC。即当元素CSS属性设置了下列之一时，即可创建一个BFC:

 - float：left|right
 - position：absolute|fixed
 - display: table-cell|table-caption|inline-block
 - overflow: hidden|scroll|auto

BFC布局规则
--------

内部的Box会在垂直方向，一个接一个地放置。
Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
BFC的区域不会与float box重叠。
BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
计算BFC的高度时，浮动元素也参与计算。
