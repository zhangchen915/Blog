---
title: "PS—不止切图"
categories: ["默认分类"]
tags: []
draft: false
slug: "76"
date: "2015-12-29 03:00:00"
---

**虽然我认为切图时代终将过去，但是这毕竟是设计师的事，他们给我们设计稿，我们还是要切...**
我们不想切我们可以用工具，好在PS的cc版本也为前端做了好多优化，所以我们也得与时俱进~

**1.文件-脚本-将图层导出到文件（选项全部勾上）**
PS将运行脚本自动将所有图层切图，而且还会保留阴影。


**2.编辑-首选项-增效工具-启用生成器；文件-生成-勾选图像资源**
只要为图层增加后缀（如png、svg等），就会自动生成对应的图片文件。

**3.复制CSS**
复制 CSS 可从形状或文本图层生成级联样式表 (CSS) 属性。CSS 被复制到剪贴板并且可以粘贴到样式表中。

**对于形状，它会捕获以下内容的值：大小、位置、描边、颜色、填充颜色（包括渐变）、投影**
**对于文本图层，复制 CSS 还捕获以下值：字体系列、字体大小、字体粗细、行高、下划线、删除线、上标、下标、文本对齐方式**
从包含形状或文本的图层组复制 CSS 会为每个图层创建一个类以及创建组类。组类表示包含与组中图层对应的子 div 的父 div。子 div 的顶层/左侧值与父 div 有关。
**注意:**“复制 CSS”命令不适用于智能对象或选择未分组的多个形状/文本图层的情况。

**当然复制CSS功能毕竟只是个小工具也有一些不足的地方：1.不支持简写；2.存在多余的定位样式；3.颜色表示不够简洁。所以我们在用的时候千万要再检查一遍，把属性简写，删除冗余代码。**

拓展阅读：
[Photoshop CC 与前端那些事][1]


  [1]: http://isux.tencent.com/ps-photoshop-cc-fd.html
