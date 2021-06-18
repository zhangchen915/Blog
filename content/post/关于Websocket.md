---
title: "关于Websocket"
categories: ["默认分类"]
tags: []
draft: false
slug: "65"
date: "2015-12-18 15:41:00"
---

什么是WebSocket
------------

WebSocket 规范定义了一种 API，可在网络浏览器和服务器之间建立“套接字”连接。简单地说：客户端和服务器之间存在持久的连接，而且双方都可以随时开始发送数据，而被广泛使用的HTTP协议是非持久连接。假如我们需要构建一个实时聊天的app，如果用非持久的HTTP，那么为了接收最新消息势必要每隔一小段时间都要向服务器发起一次请求，如果在在线人很多，那么服务器将要处理大量HTTP请求。

如何理解WebSocket
-------------
对于服务器来说HTTP通信是被动的，而WebSocket是主动的。HTTP协议在想要获得新消息就必须客户端向服务器发送请求，询问是否有新消息，而使用WebSocket协议时新的消息不用客户端来询问，服务器会主动把新消息发送出去。
就像网购一个东西HTTP就是你想要了解最新信息就要不停上网查物流，而WebSocket就是物流到一个中转站就主动给你发短信。
就是这么简单，但是减少了不少额外沟通成本。

WebSocket API
-------------

WebSocket 也是一个网络协议，所以URL开头也和HTTP类似：`ws:// wss://`

    var wsServer = 'ws://localhost:8888/'; //指定WebSocket服务器地址
    var websocket = new WebSocket(wsServer); //实例化WebSocket
    //以下指定相应的回调函数
    websocket.binaryType = "arraybuffer";
    websocket.onopen = onOpen;       //在开启链接时触发
    websocket.onclose = onClose;     //在关闭连接时触发
    websocket.onmessage = onMessage; //客户端收到消息时触发
    websocket.onerror = onError;     //再发生错误时触发
    websocket.send('you message');   //向服务器发送数据（字符串或二进制数据）
    
    // 以缓冲数组（ArrayBuffer）形式发送画布信息
    var img = canvas_context.getImageData(0, 0, 400, 320);
    var binary = new Uint8Array(img.data.length);
    for (var i = 0; i < img.data.length; i++) {
      binary[i] = img.data[i];
    }
    websocket.send(binary.buffer);
    
    // 以二进制发送文件
    var file = document.querySelector('input[type="file"]').files[0];
    websocket.send(file);
    myWebSocket.close(); //关闭连接


**对比**
------

**例子来自：[websocket.org][1]**
用例1: 每秒钟1000个客户询:网络吞吐量(871 x 1000)= 871000字节= 6968000位/秒(6.6 Mbps)
用例2: 每秒钟10000个客户询:网络吞吐量(871 x 10000)= 8710000字节= 69680000位/秒(66 Mbps)
用例3: 100000客户端轮询每1秒:网络吞吐量(871 x 100000)= 87100000字节= 696800000位/秒(665 Mbps)

这是一个巨大的不必要的网络吞吐量! 下面用HTML5 Web Sockets 重构app后的结果。

用例1: 每秒1000客户收到消息:网络吞吐量(2 x 1000)= 2000字节= 16000位/秒(0.015 Mbps)
用例2: 每秒10000客户收到消息:网络吞吐量(2 x 10000)= 20000字节= 160000位/秒(0.153 Mbps)
用例3: 每秒100000客户收到消息:网络吞吐量(2 x 100000)= 200000字节= 1600000位/秒(1.526 Mbps)

好处不言而喻，websocket是未来的趋势，特别是对于大型时效性应用。

**缺点**
------

缺点也很明显，新的东西必然兼容性不好，有的浏览器不兼容可以用第三方库，有的代理服务器不能兼容websocket，而且还需要服务器支持，那只能更换其他办法，比如轮询、长轮询、流等。**所以对于一般应用我们没有必要使用websocket，带来的兼容问题可能远大于它来的好处。**
当然，随着时代的发展，这些兼容问题总会被解决的。

扩展阅读：

[WebSocket 是什么原理？为什么可以实现持久连接？][2]
[开了N个知乎窗口，标题都有（1 条消息），点开其中一个窗口的消息提示后，（1 条消息）消失，紧接着其他所有标题都会陆续更新，什么技术？][3]
[WEBSOCKET.ORG][4]
[W3C WebSocket API][5]


  [1]: http://www.websocket.org/quantum.html
  [2]: https://www.zhihu.com/question/20215561
  [3]: https://www.zhihu.com/question/20806765
  [4]: http://www.websocket.org/index.html
  [5]: http://www.w3.org/TR/2012/WD-websockets-20120524/
