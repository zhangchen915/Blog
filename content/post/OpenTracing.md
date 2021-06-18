---
title: "OpenTracing"
categories: ["Node.js","后端"]
tags: []
draft: false
slug: "874"
date: "2020-11-20 01:09:00"
---

- Logging：用于记录离散的事件，包含程序执行到某一点或某一阶段的详细信息。
- Metrics：可聚合的数据，且通常是固定类型的时序数据，包括 Counter、Gauge、Histogram 等。
- Tracing：记录单个请求的处理流程，其中包括服务调用和处理时长等信息。

#### OpenTracing 数据模型
大多数分布式追踪系统的思想模型都来自Google's Dapper论文，OpenTracing也使用相似的术语。有几个基本概念我们需要提前了解清楚：

Trace(追踪) ：在广义上，一个trace代表了一个事务或者流程在（分布式）系统中的执行过程。在OpenTracing标准中，trace是多个span组成的一个有向无环图（DAG），每一个span代表trace中被命名并计时的连续性的执行片段。

Span(跨度) ：一个span代表系统中具有开始时间和执行时长的逻辑运行单元，即应用中的一个逻辑操作。span之间通过嵌套或者顺序排列建立逻辑因果关系。一个span可以被理解为一次方法调用，一个程序块的调用，或者一次RPC/数据库访问，只要是一个具有完整时间周期的程序访问，都可以被认为是一个span。

Logs ：每个span可以进行多次Logs操作，每一次Logs操作，都需要一个带时间戳的时间名称，以及可选的任意大小的存储结构。

Tags ：每个span可以有多个键值对（key ：value）形式的Tags，Tags是没有时间戳的，支持简单的对span进行注解和补充。

SpanContext ：SpanContext更像是一个“概念”，而不是通用 OpenTracing 层的有用功能。在创建Span、向传输协议Inject（注入）和从传输协议中Extract（提取）调用链信息时，SpanContext发挥着重要作用。

### async_hooks

Async hook 对每一个函数（不论异步还是同步）提供了一个 Async scope，你可以通过 async_hooks.currentId() 获取当前函数的 Async ID。


const async_hooks = require('async_hooks');

console.log('default Async Id', async_hooks.currentId()); // 1

process.nextTick(() => {
  console.log('nextTick Async Id', async_hooks.currentId()); // 5
  test();
});

function test () {
  console.log('nextTick Async Id', async_hooks.currentId()); // 5
}
在同一个 Async scope 中，你会拿到相同的 Async ID。

### 参考文章
https://wu-sheng.gitbooks.io/opentracing-io/content/
https://github.com/opentracing/opentracing-javascript
https://www.cnblogs.com/rossiXYZ/p/13641637.html
