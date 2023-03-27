---
title: "nuxt 实现 spa 页面 hash 路由跳转"
categories: ["前端"]
tags: ["nuxt"]
draft: false
slug: "nuxt-js-smooth-scrolling-with-hash-links"
date: "2023-03-23 11:56:10"
---

从 Nuxt.js 版本 1.4.2 开始，当使用元素 ID 作为路由中的哈希链接时，默认滚动行为不会按预期工作（例如：about-us/#john）。

参考：[Nuxt.js 默认滚动行为](https://github.com/nuxt/nuxt.js/blob/bceddf5bcf3dd0010b72d1ec45cbd081a297bd1c/lib/app/router.js#L49-L91)

当直接导航到时，意味着用户立即通过哈希路由进入，浏览器将滚动定位到具有匹配 ID 的元素。 这是预期的行为，并且在完全静态页面的初始页面加载时完美运行。

然而，一旦网站加载完毕，该网站将作为单页应用程序 (SPA) 运行，浏览器将停止响应路由更改，因为这些更改现在由 vue-router 处理。 这允许更快的页面加载和网站内的导航更可控，但浏览器不再处理滚动以关注哈希附加路由中指定的元素 ID，这有可能破坏使用此功能的网站。

解决方案是从 nuxt.config.js 配置对象中覆盖默认的 router.scrollBehavior 方法。

```js
export default async function (to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition
  }

  const findEl = async (hash, x) => {
    return (
      document.querySelector(hash) ||
      new Promise((resolve, reject) => {
        if (x > 50) {
          return resolve()
        }
        setTimeout(() => {
          resolve(findEl(hash, ++x || 1))
        }, 100)
      })
    )
  }

  if (to.hash) {
    const el = await findEl(to.hash)
    if ('scrollBehavior' in document.documentElement.style) {
      return window.scrollTo({ top: el.offsetTop, behavior: 'smooth' })
    } else {
      return window.scrollTo(0, el.offsetTop)
    }
  }

  return { x: 0, y: 0 }
}
```
此配置覆盖解决了两个问题。首先，它将 smooth 应用于 window.scrollTo 方法，让浏览器平滑滚动到指定位置。

其次，它会在几秒钟内多次检查元素是否存在（准确地说是 50 次）。默认滚动行为期望在调用滚动操作时加载内容，但默认 Nuxt 站点加载框架并在从服务器或 CMS 加载完整内容之前开始初始渲染。默认脚本会在第一次未命中后放弃，导致页面始终聚焦在顶部。该脚本在第一次尝试失败后并没有放弃，而是继续每 100 毫秒在 DOM 中搜索预期的元素，持续 5 秒（大约）。理论上有更多的程序化方法来确定内容何时完成加载，但复杂性的成本可能超过此代码未涵盖的边界情况。

```js
const findEl = async (hash, x) => {
return document.querySelector(hash) ||
  new Promise((resolve, reject) => {
    if (x > 50) {
      return resolve()
    }
    setTimeout(() => { resolve(findEl(hash, ++x || 1)) }, 100)
  })
}
```

> 这种方法在两种 Nuxt 模式下都能很好地工作，并且无论 SPA 是否已完成加载都提供统一的用户体验。

https://dev.to/dimer191996/nuxt-js-smooth-scrolling-with-hash-links-94a
