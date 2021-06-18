---
title: "exports 和 module.exports 的区别"
categories: ["Node.js"]
tags: []
draft: false
slug: "exports-和-module-exports-的区别"
date: "2016-06-28 17:04:00"
---

1.`exports` 是指向的 `module.exports` 的引用
2.module.exports 初始值为一个空对象 {}，所以 `exports` 初始值也是 {}
3.require() 返回的是 `module.exports` 而不是 `exports`


module.exports = somethings 是对 module.exports 进行了覆盖，所以 module.exports 指向了新的内存，而 exports 还是指向原来的内存块，为了让 module.exports 和 exports 还是指向同一块内存或者说指向同一个 “对象”，可以这样写： `exports = module.exports = somethings`
因为node.js的require导出的，永远是module，所以在实际应用中推荐只使用`module.exports`。

拓展阅读：
[Global对象][1]
[exports 和 module.exports 的区别][2]


  [1]: https://yjhjstz.gitbooks.io/deep-into-node/content/chapter12/chapter12-1.html
  [2]: https://cnodejs.org/topic/5231a630101e574521e45ef8
