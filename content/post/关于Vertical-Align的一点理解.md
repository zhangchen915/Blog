---
title: "关于Vertical-Align的一点理解"
categories: ["默认分类"]
tags: []
draft: false
slug: "28"
date: "2015-12-06 16:51:00"
---

Vertical-Align翻译过来就是垂直对齐，对齐方式有很多种，常用的middle，bottom，top还能与文字的上下角标对齐，可见他的能力也很强大，既然对其方法很多那么前提要求也要严格一些，必须是对行内元素或者table-call才能起作用。另外还要注意css操作对元素属性的改变，比如直接display操作，或者间接的float、position：abosolute等都会造成Vertical-Align设置失效。

还是不容易理解，再想想之前没有用Vertical-Align时对文字的垂直居中是怎么实现的，我们是通过设置line-height=heigth来实现的，感觉这两个应该或多或少有些联系吧，确实。我的理解，对于行内元素，都有一个基线，而这两个属性的原理都与这个有关，现在想想之前的失效也很正常了，块元素没有基线所以失效也就理所当然了。

注意，基线（baseline）是字母X的下边缘而不是bottom，基线与底部边缘是存在空隙的，这点在使用时要注意。
