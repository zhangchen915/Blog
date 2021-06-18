---
title: "迁移到 Electron 5 出现 external &quot;require('url')&quot;:1 错误的解决"
categories: ["前端"]
tags: []
draft: false
slug: "602"
date: "2019-05-16 17:08:00"
---

`nodeIntegration`在以前的电子版本中默认为`true`，但`5.0`版本为了安全性考虑（此[PR][1]）默认情况下关闭了node环境。所以需要手动将其打开。

```
new BrowserWindow({
    webPreferences: {
      nodeIntegration: true,
```


  [1]: https://github.com/electron/electron/pull/16235
