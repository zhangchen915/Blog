---
title: "Canvas 与 SVG "
categories: ["前端"]
tags: ["SVG","Canvas"]
draft: false
slug: "83"
date: "2016-01-03 03:12:00"
---

什么是SVG
------

SVG 指可伸缩矢量图形 (Scalable Vector Graphics)，是一种用来描述二维矢量图形的XML 标记语言。**简单地说，SVG面向图形，XHTML面向文本。**所以SVG图像可以无限放大，而且体积更小。注意只有IE9+以上支持。

使用SVG
-----
**1.坐标定位**
 `<svg width="200" height="200" viewBox="0 0 100 100">` 或者直接使用img标签。
使用SVG第一步就是要通过width和height定义画布大小，viewBox属性定义了画布上可以显示的区域：从(0,0)点开始，100宽*100高的区域，实际上就是定义了一个坐标系，这个100相当于200px。

**2.绘制图形**
` <line x1="10" x2="50" y1="110" y2="150" stroke="orange" fill="transparent" stroke-width="5" stroke-linecap="round"/>`
标签名称表示绘制的图形样式，例如：rect（四边形）、circle（圆）、ellipse（椭圆）、polyline（折线）、polygon （多边形）、path（路径）。
**坐标：**x、y表示关键点的坐标（圆形用rx、ry表示圆心）
**颜色：**fill属性设置对象内部的颜色，stroke属性设置绘制对象的线条的颜色。fill-opacity控制填充色的不透明度，属性stroke-opacity控制描边的不透明度。
**描边:**
`stroke-width`属性定义了描边的宽度。注意，描边是以路径为中心线绘制的，可以理解为给路径加一个边框，所以描边不是简单的宽度，而是路径的一圈边框。
`stroke-linecap`属性控制边框终点的形状。取值：
butt用直边结束线段，它是常规做法，线段边界90度垂直于描边的方向、贯穿它的终点。
square的效果差不多，但是会稍微超出实际路径的范围，超出长度等于stroke-width。
round表示边框的终点是圆角，圆角的半径等于stroke-width。
stroke-linejoin属性控制两条描边线段之间的连接方式，取值：miter（直角）、round（圆角）、bevel（平角）。

**3.控制**
用css和js都能控制svg，但是IE不支持css方法，所以我们只用js来控制。
1.对于嵌入在HTML中的svg，都在一个document下所以svg和其他HTMl元素没有区别，使用选择器选择元素后使用setAttribute()加入属性和值即可
2.对于通过src属性引入的svg文件，则需要`contentDocument`属性获得对应的document

      var object = document.getElementById("element");
      var svgDocument = object.contentDocument;

实例：
---

<p data-height="268" data-theme-id="21453" data-slug-hash="VePrOV" data-default-tab="result" data-user="zhangchen915" class='codepen'>See the Pen <a href='http://codepen.io/zhangchen915/pen/VePrOV/'>SVG时钟</a> by zhangchen (<a href='http://codepen.io/zhangchen915'>@zhangchen915</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>


什么是Canvas
---------

`<canvas>` 是 HTML5 新增的元素，可使用JavaScript脚本来绘制图形。例如：画图，合成照片，创建动画甚至实时视频处理与渲染。注意Canvas同样只有IE9+以上支持。

使用Canvas
--------
**1.坐标定位**
`<canvas id="tutorial" width="150" height="150"></canvas>`
和svg不同canvas只能在HTML文档内，而不能像图片一样引入。

**2.访问绘画元素上下文**
canvas初始是空白的，所以js首先需要找到渲染上下文。

    var canvas = document.getElementById('tutorial');
    var ctx = canvas.getContext('2d');

**3.绘制图形**
1.矩形
fillRect(x, y, width, height)绘制一个填充的矩形
strokeRect(x, y, width, height)绘制一个矩形的边框
clearRect(x, y, width, height)清除指定矩形区域，让清除部分完全透明。

2.绘制路径
beginPath()新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
moveTo(x, y)将笔触移动到指定的坐标x以及y上。**注意：第一条路径构造命令必须用moveTo**
lineTo(x, y)绘制一条从当前位置到指定x以及y位置的直线。
closePath()闭合路径之后图形绘制命令又重新指向到上下文中。
stroke()通过线条来绘制图形轮廓。
fill()通过填充路径的内容区域生成实心的图形。

实例：
---

<p data-height="268" data-theme-id="21453" data-slug-hash="bEgLaM" data-default-tab="result" data-user="ge1doot" class='codepen'>See the Pen <a href='http://codepen.io/ge1doot/pen/bEgLaM/'>prometheus II</a> by Gerard Ferrandez (<a href='http://codepen.io/ge1doot'>@ge1doot</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

SVG与Canvas比较
------------
![比较.png][1]

**两种方案如何选择如何选择，下面一个图就够了**

![cancas&svg.png][2]
	


拓展阅读：
[MDN：SVG教程][3]
[SVG应用指南][4]
[JavaScript操作SVG的一些知识][5]
[Canvas的基本用法][6]
[SVG 与 Canvas：如何选择][7]


  [1]: http://www.zhangchen915.com/usr/uploads/2016/01/3901947819.png
  [2]: http://www.zhangchen915.com/usr/uploads/2016/01/1430902699.png
  [3]: https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial
  [4]: https://svgontheweb.com/zh/
  [5]: http://blog.iderzheng.com/something-about-svg-with-javascript/
  [6]: https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Basic_usage
  [7]: https://msdn.microsoft.com/zh-cn/library/gg193983(v=vs.85).aspx#Using_Canvas_AndOr_SVG
