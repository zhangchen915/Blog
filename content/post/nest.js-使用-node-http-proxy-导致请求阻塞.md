---
title: "nest.js 使用 node-http-proxy 导致请求阻塞"
categories: ["Node.js","后端"]
tags: ["express","nest"]
draft: false
slug: "596"
date: "2019-04-23 15:38:00"
---

问题的原因是代理中间件与`body-parser`中间件发生了冲突。请求体的解析如果在转发之前，那么后端服务将会收不到请求结束的消息，导致请求一直 pending。

`nest`默认是启用`body-parser`的，需要在初始化时将其关闭。
`const app = await NestFactory.create(AppModule, { bodyParser: false });`

然后需要为不需要代理的请求添加`body-parser`中间件。`body-parser`包括了json，urlencoded和text的解析，根据情况按需引用。
```
const unless = (path, middleware) => (req, res, next) => {
  if (req.path.indexOf(path) !== -1) {
    return next();
  } else {
    return middleware(req, res, next);
  }
};


app.use(unless('/db', json()));
app.use(unless('/db', urlencoded({ extended: false })));
```

