---
title: "Nuxt 实现组件缓存"
categories: ["前端"]
tags: ["Nuxt"]
draft: false
slug: "802"
date: "2020-04-22 00:50:51"
---

### 组件缓存

在`nuxt` 中提供了 `bundleRenderer` 选项，它会作为`vue-server-renderer`中`createRenderer`方法的配置。其中`cache`对象必须`get`和`set`方法。
```js
type RenderCache = {
  get: (key: string, cb?: Function) => string | void;
  set: (key: string, val: string) => void;
  has?: (key: string, cb?: Function) => boolean | void;
};
```
`lru-cache`创建的实例包含了`get`和`set`方法，所以可以直接配置。
```js
export default {
  render: {
    bundleRenderer: {
      cache: require('lru-cache')({
        max: 1000,
        maxAge: 1000 * 60 * 15
      })
    }
  }
}
```

然后可缓存组件必须定义一个唯一`name`选项。如果渲染结果也依赖于`prop`，那么还需要修改`serverCacheKey`的计算方式。如果`serverCacheKey`明确返回`false`，那么就将直接抛弃缓存，重新渲染。

```js
name: 'Date',
serverCacheKey: props => props.item.id,
```

你也可以安装[`@nuxtjs/component-cache`](https://nuxtjs.org/faq/cached-components/)，它也是使用`lru-cache`实现的组件缓存。但使用`lru-cache`在内存中进行缓存的缺点也很明显，开启多进程时每个进程都会缓存一份资源浪费，也有可能出现缓存不一致的情况。

#### 不应缓存的组件
缓存组件必须十分谨慎，下面的组件是不能缓存的：
- 具有可能依赖于全局状态的子组件。
- 有子组件会在渲染上产生副作用context。


### 参考文章
https://gist.github.com/Tuarisa/b7c07289eb32b2a7a77be270e1c8360f

