---
title: "Preload 和 Prefetch的作用区别"
categories: ["前端"]
tags: ["性能优化"]
draft: false
slug: "389"
date: "2017-08-31 17:11:00"
---

## Preload
`preload` 是声明式的 fetch，可以强制浏览器请求资源，同时不阻塞文档 onload 事件。
```html
<!-- preload stylesheet resource via declarative markup -->
<link rel="preload" href="/styles/other.css" as="style">
<!-- or, preload stylesheet resource via JavaScript -->
<script>
    var res = document.createElement("link");
    res.rel = "preload";
    res.as = "style";
    res.href = "styles/other.css";
    document.head.appendChild(res);
</script>
```

`preload`不仅仅能预加载css文件，通过as属性我们还能够加载各种其他资源。
```
<link rel=preload as=video href=...>
<link rel=preload as=fetch crossorigin href=...>
<link rel=preload as=image href=...>
```
注意：CORS的资源的预加载，例如具有`crossorigin`属性的字体或图像，还必须包含一个`crossorigin`属性，以便正确使用该资源。

## Prefetch
`prefetch`用于识别下一次导航可能需要的资源，以及用户代理应该获取的资源，以便在将来请求资源后，用户代理可以提供更快的响应。和`preload`类似，同样可以使用as属性获取各种资源。


## 区别
> 预获取（Prefetch）和预加载（Preload）均声明资源及其提取属性，但在用户代理程序的资源获取方式和时间上有所不同：`prefetch`是对后续导航可能使用的资源的可选且低优先级的提取；`Preload`是对当前导航所必需的资源的强制和高优先级的提取。开发人员应该相应地使用每一个来最小化资源竞争并优化负载性能。

对于当前页面很有必要的资源(如关键的脚本，字体，主要图片)使用 `preload`，对于可能在将来的页面中使用的资源使用 `prefetch`。

*更多细节请阅读W3C官方文档，并留意兼容性。*

**参考阅读：**
[W3C-Preload][1]
[W3C-Prefetch][2]
[Preload, Prefetch And Priorities in Chrome][3]


  [1]: https://w3c.github.io/preload/
  [2]: https://www.w3.org/TR/resource-hints/#prefetch
  [3]: https://medium.com/reloading/preload-prefetch-and-priorities-in-chrome-776165961bbf
