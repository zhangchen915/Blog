---
title: "WebSocket 与 SSE 性能对比"
categories: ["网络"]
tags: ["WebSocket", "SSE"]
draft: false
slug: "WebSocket-vs-Server-sent-Events"
date: "2022-11-20 19:01:45"
---

# WebSocket 与 SSE 性能对比

在Timeplus，我们正在构建一个流媒体优先的实时分析平台。我们的前端需要在超低延迟下可视化大量的实时数据（你可以在这里试试！）。如果我们想为用户提供实时更新，传统的基于拉取的 API 不能满足我们的需求。这里更好的解决方案是适配推送模型：当服务器端有新数据时，服务器应该将数据推送到前端。这就是我们可以持续即时地向用户提供更新的方式。


提供这种推送模型支持的主要技术是WebSocket (WS) 和服务器发送事件(SSE)。已经有很多很棒的文章比较了这两个选项的特性，这里不再赘述。然而，我们也对这两种技术的性能方面感兴趣。没有多少文章讨论它们的性能，所以我们决定自己找出答案。


以下是我们正在研究的几个主题：

一般哪个更快？

我们如何微调它们？（例如，我们如何在批量大小和延迟之间找到平衡点？吞吐量会随着批量大小的增加而线性增加吗？）

对于一定的吞吐量，每个资源将消耗多少资源？

![](https://static.wixstatic.com/media/b32125_5db3f33da99443a2b459ab55a38d6c24~mv2.png/v1/fill/w_740,h_559,al_c,q_90,usm_0.66_1.00_0.01,enc_auto/b32125_5db3f33da99443a2b459ab55a38d6c24~mv2.png)

在开始测试之前，让我们简单介绍一下我们的用例。

## 我们的用例

在Timeplus，需要推送模型的两个主要前端组件是流式表和实时图表。

### 流式表

流式表需要来自后端服务器的实时原始事件。想象一个用户运行查询来搜索流中的所有原始事件。服务器需要将所有事件流式传输回浏览器，然后浏览器需要将事件追加到表中。这里的数据量可能是巨大的。

```
// Sample query
select * from car_live_data

// Results that streaming to the frontend. One event per row
[1,16.64609333871044,54.12955095924333,39,"73.12",934.1228226766616,0,"c00777","2022-07-13T21:04:08.419Z","2022-07-13T21:04:08.419Z","1970-01-01T00:00:00Z"]
[1,135.15407205751052,67.16106649459257,44,"49.18",923.1921453934982,0,"c00452","2022-07-13T21:04:08.419Z","2022-07-13T21:04:08.419Z","1970-01-01T00:00:00Z"]
[1,-110.6063911072836,18.11142726885563,83,"79.94",1251.1098362542486,0,"c00709","2022-07-13T21:04:08.42Z","2022-07-13T21:04:08.42Z","1970-01-01T00:00:00Z"]
...
```

![](https://static.wixstatic.com/media/867802_f1d35e08b22d4b2f9aba99de51d97567~mv2.png/v1/fill/w_740,h_243,al_c,q_85,usm_0.66_1.00_0.01,enc_auto/867802_f1d35e08b22d4b2f9aba99de51d97567~mv2.png)

### 实时图表

实时图表通常需要聚合数据。一般来说，数据量较小。

```
// Sample query
SELECT window_start, max(speed_kmh) as speed_kmh FROM tumble(car_live_data, INTERVAL 1 s) group by window_start

// Results
["2022-07-13T21:09:40Z",49.45]
["2022-07-13T21:09:41Z",45.64]
["2022-07-13T21:09:42Z",48.02]
...
```

![](https://static.wixstatic.com/media/867802_bb96ba59de264f579afc509ad1c24558~mv2.jpg/v1/fill/w_740,h_310,al_c,q_80,usm_0.66_1.00_0.01,enc_auto/867802_bb96ba59de264f579afc509ad1c24558~mv2.jpg)

理想情况下，我们希望尽可能频繁地渲染组件。然而，为了平衡性能和用户体验，我们决定将渲染间隔限制在 200 毫秒。根据我们的经验，100-500 毫秒的渲染间隔相当不错。

## 测试设置

为了使事情更容易，我们使用以下控制设置，因为它们不是比较的一部分：

**Event payload**
```
[0, "2022-03-29T20:35:56.581Z", 1, 116.9232297, 40.676318, 4745, "c00070", 78, "32.07", "1970-01-01T00:00:00Z", "column 1", "another field"]
```

**连接协议** ：HTTP/2（需要 HTTP/2 来绕过 SSE 的 6 个并发连接限制）
**每批间隔** ：1ms（每秒 1,000 批）
**压缩** ：无
**Chrome** ：版本 103.0.5060.53
**硬件** ： Apple M1 Pro，10 个内核（8 个性能和 2 个效率），32 GB 内存，macOS Monterey (12.4)

## 测试 I：批量大小

该测试的主要目的是比较不同批量大小设置下的不同 EPS 和 CPU 利用率。所有测试的并发数都固定为 10。凭直觉，我们相信我们可以通过更大的批次大小获得更好的吞吐量。我们想知道的是 WS 和 SSE 是否在相同的批量大小下具有相似的性能。

首先，让我们看一下SSE的结果。下图显示，随着批处理大小从 1 增加到 400，EPS 和 CPU 利用率呈线性增加，达到 3,000,000 EPS 或大约 400 MiB/s。当 batch size 达到 400 时，tab 的 CPU 使用率变成 100%，这是测试的瓶颈。在此之后继续增加批量大小将无济于事。

<iframe class="_2xvJT" title="远程内容" allow="fullscreen" allowfullscreen="" tabindex="0" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSjOO_w-lsmSy_MnHQPm7ojIuUOIhnd8UHR4LyMDkfs-9GlBBp_u2hfc6Atj-9KvlqO01hkoiyUP7Xk/pubchart?oid=1521117545&amp;amp;format=interactive" sandbox="allow-popups allow-presentation allow-forms allow-same-origin allow-scripts"></iframe>

接下来我们看一下WS的结果。与 SSE 类似，WS 的 EPS 呈线性增长。 **我们观察到，在相同的 batch size 下，WS 的 CPU 利用率略低于 SSE** 。这意味着 WS 可以更好地利用 CPU；因此，如果其他条件相同， **WS 应该支持比 SSE 更高的吞吐量** 。

<iframe class="_2xvJT" title="远程内容" allow="fullscreen" allowfullscreen="" tabindex="0" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSjOO_w-lsmSy_MnHQPm7ojIuUOIhnd8UHR4LyMDkfs-9GlBBp_u2hfc6Atj-9KvlqO01hkoiyUP7Xk/pubchart?oid=524796111&amp;amp;format=interactive" sandbox="allow-popups allow-presentation allow-forms allow-same-origin allow-scripts"></iframe>

## 测试二：并发

这个测试与之前的测试类似，但现在我们关注并发性如何影响性能。在这部分中，我们将使用 50 作为固定批量大小。

下面的第一张图表显示了 SSE 的结果。同样，EPS 非常线性地增加直到某个点。我们在这里可以获得的最大 EPS 是当并发等于 60 时，此时选项卡的 CPU 使用率变为 100%。

<iframe class="_2xvJT" title="远程内容" allow="fullscreen" allowfullscreen="" tabindex="0" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSjOO_w-lsmSy_MnHQPm7ojIuUOIhnd8UHR4LyMDkfs-9GlBBp_u2hfc6Atj-9KvlqO01hkoiyUP7Xk/pubchart?oid=1221947782&amp;format=interactive" sandbox="allow-popups allow-presentation allow-forms allow-same-origin allow-scripts"></iframe>

对于WS，一开始的结果和SSE差不多。当我们将并发数从 1 增加到 50 时，EPS 几乎与 SSE 相同。但是，我们注意到两件事：

1. 与 SSE 相比，WS 的客户端 CPU 利用率较低
2. 并发数达到 50 后，我们不再看到性能呈线性增长。我们可以通过 WS 获得的最大 EPS 接近但不如 SSE（240 万对 270 万）

<iframe class="_2xvJT" title="远程内容" allow="fullscreen" allowfullscreen="" tabindex="0" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSjOO_w-lsmSy_MnHQPm7ojIuUOIhnd8UHR4LyMDkfs-9GlBBp_u2hfc6Atj-9KvlqO01hkoiyUP7Xk/pubchart?oid=786709766&amp;format=interactive" sandbox="allow-popups allow-presentation allow-forms allow-same-origin allow-scripts"></iframe>

这是 EPS 几乎相同时 WS 和 SSE 之间客户端 CPU 使用率的比较视图。注意到 WS 的 CPU 使用率较低。

<iframe class="_2xvJT" title="远程内容" allow="fullscreen" allowfullscreen="" tabindex="0" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSjOO_w-lsmSy_MnHQPm7ojIuUOIhnd8UHR4LyMDkfs-9GlBBp_u2hfc6Atj-9KvlqO01hkoiyUP7Xk/pubchart?oid=1037346081&amp;amp;format=interactive" sandbox="allow-popups allow-presentation allow-forms allow-same-origin allow-scripts"></iframe>

## 测试 III：真实场景

在测试 I 和 II 中，我们只是试图突破 SSE 和 WS 的极限，看看我们能走多远。实际上，在前端可视化如此大量的数据是不可能的。在本次测试中，我们正在尝试评估更多实际场景。

我们假设我们的延迟目标是 50 毫秒，并且每秒有 100,000 个事件流式传输到前端。

* 每秒批次：20
* 每秒事件数：100,000

### 场景 1：每个连接具有高吞吐量的连接较少

* 连接数：20
* 每个连接每批事件：250

### 场景 2：许多连接每个连接的吞吐量较低

* 连接数**：** 100
* 每个连接每批事件：50


## 其他奇怪知识

我们喜欢分享可能对社区有帮助的知识。因此，虽然与性能测试没有直接关系，但我想我会分享一些可能会为您节省一些时间的其他发现。

#### 只有一个 Chrome 助手进程（Google Chrome 助手）处理所有选项卡的网络。因此，一旦此进度使用 100% CPU，启动新选项卡将无济于事。你

1 标签 3,000,000 EPS

![](https://static.wixstatic.com/media/867802_8b1d445bb3234b3699f78d121b3bc40f~mv2.png/v1/fill/w_740,h_229,al_c,q_85,usm_0.66_1.00_0.01,enc_auto/867802_8b1d445bb3234b3699f78d121b3bc40f~mv2.png)

2 个标签 4,100,000 EPS

![](https://static.wixstatic.com/media/867802_05740a60a95544c4b5559e2c9041199a~mv2.png/v1/fill/w_740,h_211,al_c,q_85,usm_0.66_1.00_0.01,enc_auto/867802_05740a60a95544c4b5559e2c9041199a~mv2.png)

3 个标签 4,000,000 EPS

![](https://static.wixstatic.com/media/867802_0b772576eb7f4915868d777d4cdc6fd2~mv2.png/v1/fill/w_740,h_257,al_c,q_85,usm_0.66_1.00_0.01,enc_auto/867802_0b772576eb7f4915868d777d4cdc6fd2~mv2.png)

有一篇很棒的文章解释了 Chrome 的多进程架构。如果您想了解更多信息，[请在此处查看。](https://www.aosabook.org/en/posa/high-performance-networking-in-chrome.html)

#### Chrome Devtool 可以显着影响性能（CPU 方面）

打开 Devtool 时，CPU 利用率会增加。这是我们的一项测试的结果（批量大小 50，并发性 30，WebSocket）。

在关闭 Devtool 的情况下，EPS 为 1,350,000。该选项卡的 CPU 使用率约为 75%，而网络使用率为 55%

![](https://static.wixstatic.com/media/867802_b470f25ca91446ca9e8ec4c7a5b75f15~mv2.png/v1/fill/w_740,h_146,al_c,q_85,usm_0.66_1.00_0.01,enc_auto/867802_b470f25ca91446ca9e8ec4c7a5b75f15~mv2.png)

启用 Devtool 后，选项卡的 CPU 利用率跃升至 130%（从 75%），浏览器的 CPU 利用率跃升至 100%（从 0%！）。此外，我们注意到 EPS 降至 1,000,000。选项卡的CPU利用率已经成为EPS的瓶颈。

![](https://static.wixstatic.com/media/867802_9cb48cf94fec43ac99cd404a6faa5f83~mv2.png/v1/fill/w_740,h_181,al_c,q_85,usm_0.66_1.00_0.01,enc_auto/867802_9cb48cf94fec43ac99cd404a6faa5f83~mv2.png)

## 结论

让我们回到一开始提出的原始问题：通常哪个更快？

好吧，这个问题的答案可能看起来很无聊——**他们真的足够接近了**。在这些场景中，我们的测试并未显示 SSE 和 WS 之间的性能差异。实际上，大部分 CPU 应该用于解析和渲染数据而不是传输数据。鉴于性能的相对对等，**我们建议用户在选择时关注功能方面的需求**。例如，如果您的应用程序需要双向通信或传输二进制数据，WebSocket 将是您的最佳选择。另一方面，如果您只需要一个简单的协议来将 UTF-8 数据从服务器发送到客户端，SSE 应该适合您的需要。
