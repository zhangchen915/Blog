---
title: "【译】Javascript 中的独立 UUID 生成器"
categories: ["前端"]
tags: []
draft: false
slug: "810"
date: "2020-06-13 22:58:00"
---

这是一种在 Javascript 中生成UUID的简便方法（无外部依赖项）。

```js
// Author: Abhishek Dutta, 12 June 2020
// License: CC0 (https://creativecommons.org/choose/zero/)
function uuid() {
  var temp_url = URL.createObjectURL(new Blob());
  var uuid = temp_url.toString();
  URL.revokeObjectURL(temp_url);
  return uuid.substr(uuid.lastIndexOf('/') + 1); // remove prefix (e.g. blob:null/, blob:www.test.com/, ...)
}
```

`URL.createObjectURL()` 静态方法会创建一个唯一的URL，以表示作为参数传递给它的对象。该`uuid()`方法使用此属性`URL.createObjectURL()`来在空对象上生成唯一的URL（这是我到目前为止测试过的所有浏览器中的随机UUID）。以下是此方法生成的一些示例UUID：

```text
for(var i=0; i<10; ++i) { console.log(uuid()); }

f6ca05c0-fad5-46fc-a237-a8e930e7cb49
6a88664e-51e1-48c3-a85e-7bf00467e9e6
e6050f4c-e86d-4081-9376-099bfbef2c30
bde3da3c-b318-4498-8a03-9a773afa84bd
ba0fda03-f806-4c2f-b6f5-1e74a299e603
62b2edc3-b09f-4bf9-8dbf-c4d599479a29
e70c0609-22ad-4493-abcc-0e3445291397
920255b2-1838-497d-bc33-56550842b378
45559c64-971c-4236-9cfc-706048b60e70
4bc4bbb9-1e90-432b-99e8-277b40af92cd
```
注意：`URL.createObjectURL()`目标不是生成随机UUID。因此，上述生成UUID的方法可能会导致我尚未意识到的副作用。

为什么可以这样做，是因为[blob URL](https://www.w3.org/TR/FileAPI/#blob-url)生成UUID是规范的一部分。

### 原文地址：
https://abhishekdutta.org/blog/standalone_uuid_generator_in_javascript.html
