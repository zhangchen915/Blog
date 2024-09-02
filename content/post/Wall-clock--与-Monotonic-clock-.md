---
title: "Wall clock  与 Monotonic clock "
categories: ["默认分类"]
tags: []
draft: false
slug: "766"
date: "2020-01-07 02:01:00"
---

<img src="https://img.bi-bo.cn/2020/01/4277428354.jpg" alt="reloj_indefinido.jpg" />
现代操作系统通常有两个时间，Wall clock 和 Monotonic clock。

Wall clock 就是我们一般意义上的时间，就像墙上钟所指示的时间。该时钟可能会发生变化。例如，如果它与NTP（网络时间协议）同步。在这种情况下，同步后，我们服务器的本地时钟可以在时间上向后或向前跳。因此，从挂钟开始测量持续时间会产生偏差。

Monotonic clock 字面意思是单调时间，实际上它指的是从某个点开始后（比如系统启动以后）流逝的时间，单调时间重要的不是当前值，而是保证时间源严格线性增加，因此对于计算时间差很有用。

```text
Rust: std::time::Instant.

Ruby: Process.clock_gettime(Process::CLOCK_MONOTONIC). Or I wrote monotime as a nicer alternative.

Python: time.monotonic().

PHP: hrtime as of 7.3.

C: on POSIXy platforms, clock_gettime() with CLOCK_MONOTONIC. On Windows, there's QueryPerformanceCounter.

C++: std::chrono::steady_clock, though be wary about it on older Visual Studios.

Java: System.nanoTime() should be monotonic on most platforms.
````
