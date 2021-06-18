---
title: "如何把 JSON 导入到 TypeScript"
categories: ["前端"]
tags: ["Typescript"]
draft: false
slug: "366"
date: "2017-07-30 22:25:00"
---

在 ES6/ES2015 中你可以使用 import 导入。
`import * as data from './example.json'`

但是在 Typescript 中上面的代码则会报错：
```
Cannot find module 'example.json'
Solution: Using Wildcard Module Name
```
在 Typescript 2.0以上的版本中，我们能在模块名称上使用通配符。 在TS定义文件，例如`typings.d.ts`中, 可以添加以下内容：
```
declare module "*.json" {
    const value: any;
    export default value;
}
```

立竿见影，你的代码就正常工作了！

```
import * as data from './example.json';
const word = (<any>data).name;
console.log(word); // output 'testing'
```
