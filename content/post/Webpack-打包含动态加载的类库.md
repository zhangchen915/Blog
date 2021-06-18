---
title: "Webpack 打包含动态加载的类库"
categories: ["前端"]
tags: []
draft: false
slug: "875"
date: "2020-11-21 18:13:00"
---

原文：https://scarletsky.github.io/2019/02/19/webpack-bundling-libraries-with-dynamic-imports/#%E6%96%B9%E6%A1%88%E4%BA%8C-%E7%94%B1%E7%B1%BB%E5%BA%93%E5%A4%84%E7%90%86

把含有加载依赖的部分分离到另一个文件中。
```js
// runtime.js
module.exports = {
    onLoadDeps: function() {
        return Promise.all([
            import('my-lib/dist/text-encoding'),
            import('my-lib/dist/other-dep.js'),
            import('my-lib/dist/another-dep.js'),
        ]);
    }
}
```
然后修改 webpack 配置：
```js
module.exports = {
    output: { ... },
    module: {
        noParse: /src\/runtime/,
    },
    plugins: [ ... ] 
}
```
这样，webpack 在处理 my-lib.js 的时候会把 runtime.js 加载进来，但不会去解析它。

如果应用层引用了这个类库，那么 webpack 打包应用的时候就会处理类库中的 import()，这样就和应用层平时的动态加载一样了，上面的问题也就解决了。 最后剩下一个问题，那就是在开发环境下，我们也需要测试 runtime.js，但这时候它是 import('my-lib/dist/xxx') 的，这个肯定会报 Error: Cannot find module 的错误。 这时候可以像方案一那样，用 import(importPrefix + '/text-encoding') 的方式来解决，也可以利用 NormalModuleReplacementPlugin 来解决。

```js
// webpack.dev.js
module.exports = {
    // ...
    plugins: [
        new webpack.NormalModuleReplacementPlugin(/my-lib\/dist\/(.*)/, function (resource) {
            resource.request = resource.request.replace(/my-lib\/dist/, '../src/deps')
        }),
    ]
}
```
这个插件可以改变重定向资源，上面的配置是把 my-lib/dist/* 里面的资源都重定向到 ../src/deps。 更多详细的用法，可以参考下官方文档 NormalModuleReplacementPlugin。 注意：这个插件最好只用在开发环境中。

