---
title: "sass与compass"
categories: ["默认分类"]
tags: []
draft: false
slug: "12"
date: "2015-12-01 20:28:00"
---

之前总是想找到一个能自动完成浏览器兼容的工具，今天终于发现了这个compass工具。
如果你有sass基础，再学会了compass，你的开发效率会有一个极大的提升！
关于compass的使用，官网上讲的非常详细也很简单，可以去看看哦~
而且，有国人开发了一个叫Koala的可视化编译工具，作为小白的我们最喜欢这种了，是不是~

记不住这些命令啊，只好在这里把几个常用命令手打一遍：
创建项目文件：compass create myproject
编译：compass compile
监视文件变化自动编译：compass watch
编译压缩版本：compass compile --output-style compressed

compass分为五个模块
reset：重置默认样式
css3：这里就是完成厂商前缀的模块
layout：布局模块（比如页脚）
typography：版式模块（比如链接、文字等）
utilities：（不属于以上模块的功能）
其中还有特殊的browser support模块是，顾名思义这是浏览器支持模块，其中最常用估计也就这一个了$browser-minimum-versions:('ie','8'),表示对浏览器支持到哪个版本。
其中css3模块不用说肯定是最常用了，去官网看看都有什么吧~


