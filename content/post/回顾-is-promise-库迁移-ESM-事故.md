---
title: "回顾 is-promise 库迁移 ESM 事故"
categories: ["前端"]
tags: []
draft: false
slug: "807"
date: "2020-05-07 01:22:00"
---

is-promise 只做一件事，判断 JavaScript 对象是否为 Promise，但这个包被近百万个项目所依赖。作者在 4月25日发布的新版本并未遵循正确的 ES 模块标准，从而导致更新完成后，所有在构建时使用 is-promise 库的项目几乎全部报错。

这里犯了几个个错误 https://github.com/then/is-promise/blob/8e51d62bf158eb0685cd6109f0137472e8c3cb91/package.json#L7-L10

- 以为 exports 和 main 一样，但实际上需要 `./` 前缀。
- exports 不仅限制你能导入什么，还得限制你怎么导入

### exports 字段
我们都知道 node 模块导出有 main ，所有版本的Node.js都支持，但是能力有限：它仅定义包的主要入口点。

"exports"扩展了 main，而且二者同时存在时，优先使用 exports。

Node.js支持以下条件：

- "import" 通过import或 加载包时匹配import()。可以同时引用ES模块或CommonJS文件， import并且import()可以加载ES模块或CommonJS源。
- "require"通过加载包时匹配require()。由于require()仅支持CommonJS，因此引用的文件必须为CommonJS。
- "node"适用于任何Node.js环境。可以是CommonJS或ES模块文件。这种情况应该总是在"import"或 之后出现"require"。
- "default"是CommonJS或ES模块文件，应始终排在最后。

#### 条件导出
一个package.json文件可以直接定义单独的CommonJS和ES模块入口点：
条件匹配的规则是从上至下，所以应按对象顺序使用从最具体到最不具体的条件。

```json
 "exports": {
    ".": [
      {
        "import": "./index.mjs",
        "require": "./index.js",
        "default": "./index.js"
      },
      "./index.js"
    ]
  },
```

####嵌套条件
```json
"exports": {
    "browser": "./feature-browser.mjs",
    "node": {
      "import": "./feature-node.mjs",
      "require": "./feature-node.cjs"
    }
  }
```

参考文章：
https://github.com/then/is-promise/issues/13
https://medium.com/javascript-in-plain-english/is-promise-post-mortem-cab807f18dcc
