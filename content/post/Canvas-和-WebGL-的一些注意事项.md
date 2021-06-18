---
title: "Canvas 和 WebGL 的一些注意事项"
categories: ["前端"]
tags: ["Canvas","WebGL"]
draft: false
slug: "422"
date: "2017-09-10 13:19:00"
---

##1.canvas元素宽高
canvas元素有两套尺寸：一个是元素本身的大小(通过CSS设置)，另一个是元素绘图表面的大小(通过canvas自身的width和height属性设置)，当没有设置canvas自身宽高时，canvas会默认初始化为 300×150 像素。
注意：通过CSS修改width和height，只是改变了元素本身大小，对元素绘图表面的大小并无影响;而通过修改属性width和height，则会同时改变元素本身大小和绘图表面大小。如果CSS的尺寸与初始画布的比例不一致，画面会出现扭曲。

##2. 渲染上下文
我们要通过`<canvas>`元素的 `getContext()` 方法获取渲染上下文才能绘制。`getContext()`只有一个参数：上下文的格式（二维是“2d”，但三维是“webgl”，没有“3d”的说法）。

##3. 坐标
左上角为原点（0,0），右移：x坐标增加；下移：y坐标增加。
注意：canvas坐标系与笛卡尔坐标系并不一样。
![canvas.png][1]

##4. 清空子路径
`beginPath`清空子路径列表开始一个新路径。如果不清除上次绘制时的子路径，那么之后每次绘制都添加一个子路径，最后会导致帧数急剧下降。

![beginPath.png][2]
```js
ctx.moveTo(10, 10);           ctx.moveTo(10, 10);
ctx.lineTo(90, 10);           ctx.lineTo(90, 10);
ctx.lineWidth = 4;            ctx.lineWidth = 4;
ctx.strokeStyle = "red";      ctx.strokeStyle = "red";
ctx.stroke();                 ctx.stroke();
                              ctx.beginPath();
ctx.moveTo(10, 20);           ctx.moveTo(10, 20);
ctx.lineTo(90, 20);           ctx.lineTo(90, 20);
ctx.lineWidth = 2;            ctx.lineWidth = 2;
ctx.strokeStyle = "blue";     ctx.strokeStyle = "blue";
ctx.stroke();                 ctx.stroke();
```
左侧没有使用`beginPath`清空子路径，第二次就有两条子路径，所以会出现两条蓝色线段。

##五、lineWidth只能设置为1
![lineWidth.png][3]

[这里][4]是一个测试地址，你可以看自己的浏览器是否有同样问题，这个问题在大多数浏览器都存在。
我们应该尽量少用`lineWidth`，为了确保它们在设备上看起来相同，应该使用多边形来模拟。

###[WebGL、Canvas 还是 SVG？][5]
Canvas与WebGL的最主要的区别是：canvas用cpu计算渲染，WebGL用gpu计算渲染。
所以WebGL速度更快功能也更多，但是门槛也很高。

**参考文章：**
[Do I have to have the content.beginPath() and content.closePath()?][6]
[Canvas is stretched when using CSS but normal with “width” / “height” properties][7]
[Line width setting in WebGL has no effect][8]


  [1]: https://zhangchen915.com/usr/uploads/2017/09/842646587.png
  [2]: https://zhangchen915.com/usr/uploads/2017/09/2147679425.png
  [3]: https://zhangchen915.com/usr/uploads/2017/09/1614542632.png
  [4]: http://alteredqualia.com/tmp/webgl-linewidth-test/
  [5]: https://msdn.microsoft.com/zh-cn/library/dn265058%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396
  [6]: http://%20https://stackoverflow.com/questions/22432036/do-i-have-to-have-the-content-beginpath-and-content-closepath
  [7]: https://stackoverflow.com/questions/2588181/canvas-is-stretched-when-using-css-but-normal-with-width-height-properties
  [8]: https://bugs.chromium.org/p/chromium/issues/detail?id=60124
