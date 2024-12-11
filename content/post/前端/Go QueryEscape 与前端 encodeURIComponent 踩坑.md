---
title: "Go QueryEscape 与前端 encodeURIComponent 踩坑"
categories: ["go"]
tags: [""]
draft: false
slug: "QueryEscape-encodeURIComponent"
date: "2024-12-04 18:56:10"
---


encodeURIComponent转义除以下字符之外的所有字符：字母、十进制数字、 '-', '_', '.', '!', '~', '*', ''', '(', ')'

从Go语言的url.QueryEscape实现（具体来说，shouldEscape私有函数）中，转义除字母、十进制数字、'-'、'_'、'之外的所有字符。', '~'.

与JavaScript不同，Go的 QueryEscape 将转义'！', '*', ''', '(', ')'.基本上，Go的版本严格符合RFC-3986，JavaScript比较宽松。

如果希望更严格地遵守RFC 3986（保留！，'、（、）和 *），尽管这些字符没有正式的URI定界用途，可以使用一下函数进行转换：

```js
function fixedEncodeURIComponent (str) {
    return encodeURIComponent(str).replace(/[!'()*]/g, x => `%${x.charCodeAt(0).toString(16).toUpperCase()}`)
}
```

#### 空格处理

rfc2396中空格被编码为%20，W3C的标准中空格可以被替换为+或%20。
QueryEscape编码时，空格被编码为+，而+本身被编码为%2B，PathEscape编码时，空格被编码为20%, 而+则未被编码。

```js
encodeURI('1+ 1@%&()') // 1+%201@%25&()
encodeURIComponent('1+ 1@%&()') // 1%2B%201%40%25%26()
url.QueryEscape("1+ 1@%&()")  //1%2B+1%40%25%26%28%29
url.PathEscape("1+ 1@%&()")   // 1+%201@%25&%28%29
```

所以推荐使用 PathEscape 配合前端 encodeURI + fixedEncodeURIComponent


https://stackoverflow.com/questions/13820280/encode-decode-urls
