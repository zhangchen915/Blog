---
title: "HTML5语义化"
categories: ["默认分类"]
tags: []
draft: false
slug: "15"
date: "2015-12-01 23:02:00"
---

啥叫语义化，就是别全用div，一按F12一片div，头都晕了好吗~

![html5.png][1]

一、header
--------
header，其中一般包括标题，介绍文本，和导航栏。注意不要和head标签混淆。
二、nav
-----
块级元素nav用来表示页面中导航类链接组，比如全局导航，面包屑导航，上一页/下一页。

    <nav>
        <ul>
            <li><a href="#">...</a></li>
            <li><a href="#">...</a></li>
        </ul>
    </nav>

三、article
---------
块级元素article非常类似于div或者section。看看w3c上怎么说的吧：article标签定义外部的内容。外部内容可以是来自一个外部的新闻提供者的一篇新的文章，或者来自 blog 的文本，或者是来自论坛的文本。亦或是来自其他外部源内容。

四、section
---------
section元素表示文档中的一个区域（或节），section元素恰当的用途是将页面分割成有层级的块，元素内部通常会有一个对应层级的标题（h1到h6）。
看看MDN的使用提醒：
1.如果元素内容可以分为几个部分的话，应该使用 <article> 而不是 <section>。
2.不要把 <section> 元素作为一个普通的容器来使用；这种情况是 <div> 的适用范围，特别是当它的目标只是美化样式的情况。 通常来说一个 <section> 应该出现在文档的框架中。

五、aside
-------

还是参考MDN中的介绍：<aside> 元素中连接到页面中其他部分的内容，被认为是独立于该内容的一部分并且可以被单独的拆分出来而不会使整体受影响。其通常表现为侧边栏或者被插入在该内容里。他们通常包含在工具条，例如来自词汇表的定义。也可能有其他类型的信息，例如相关的广告、web 应用程序、个人资料信息，或在博客上的相关链接。

六、footer
--------
与header元素类似，footer用于表示页面的底部。
  [1]: http://www.img.bi-bo.cn/2015/12/2229991287.png
