---
title: "如何将 JSON 文件导入到 Node.js 中?"
categories: ["Node.js"]
tags: []
draft: false
slug: "367"
date: "2017-08-01 15:15:00"
---

你需要使用 `fs` 模块进行一些操作。

#### 异步版本
```js
var fs = require('fs');

fs.readFile('/path/to/file.json', 'utf8', function (err, data) {
    if (err) throw err; // we'll not consider error handling for now
    var obj = JSON.parse(data);
});
```

#### 同步版本
```js
var fs = require('fs');
var json = JSON.parse(fs.readFileSync('/path/to/file.json', 'utf8'));
```

### 你想用`require`导入？再考虑一下！

```
var obj = require('path/to/file.json');
```

但是，因为以下几点我并不推荐这种方式：

1. `require` 只会读取**一次**文件，后续调用需要同一个文件将返回缓存副本。如果你想读取一个不断更新的.json文件，这不是一个好主意。你可以使用 hack 技巧，但是在这一点上，使用fs更简单。

2. 如果你的文件没有.json扩展名，`require`不会将该文件的内容视为JSON。

3. `require` 是同步的。如果你有一个非常大的JSON文件，它会阻塞事件循环。你真的需要使用`JSON.parse`与`fs.readFile`。

真的！请用 `JSON.parse`。

### 错误处理/安全

如果您不确定传递给`JSON.parse()`的JSON是否是有效的JSON，请确保在try/catch块中调用`JSON.parse()`。用户提供的JSON字符串可能会使应用程序崩溃，甚至可能导致安全漏洞。如果解析外部提供的JSON，请做好错误处理。

**参考文章：**
[How to parse JSON using Node.js?][1]

  [1]: https://stackoverflow.com/questions/5726729/how-to-parse-json-using-node-js/25710749#comment43497504_9804910
