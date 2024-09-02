---
title: " Webview 远程调试"
categories: ["前端"]
tags: ["调试"]
draft: false
slug: "713"
date: "2019-11-14 20:02:00"
---

![](https://img.bi-bo.cn/2019/12/3603197327.png)

### Android 使用 ADB 调试

通过 Homebrew 安装 android-platform-tools
`brew cask install android-platform-tools`
使用connect命令连接设备：
`adb connect <ip>:19129`

然后开启手机上的USB调试功能。
访问`Chrome://inspect`，点击Inspect，弹出开发者工具进行调试。

`adb disconnect` 断开所有连接。

#### 常见问题
- 如果会出现白屏或者 404 错误，这是因为Chrome需要访问 `https://chrome-devtools-frontend.appspot.com`，来选择要使用兼容的 devtools。所以将该域名加入代理即可。
- 如果调试工具界面错乱，则说明调试工具的版本过低（[Chrome 63 后移除了 /deep/ 选择器](https://developers.google.cn/web/updates/2017/10/remove-shadow-piercing?hl=zh-cn)），需要下载一个低版本Chrome，这里推荐下载Chromium。


### IOS
在移动端，启用 `设置 -> Safari 浏览器 -> 高级 -> Web 检查器`，然后使用数据线链接，或下载[Safari Technology Preview] 无线远程调试（因为稳定版 Safari 不支持）。

参考文章：
https://saubcy.com/2019/01/13/chrome-remote-devices-window-breaks/
