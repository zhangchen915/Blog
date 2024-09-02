---
title: "最大潜在优先输入延迟（Max Potential First Input Delay）"
categories: ["默认分类"]
tags: []
draft: false
slug: "750"
date: "2019-12-20 01:43:00"
---

<img src="https://img.bi-bo.cn/2019/12/204489382.jpg" alt="maxresdefault.jpg" />

**我们衡量一个页面的首屏，往往只关注了首屏的渲染速度，而忽视了交互维度的评价。**

JavaScript在单线程环境中运行，当一个任务执行时间过长就会阻塞线程，其他所有任务都必须等待。在移动设备上情况更糟。任务可能要花费3-5倍的时间。

## 什么是最大潜在优先输入延迟
让我们定义第一个输入延迟的含义，因为它不包括所有用户交互。

FID可测量诸如单击，按键和在字段中输入文本之类的操作。它不包括滚动或缩放，因为它们可以由浏览器在单独的线程上运行。

### 评分规则

| 最大潜在FID时间（ms）   | 颜色编码|
|----------|:-------------:|
|0–130|  绿色（快速）
|130-250|    橙色（平均）
|超过250| 红色（慢）

## 与TTI的区别
TTI测量页面变为完全交互式所花费的时间。它与FID是比较相似的，但TTI是在没有任何用户在场的情况下进行测量的指标，是一种理想情况下指标，但是并不能真实的反应用户的体验。

## 如何优化
优化TTI的策略同样适用于FID，如果更有效地交付JavaScript以缩短互动时间，那么它也会缩短“首次输入延迟”。

我们与优化的核心思路是合理的加载JS，`仅在必要时加载必要JS`，推迟或删除不必要的JavaScript。最常见的做法是拆分代码并按照优先顺序排列要加载的代码，这样不仅可以缩短页面可交互时间，还可以减少耗时较长的任务。但是我们还可以把粒度做得更细，通常视窗的高度是小于整个页面的高度的，所以我们可以使用懒加载策略，让位于页面下方首屏不可见的组件和图片只在滚动到可见区域是动态加载。

### 延迟
延迟加载了JS，我们还可以考虑将一些延迟进行一些初始化计算。React 的 Fiber 渲染引擎就是在这方面做的优化。

#### 对SSR项目的进一步优化

首先我们看一下SSR页面解析的过程：
<img src="https://img.bi-bo.cn/2019/12/406174717.png" alt="hydrate@2x.png" />
为什么要对SSR页面额外进行关注？因为用户看见页面的时间提前了，但是客户端依然要解析执行JS，如果不做优化的话，可交互时间变化可能不大，所以之两个之间点的跨度反而变大了。

下面是一个实际SSR项目的截图：
<img src="https://img.bi-bo.cn/2019/12/402169385.jpg" />

当然我们可以继续用上述思路优化，但是对于SSR项目我们还可推迟 `hydration` （客户端激活），或者部分 `hydration` （[Partial Hydration](https://github.com/facebook/react/pull/14717)）。

>客户端激活，指的是在浏览器端接管由服务端发送的静态 HTML，使其变为由 框架 管理的动态 DOM 的过程。

例如一些组件只包含文本和图像，但除了呈现内容外没有其他功能。如果这些组件的呈现的HTML输出无论如何都不会发生变化，没有必要加载JavaScript代码并对服务器端呈现的代码进行昂贵的 `hydration`处理。

下面是 vue-lazy-hydration 中选取的一段代码

```js
 const id = requestIdleCallback(() => {
        requestAnimationFrame(() => {
          this.hydrate();
        });
      }, { timeout: this.idleTimeout });
 this.idle = () => cancelIdleCallback(id);
```

原理是提取出 lazy-hydration 部分的标签名，然后当作异步组件的占位符，在满足一定条件后再返回 child（这个条件可以是各种事件，或组件到可视区域，或者浏览器主线程处于空闲时）。 
```js
// vue 源码截取
export function isAsyncPlaceholder (node: VNode): boolean {
  return node.isComment && node.asyncFactory
}

// lazy-hydration render 截取
const child = this.$scopedSlots.default
      ? this.$scopedSlots.default({ hydrated: this.hydrated })
      : this.$slots.default[0];

if (this.hydrated) return child;

const tag = this.$el ? this.$el.tagName : `div`;
const vnode = h(tag);
vnode.asyncFactory = {};
vnode.isComment = true;
```

**参考文章：**
https://premium.wpmudev.org/blog/how-to-improve-google-pagespeed-user-interaction-metrics-in-wordpress/
https://philipwalton.com/articles/idle-until-urgent/
https://markus.oberlehner.net/blog/how-to-drastically-reduce-estimated-input-latency-and-time-to-interactive-of-ssr-vue-applications/
https://addyosmani.com/blog/rehydration/
