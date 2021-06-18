---
title: "Enzyme 中 Shallow, Mount 和 render 的区别"
categories: ["前端"]
tags: []
draft: false
slug: "827"
date: "2020-08-30 15:16:59"
---

原文：https://gist.github.com/fokusferit/e4558d384e4e9cab95d04e5f35d4f913

## Shallow

真正的单元测试 (隔离，子组件不会渲染)

### Simple shallow

Calls:

- constructor
- render

### Shallow + setProps

Calls:

- componentWillReceiveProps
- shouldComponentUpdate
- componentWillUpdate
- render

### Shallow + unmount

Calls:

- componentWillUnmount

### Mount

测试 componentDidMount 和 componentDidUpdate 的唯一方式。会渲染包括子组件在内的所有组件。
需要 DOM (jsdom, domino)。
执行时更稳定。
如果在JSDOM之前包含react，则可能需要一些技巧：

`require('fbjs/lib/ExecutionEnvironment').canUseDOM = true;` 

### Simple mount

Calls:

- constructor
- render
- componentDidMount

### Mount + setProps

Calls:

- componentWillReceiveProps
- shouldComponentUpdate
- componentWillUpdate
- render
- componentDidUpdate

### Mount + unmount

Calls:

- componentWillUnmount

### Render

仅调用render，但渲染所有子组件。

所以我的经验法则是：

- 总是从浅处开始
- 如果应该测试componentDidMount或componentDidUpdate，使用mount
- 如果要测试组件的生命周期和子行为，使用mount
- 如果要以比挂载少的开销测试子级渲染，并且对生命周期方法不感兴趣，使用render


There seems to be a very tiny use case for render. I like it because it seems snappier than requiring jsdom but as @ljharb said, we cannot really test React internals with this.

I wonder if it would be possible to emulate lifecycle methods with the render method just like shallow ?
I would really appreciate if you could give me the use cases you have for render internally or what use cases you have seen in the wild.

I'm also curious to know why shallow does not call componentDidUpdate.

