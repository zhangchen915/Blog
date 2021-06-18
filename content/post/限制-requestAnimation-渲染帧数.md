---
title: "限制 requestAnimation 渲染帧数"
categories: ["前端"]
tags: ["性能优化","动画"]
draft: false
slug: "675"
date: "2019-09-16 23:42:00"
---

当机器性能不足以维持高帧数渲染时，一个较低的稳定的帧数相比不断在60帧上下波动能带来更好的体验。所以在一些动画交互较多的场景，有必要限制渲染帧数。

```js
class AnimationFrame {
  constructor(fps = 60, animate) {
    this.requestID = 0;
    this.fps = fps;
    this.animate = animate;
  }

  start() {
    let then = performance.now();
    const interval = 1000 / this.fps;
    const tolerance = 0.1;

    const animateLoop = (now) => {
      this.requestID = requestAnimationFrame(animateLoop);
      const delta = now - then;

      if (delta >= interval - tolerance) {
        then = now - (delta % interval);
        this.animate(delta);
      }
    };
    this.requestID = requestAnimationFrame(animateLoop);
  }

  stop() {
    cancelAnimationFrame(this.requestID);
  }
}
```

requestAnimationFrame永远以60FPS运行，也就是每16.6ms执行一次，以我们要限制在10FPS（100ms）为例。递归7次时`116.2ms>100ms`，我们再执行下一次渲染。为了避免误差导致整体帧数低于设置，这里不能直接使用`then = now`，而是` then = now - (delta % interval)`。
!!!
<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="js,result" data-user="rishabhp" data-slug-hash="XKpBQX" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Limiting FPS (framerate) with Request Animation Frame">
  <span>See the Pen <a href="https://codepen.io/rishabhp/pen/XKpBQX/">
  Limiting FPS (framerate) with Request Animation Frame</a> by Rishabh (<a href="https://codepen.io/rishabhp">@rishabhp</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
!!!

**参考文章：**
[Controlling the Frame Rate with requestAnimationFrame][1]
[limitLoop.js][2]


  [1]: https://codetheory.in/controlling-the-frame-rate-with-requestanimationframe/
  [2]: https://gist.github.com/addyosmani/5434533
