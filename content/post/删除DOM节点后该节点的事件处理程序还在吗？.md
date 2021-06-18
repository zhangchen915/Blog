---
title: "删除DOM节点后该节点的事件处理程序还在吗？"
categories: ["前端"]
tags: []
draft: false
slug: "575"
date: "2018-08-01 21:45:00"
---

一个DOM被删除并不会直接释放对应的event listener，如果删除的DOM元素是无引用的（没有指向它的引用），那么元素本身以及与之关联的任何事件处理程序/侦听器会被由垃圾收集器回收。
```js
var a = document.createElement('div');
var b = document.createElement('p');
// Add event listeners to b etc...
a.appendChild(b);
a.removeChild(b);
b = null; 
// A reference to 'b' no longer exists 
// Therefore the element and any event listeners attached to it are removed.
```

但如果存在仍指向该元素的引用，则该元素及其事件侦听器将保留在内存中。

```js
var a = document.createElement('div');
var b = document.createElement('p'); 
// Add event listeners to b etc...
a.appendChild(b);
a.removeChild(b); 
// A reference to 'b' still exists 
// Therefore the element and any associated event listeners are still retained.
```


