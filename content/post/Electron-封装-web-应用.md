---
title: "Electron 封装 web 应用"
categories: ["前端"]
tags: []
draft: false
slug: "879"
date: "2020-12-09 11:08:00"
---

![articleocw-5783f68fee8ae.png](https://zhangchen915.com/usr/uploads/2020/12/1159195420.png)

### webview 标签
使用 webview 标签，首先需要开启权限。

```js
webPreferences: {webviewTag: true, nodeIntegration: true, webSecurity: false}
```
webview 常用属性
- nodeintegration 整合node，并且拥有可以使用系统底层的资源，例如 require 和 process
- disablewebsecurity 禁用web安全机制

```html
<webview id="webview" src="https://google.com/"
         disablewebsecurity style="display:flex; width:100%; height:100vh"/>
```

### 注入脚本
```js
const webview = document.querySelector('#webview')
const preloadFile = 'file://' + require('path').resolve('../static/preload.js');
webview.setAttribute('preload', preloadFile);
```

`preload.js`
将 ipcRenderer 注入到 web 应用全局，这样就可以与 electron 主进程通信了。
```js
const {ipcRenderer} = require('electron')
window.ipcRenderer = ipcRenderer
```

### webpack 配置
preload 文件不会自动打包进去，需要配置 webpack 复制进去。
```js
new CopyWebpackPlugin({
        patterns: [{ from: path.resolve(__dirname, 'src', 'static'), to: 'static' }],
    }),
```

### 参考文章
https://gist.github.com/bbudd/2a246a718b7757584950b4ed98109115
