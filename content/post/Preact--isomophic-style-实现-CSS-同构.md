---
title: "Preact  isomophic style 实现 CSS 同构"
categories: ["前端"]
tags: ["SSR","preact","isomophic"]
draft: false
slug: "688"
date: "2019-10-27 16:15:00"
---

![logo.svg][1]

实现SSR不仅需要输出HTML字符串，还要包括关键渲染路径的CSS插入到HTML中，如果样式需要浏览器加载完 CSS 后才会加上，这个样式添加的过程就会造成页面的闪动，SSR也就没有意义了。

在服务端我们不能使用`style-loader`，因为它只能在浏览器环境将CSS插入到页面中，我们采用`isomophic style load`在服务器端输出 html 字符串的同时，也将页面用到的样式抠出来，然后插入到 html 字符串当中，将结果一同传送到客户端。

下面是webpack中用到了loader部分。
```js
    var insertCss = require(${stringifyRequest(this, `!${insertCss}`)});
    var content = typeof css === 'string' ? [[module.id, css, '']] : css;
    
    exports = module.exports = css.locals || {};
    exports._insertCss = function() { return insertCss(css) };
    exports._toString = css.toString;
    exports._module_id = id
```

我们实现同构 css 函数 `insertCss`，这里只展示服务端部分，函数接收style参数，这是`css-loader`生成的style对象，包括
```js
  const [module, css, media, sourceMap] = style[0]

  // Generate Id based on length of css , because css module give different id between browser and node
  const dev = process.env.NODE_ENV === 'development'
  const id = dev ? module : style.locals._module_id

  // Server Side
  if (typeof window === 'undefined' || typeof document === 'undefined') {
    return {id, css}
  }
```


最后我们在服务端实现一个insertCss函数收集用到的style，然后使用`<StyleContext.Provider value={insertCss}>`将插入该函数注入。
```js
const isomorphicStyle = new Map();
const insertCss = (styles) => styles.forEach(style => {
    const {id,css} = style._insertCss();
    isomorphicStyle.set(id,css)
});
```

我们使用 `withStyles` 方法装饰组件，使用`useContext`拿到服务端中的`insertCss`函数，然后调用该函数将页面导入的style 对象传进去，该函数返回移除样式的方法`removeCss` ，我们在`componentWillUnmount` 是移除CSS。
```js
constructor(props) {
      super(props)
      const insertCss = useContext(StyleContext)
      this.removeCss = insertCss(styles)
    }

    componentWillUnmount() {
      if (this.removeCss) setTimeout(this.removeCss, 0)
    }
```

###项目地址
https://github.com/zhangchen915/preact-isomorphic-style-loader
安装 `npm i preact-isomorphic-style-loader`



  [1]: https://zhangchen915.com/usr/uploads/2019/10/1713576898.svg
