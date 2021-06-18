---
title: "使用 json-server 搭建 mock 服务"
categories: ["前端"]
tags: ["webpack","mock"]
draft: false
slug: "289"
date: "2017-04-15 15:46:00"
---

前端后端分离的mock服务，这方面相关的工具有很多，比如阿里的[RAP][1]，它提供了很多实用的特性，比如动态生成模拟数据等等，但是这个工具是基于JAVA的，而且更多的是接口文档管理管理功能。而我们需要的是一个轻量级的mock服务，随时可以在本地修改能够方便并且能和webpack整合，所以我们选择了json-server。此外通过faker.js也一样可以实现动态的模拟数据。

`json-server`与`webpack-dev-server`是相互独立的，需要在`webpack-dev-server`中配置proxy，proxy决定将接口转发到`mockserver`还是后端同学的开发机上，这样就可以很方便的自测，而且可以一个一个接口的连调。

json-server 可以通过命令行配置，也可以把它当成 Express 之类框架的中间件使用。
后者可定制性强，但是使用需要使用mone自己实现watch功能，而且需要开发者有node基础，尽管如此我还是建议使用第二种方法配置，因为随着项目复杂度变高，命令行早晚会hold不住。

另外，项目中接口需要通过`router.json`配置，因为`db.json`里的路径不能出现`{"a/b/c":[{}]}`。

```json
//db.json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ]
}

//router.json
{
  "/a/b": "/posts",
  "/a/c": "/comments,
    
}
```
## 遇到的一些问题

### 响应post请求
其实就是把post重写成了get

```js
server.use(function (req, res, next) {
  if (req.method === 'POST') {
    // Converts POST to GET and move payload to query params
    // This way it will make JSON Server that it's GET request
    req.method = 'GET'
    req.query = req.body
  }
  // Continue to JSON Server router
  next()
})
```

### 多个json文件
json-server只支持一个json文件，但是在实际开发中我们希望能够按模块分成多个json，这样更加清晰，也能够防止大家同时修改一个文件导致发生冲突。只要在node中遍历mock文件下的所有的json文件，并用`Object.assign()`把他们合并到一起。注意使用这个方法要注意node版本。

下面是项目中的配置文件，以供参考：
```js
const fs = require('fs');
const path = require('path');
const jsonServer = require('json-server');
const MOCK_DIR = path.resolve(__dirname, 'mock');
const config = require('./build/server.config');
const JSONServer = jsonServer.create();
const middleWares = jsonServer.defaults();
const walk = dir => {
    let results = [];
    let list = fs.readdirSync(dir);
    list.forEach(function (file) {
        file = dir + '/' + file;
        let stat = fs.statSync(file);
        if (stat && stat.isDirectory()) {
            results = results.concat(walk(file));
        }
        else if (path.extname(file) === '.json') {
            results.push(file);
        }
    });
    return results;
};
const files = walk(MOCK_DIR);
let db = {};
files.forEach(function (file) {
    Object.assign(db, require(path.resolve(MOCK_DIR, file)));
});
JSONServer.use(middleWares);
JSONServer.use(jsonServer.rewriter(config.router));

JSONServer.use(jsonServer.bodyParser);
JSONServer.use(function (req, res, next) {
    if (req.method === 'POST') {
        // Converts POST to GET and move payload to query params
        // This way it will make JSON Server that it's GET request
        req.method = 'GET';
        req.query = req.body;
    }
    // Continue to JSON Server router
    next();
});

JSONServer.use(jsonServer.router(db));
JSONServer.listen(config.mockPort, function () {
    console.log(`JSON Server is running on:${config.mockPort}`);
});

```


  [1]: http://rapapi.org/org/index.do
