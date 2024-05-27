---
title: "Canvas + TS 实现锥形渐变效果"
categories: ["前端"]
tags: ["Canvas","WebGL"]
draft: false
slug: "cache-request"
date: "2024-5-21 17:26:10"
---

### 锥形渐变简介
锥形渐变是一种特殊的颜色渐变方式，其特点是颜色从一个中心点向外辐射，沿着圆周方向平滑过渡。
css 中可以使用 conic-gradient() 创建，但是兼容性不是很好，所以使用 canvas 模拟实现。

### 实现思路
由于Canvas API没有直接提供生成锥形渐变的函数，我们的策略是通过计算不同角度上的颜色分布，然后使用多个线性渐变模拟出整体的锥形渐变效果。具体来说，分为以下几个步骤：

确定渐变中心与半径：首先确定锥形渐变的中心点(cx, cy)和渐变覆盖的半径radius。
划分颜色段：根据所需的颜色数量，计算每个颜色段所占的角度，确保总和为360度。
创建线性渐变：对于每个颜色段，基于该段起始和结束角度计算线性渐变的方向，并应用到相应扇形区域。
填充渐变：使用arc和lineTo方法绘制扇形路径，并应用相应的线性渐变填充。

<iframe height="300" style="width: 100%;" scrolling="no" title="Untitled" src="https://codepen.io/zhangchen915/embed/OJYbNML?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/zhangchen915/pen/OJYbNML">
  Untitled</a> by zhangchen (<a href="https://codepen.io/zhangchen915">@zhangchen915</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>
