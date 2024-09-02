---
title: "纯 JS 回到顶部"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "536"
date: "2018-05-05 12:52:00"
---

浏览器中控制滚动条高度的是`scrollTo`方法，它接受两个参数，分别是相对左上角xy轴滚动的像素。`scrollTo(0, 0)`就能回到顶部，但这个方法是直接**跳到**顶部的，所以我们还需要一个动画效果来实现scrollY到0的平滑滚动。

这里我们采用 0-Pi 上的[余弦平方曲线][1]`(1-cos(x))^2`，来模拟贝塞尔曲线的效果。
![屏幕快照 2018-05-05 下午2.12.22.png][2]

`window.scrollY`表示文档在垂直方向已滚动的像素值。所以我们通过scrollY获取滚动条的初始高度，用**1/4**余弦平方曲线计算每一帧的滚动条需要到达的高度。通过`requestAnimationFrame`在30帧后到达顶部。

下面为代码实现：
```js
function scrollTop(duration = 30) {
    let stepCount = 0;
    const initialPosition = scrollY;
    const stepPI = Math.PI / duration;
    requestAnimationFrame(step);

    function step() {
        if (stepCount < duration) {
            requestAnimationFrame(step);
            stepCount++;
            scrollTo(0, initialPosition * 
                        (1 - 0.25 * Math.pow((1 - Math.cos(stepPI * stepCount)), 2)));
        }
    }
}
```

参考文章：
[Animated onclick scroller in pure JS][3]


  [1]: http://www.wolframalpha.com/input/?i=(1-cos(x))%5E2%20from%200%20to%20pi
  [2]: https://img.bi-bo.cn/2018/05/2168690342.png
  [3]: https://juusomikkonen.com/blog/2016/07/24/animated-onclick-scroller-in-pure-js.html
