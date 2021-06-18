---
title: "Polyfill for URL.prototype.searchParams"
categories: ["前端"]
tags: []
draft: false
slug: "695"
date: "2019-11-03 01:12:00"
---

有些机型并没有实现 URL 对象的`searchParams`方法（如 ios 10）。而`url-search-params-polyfill`没有对这部分做兼容。
需要自行添加该方法。

```js
if (window.URL) {
  Object.defineProperty(URL.prototype, 'searchParams', {
    get: function () {
      return new URLSearchParams(this.search)
    }
  })
}
```

https://github.com/jerrybendy/url-search-params-polyfill/issues/12
