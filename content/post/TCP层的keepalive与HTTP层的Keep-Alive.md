---
title: "TCP层的keepalive与HTTP层的Keep-Alive"
categories: ["后端"]
tags: ["网络"]
draft: false
slug: "483"
date: "2017-11-02 12:15:00"
---

##TCP的keepalive

网络两端通过三次握手建立好TCP连接，如果这个连接一直处于闲置状态即没有数据往来，我们就需要在一定时间之后检测对方是否还在。如果不在就需要及时关闭这个连接，避免浪费系统资源，这就是TCP层面的keepalive机制。

我们可以通过以下命令查看内核参数：
```
$ cat /proc/sys/net/ipv4/tcp_keepalive_time
  7200
$ cat /proc/sys/net/ipv4/tcp_keepalive_intvl
  75
$ cat /proc/sys/net/ipv4/tcp_keepalive_probes
  9
```

当tcp发现有tcp_keepalive_time(7200)秒未收到对端数据后，开始以间隔tcp_keepalive_intvl(75)秒的频率发送的空心跳包，如果连续tcp_keepalive_probes(9)次以上未响应，关闭连接。

**Nginx配置tcp keepalive**
```
so_keepalive=on|off|[keepidle]:[keepintvl]:[keepcnt]
```

## HTTP层的Keep-Alive
HTTP层的Keep-Alive表示长连接(persistent connection)。

![HTTP_persistent_connection.png][1]

如上图所示，短链接在发送完一个响应后，会马上关闭相应的tcp连接，而长连接在一个http产生的tcp连接在传送完最后一个响应后，还需要等待keepalive_timeout秒，才开始关闭这个连接。如果这段时间内还有其他请求那就还使用此tcp连接。但是，keep-alive也不能设置过长，长时间的tcp连接容易导致系统资源无效占用。
在HTTP/1.0里，必须在HTTP请求头里显示指定`Connection:keep-alive`，而在HTTP/1.1里，keep-alive是默认开启的。

**Nginx HTTP层的keepalive配置有：**
```
keepalive_timeout timeout [header_timeout];
keepalive_requests number;
```

##总结
HTTP层的keep-alive是为了让tcp活得更久一点，以便在同一个连接上传送多个http，提高socket的效率。而TCP层的keepalive是一种检测TCP连接状况的保鲜机制。当nginx的keepalive_timeout值设置高于tcp_keepalive_time，并且距此TCP连接传输的最后一个HTTP响应，经过了tcp_keepalive_time时间之后，操作系统才会发送侦测包来决定是否要丢弃这个TCP连接。

  [1]: https://zhangchen915.com/usr/uploads/2017/11/3010307666.png
