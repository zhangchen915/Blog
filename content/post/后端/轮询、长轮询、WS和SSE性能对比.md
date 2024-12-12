---
title: "轮询、长轮询、WS和SSE性能对比"
categories: ["网络"]
tags: ["WebSocket", "SSE"]
draft: false
slug: "polling-vs-Server-sent-Events"
date: "2022-10-01 19:01:45"
---


## 轮询、长轮询、WS和SSE性能对比

论文地址，这里直接上结论
https://www.aliyundrive.com/s/XBakKs1LuT2


### Idle test 

为了能够看到服务空闲和为客户端提供性能之间的区别，测试是在没有连接客户端的服务上进行的。
下图中的图形说明了空闲时每个服务的服务器资源。所有服务是用发送给他们的请求新建的。
服务空闲状态最值得注意的是 Websocket 和其余服务的内存使用差异。一个可能的解释是 Websocket 服务运行于 Node.js ws-module 而其他服务在 Node.js http-module 上运行。除了 Websocket 内存使用之外，服务之间没有显着差异。

![](https://img.bi-bo.cn/2022/11/sse-test.png)


### 客户端性能测试

- 未开启GC时内存使用

![](https://img.bi-bo.cn/2022/11/memory-usage.png)

可以通过额外TCP使用的内存来解释XHR轮询比长轮询使用更多的内存。

可以得出的结论是，XHR和Long轮询比SSE和Websockets使用更多的内存，这在假设中是预期的。


- CPU 使用

![](https://img.bi-bo.cn/2022/11/cpu-usage.png)


### 结论

Websocket 和 SSE 性能最好且二者区别不大，XHR轮询的性能影响取决于轮询速率。对于长轮询，它取决于
服务器生成消息的速度。如果这两个设置设置为相同的值，则XHR和Long轮询非常类似。
