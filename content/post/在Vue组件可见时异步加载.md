---
title: "在Vue组件可见时异步加载"
categories: ["前端"]
tags: ["性能优化","vue"]
draft: false
slug: "622"
date: "2019-08-07 23:29:00"
---

对于单页应用，我们通常用路由来做代码分割，但在一个页面，对于一个首屏不可见的组件或者说需要用户交互才会触发显示的组件，我们还可以用延迟加载组件的方法，来减少首页体积，更细粒度的控制加载内容。vue 提供了 `asyncComponent` 工厂函数，我们可以在这个基础上做可见时异步加载。
```js
const AsyncComponent = () => ({
  // 需要加载的组件 (应该是一个 `Promise` 对象)
  component: import('./MyComponent.vue'),
  // 异步组件加载时使用的组件
  loading: LoadingComponent,
  // 加载失败时使用的组件
  error: ErrorComponent,
  // 展示加载时组件的延时时间。默认值是 200 (毫秒)
  delay: 200,
  // 如果提供了超时时间且组件加载也超时了，
  // 则使用加载失败时使用的组件。默认值是：`Infinity`
  timeout: 3000
})
```
上面为文档上的例子，我们需要在其基础上进行扩展，首先timeout不能指定时间，因为如果组件不可见的话一直不会加载。此外由于组件为异步加载，所以我们不能直接判断组件可见，但是我们可以通过异步组件的`loading`是否出现在视窗内来判断。`component`参数接受一个`Promise`对象，所以这里我们可以直接构造一个promise，然后保存其resolve函数，然后在loading组件挂载后使用intersectionObserver api监听其是否出现在视窗内，在intersectionObserver回调函数中使用保存的resolve函数，将 `component` 的 `pending` 变为 `resolved`状态。

```js
export default function lazyLoadComponent({
  componentFactory,
  loading,
  loadingData,
}) {
  let resolveComponent;

  return () => ({
    component: new Promise((resolve) => {
      resolveComponent = resolve;
    }),
    loading: {
      mounted() {
        const observer = new IntersectionObserver(([entry]) => {
          if (!entry.isIntersecting) return;
          observer.unobserve(this.$el);
          componentFactory().then(resolveComponent);
        });
        observer.observe(this.$el);
      },
      render(h) {
        return h(loading, loadingData);
      },
    },
  });
}
```
## 示例

```js
Foo: lazyLoadComponent({
   componentFactory: () => import(`./components/Foo.vue`),
}),
```

在nuxt中使用，我们需要借助Vue动态组件方式来延迟加载。
```js
<template>
    <div class="post">
        <div style="height: 110vh"></div>
        <div :is="content"></div>
    </div>
</template>

<script>
    export default {
        components: {},
        data() {
            return {content: ''}
        },
        mounted() {
            this.content = this.$loadComponent(() => import(`...`))
        }
    }
</script>
```

## 参考文章
[Lazy Load Vue.js Components When They Become Visible][1]


  [1]: https://markus.oberlehner.net/blog/lazy-load-vue-components-when-they-become-visible/
