---
title: "iOS Safari 中 Fix 定位元素在滚动时闪烁的解决"
categories: ["前端"]
tags: ["兼容","ios"]
draft: false
slug: "689"
date: "2019-10-25 22:29:00"
---

如果你不得不在滚动时固定元素，则可能是iOS Safari（和其他移动设备）存在问题。元素通常会闪烁，然后消失，直到滚动完全停止为止。

只需通过添加`transform: translate3d(0,0,0);`来强制开启GPU加速。

如果要在固定元素中设置元素的样式，则需要将transform3d hack应用于嵌套元素，以使其不会闪烁/消失。

```css
.Element-header {
  transform: translate3d(0,0,0);
}

.Element-header--fixed {
  top: 0;
  position: fixed;
}
```

原文地址: https://muffinman.io/ios-safari-scroll-position-fixed/

