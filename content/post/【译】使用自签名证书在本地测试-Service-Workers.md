---
title: "【译】使用自签名证书在本地测试 Service Workers"
categories: ["前端"]
tags: ["Service Workers"]
draft: false
slug: "562"
date: "2018-05-28 10:36:00"
---

Service Workers非常棒。它们为我们提供了强大的功能来拦截和处理网络请求，缓存资源，发送有用的推送通知等等。由于服务工作人员具有拦截网络请求的能力，因此他们必须通过HTTPS运行，以防止被恶意开发者滥用！

Service Workers需要通过HTTPS运行，以确保网页在网络旅程中没有被篡改。如果您在部署到实时服务器之前在本地开发和测试Service Workers代码，则可以在本地主机上无需任何证书即可轻松进行测试。

最近，我一直在开发一个同时使用Service Workers和HTTP/2的Node.js应用程序。HTTP/2与Service Workers需要HTTPS的方式相同，这意味着当我在本地工作时，我需要使用自签名证书。这些自签名证书只是用于测试的伪造证书，所以浏览器会弹出证书错误页面，点击忽略但Service Workers依然不会被注册，控制台中会输出如下错误：`DOMException: Failed to register a ServiceWorker: An SSL certificate error occurred when fetching the script`。这使得本地测试变得更加困难，为了确保我的开发工作流在本地和部署时都是无缝的，我需要解决它！

幸运的是，有一种方法。谷歌的Chrome有一个设置，可以让你覆盖给定的域名。我不建议始终运行此操作，但它对于在本地使用自签名证书进行测试时可能会有所帮助。

```shell
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --user-data-dir=/tmp/foo --ignore-certificate-errors --unsafely-treat-insecure-origin-as-secure=https://localhost:1234
```

上面的命令将打开Goog​​le Chrome的新实例，并将您选择的来源安全地进行安全处理。你也不会再看到任何警告错误，并且你的Service Workers将会注册。

如果你使用Windows计算机，则需要启动命令提示符并替换指向Chrome安装的路径，可能类似于以下内容：

```shell
C:\Program Files (x86)\Google\Chrome\Application\chrome.exe --ignore-certificate-errors --unsafely-treat-insecure-origin-as-secure=https://localhost:1234
```

**原文：**
[Testing Service Workers locally with self signed certificates][1]


  [1]: https://deanhume.com/testing-service-workers-locally-with-self-signed-certificates/
