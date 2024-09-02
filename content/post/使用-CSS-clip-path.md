---
title: "使用 CSS clip-path"
categories: ["前端"]
tags: []
draft: false
slug: "812"
date: "2020-06-14 23:08:00"
---

<img src="https://img.bi-bo.cn/2020/06/1852720414.jpg" alt="images.jpg" /> 
 
 CSS 中的 `clip-path` 属性允许我们指定展示一个元素的特殊区域。注意 `clip`属性已经被废弃。

### 使用剪切路径定义基本形状
使用 clip-path 可以轻松使用 CSS exclusion 模块中的polygon，ellipse，circle或inset函数剪切规则的形状。

##### 多边形
多边形是所有可用形状中最灵活的，因为它允许您指定任意数量的点，有点像SVG路径。提供的点是可以任意单位（例如：基于像素或基于百分比）的X和Y坐标对。因为它是最灵活的，所以它也是最复杂的，你可能需要使用一些工具来定义这些点。

下面是一个简单的例子， `clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%)`：

<img src="https://ww4.sinaimg.cn/bmiddle/67bb661fly1gjlwd6p8fbj20qy0xcwmo.jpg" width="300" height="300" class="polygon" style="clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%);">

你可以使用[Clippy](http://bennettfeely.com/clippy/ "Clippy")在线生成这些坐标。

#### 自定义SVG形状
你还可以定义任何任意的SVG形状以用作剪切路径值。可以使用诸如Sketch之类的工具创建形状，然后将SVG标记复制到文本编辑器中。在SVG标记中，只需将形状包装在`clipPath`元素中，然后将`clipPath`包装在defs块中。

```xml
<svg width="0" height="0">
  <defs>
    <clipPath id="my-shape">
      <path d="M89.6342913,129 C86.6318679,137.611315 85,146.865086 85,156.5 C85,200.767808 119.448105,236.989829 163,239.821749 L163,300 L300,300 L300,163 L251.750745,163 C251.915896,160.855015 252,158.687329 252,156.5 C252,110.384223 214.615777,73 168.5,73 C146.712501,73 126.873981,81.3445721 112.006052,95.0121046 L64.5,0 L0,129 L89.6342881,129 Z">
      </path>
    </clipPath>
  </defs>
</svg>
```

现在，就可以使用url关键字和SVG形状的ID将定义的形状应用为剪切路径值：
```css
.svg-shape {
  -webkit-clip-path: url(#my-shape);
  clip-path: url(#my-shape);
}
```

其实 `clipPath`元素可以包含任何数量的基本形状（如`<rect>`，`<circle>`等），`<path>`或`<text>`。

##### clipPathUnits 属性
clipPathUnits 属性设置SVG坐标系统，值可以是 userSpaceOnUse 或 objectBoundingBox。

- userSpaceOnUse 表示当前用户坐标系
- objectBoundingBox 坐标系的原点位于剪切路径所应用于的元素的边界框的左上角，并且该边界框的宽度和高度相同。我们一般使用这个坐标系统，有点类似于百分比。

我们从矢量绘制工具里导出的通常是用户坐标系统，我们可以在 https://yoksel.github.io/relative-clip-path/ 这里将坐标转换成 objectBoundingBox。


### 剪切路径的参考框
除了剪切路径本身之外，当所应用的剪切路径为时，还可以在属性中定义引用框。也就是说，使用基本形状函数之一创建的剪切路径。因此，仅为用作剪切路径的CSS形状指定参考框，而不为SVG指定。对于SVG ，引用框是HTML元素的边框。`clip-path<basic-shape><clipPath><clipPath>`

因此，为<basic-shape>剪辑路径指定了参考框。如果被修剪的元件是一个HTML元素，所述引用盒可以是四个基本盒模型框之一：margin-box，border-box，padding-box，或content-box。这些都是不言自明的。

如果将<basic-shape>剪辑路径应用于SVG元素，则可以将参考框设置为三个关键字值之一：

- fill-box –使用对象边界框作为参考。
- stroke-box –使用笔划边界框作为参考。
- view-box –如果未viewBox指定，则使用最近的SVG视口作为参考框。如果viewBox确实指定了，则坐标系统将由所指定的原点和尺寸建立viewBox。

### 给裁剪加阴影
box-shadows肯定是不行的，clip-path将其截断。这里我们需要使用CSS filter 的 drop-shadow 函数实现。但是您不能直接在元素上使用它，因为clip-path也会将其切断，所以将其放在父元素上。只是 drop-shadow 的 spread-radius 大部分浏览器都不支持，但也基本满足了一般需求。
`filter: drop-shadow(0 0 1vw black)`

<div style="filter: drop-shadow(0 0 1vw black);"><img src="https://ww4.sinaimg.cn/bmiddle/67bb661fly1gjlwd6p8fbj20qy0xcwmo.jpg" width="300" height="300" class="polygon" style="clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%)">
</div>

### 给裁剪路径加动画
除了svg 路径外都可以直接在keyframes里定义关键帧，就可以实现动画裁剪。

<a style="filter: drop-shadow(0 0 0.75rem black);"><img src="https://ww4.sinaimg.cn/bmiddle/67bb661fly1gjlwd6p8fbj20qy0xcwmo.jpg" width="300" height="300" class="polygon" style="filter: drop-shadow(0 0 0.75rem crimson); clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%); animation: 2s polygon infinite ;">
</a>

<style type="text/css" rel="stylesheet">
@keyframes polygon {
  0% { clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%); }
  100% { clip-path:  polygon(40% 10%, 20% 15%, 28% 45%, 18% 27%, 39% 21%, 60% 20%, 21% 71%, 32% 67%, 62% 35%, 39% 85%); }
}
</style>

### 参考文章
https://alligator.io/css/clipping-with-clip-path/
https://www.sarasoueidan.com/blog/css-svg-clipping/
https://css-tricks.com/using-box-shadows-and-clip-path-together/
https://css-tricks.com/animating-with-clip-path/
