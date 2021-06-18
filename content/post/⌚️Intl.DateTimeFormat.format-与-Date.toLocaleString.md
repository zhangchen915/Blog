---
title: "⌚️Intl.DateTimeFormat.format 与 Date.toLocaleString"
categories: ["前端"]
tags: []
draft: false
slug: "636"
date: "2019-08-24 01:04:00"
---

两种方法使用方法基本一致，第一个参数可以接受BCP 47 标准的字符串，或者一个包含区域，时间格式的字符串数组。
第二个参数接受一个格式化的配置项，包含时间样式，时区，格式匹配器。下面是一个格式化utc时间戳的例子。

```js
const DateTimeFormat = new Intl.DateTimeFormat('zh-CN', {
    year: 'numeric', month: 'numeric', day: 'numeric',
    hour: 'numeric', minute: 'numeric', second: 'numeric',
    hour12: false,
});
function formatTime(utc) {
    return DateTimeFormat.format(utc);
}

formatTime(new Date)
// "2019/8/24 02:29:38"
```

`Intl.DateTimeFormat.prototype.format` 不需要每次都设置区域设置和选项，所以它的性能更好，但是`Intl`是ecma402中添加的内容，兼容没有`toLocaleString`好。
