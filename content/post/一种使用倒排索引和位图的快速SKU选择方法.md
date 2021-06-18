---
title: "一种使用倒排索引和位图的快速SKU选择方法"
categories: ["前端","算法","数据结构"]
tags: []
draft: false
slug: "693"
date: "2019-10-29 02:07:00"
---

使用所有有货的SKU**组合**生成该SKU到该组合的索引。如` [A-B,B-C]` 即生成 `{A:[1],B:[1,2],C:[2]}`。
映射到视图时只需要判断当前选中项的SKU索引的交集（默认为全集），例如选中B，则`[1,2,3]`与`[1,2]`的交集`[1,2]`，然后依次遍历SKU，判断SKU对应的索引与当前选中索引是否有交集。如果有则可选，没有则置灰。

索引可以使用位图数据结构，在判断是否为交集使用位运算提升性能。
```js
   const sku = [];
    const revertIndex = {};
    const size = 8;
    // 随机sku
    for (let i = 0; i < size; i++) {
        sku[i] = new Set();
        for (let j = 0; j < size; j++) {
            const v = j * size + RandomNumBoth(0, size);
            sku[i].add(v)
        }
    }

    for (let i = 0; i < sku.length; i++) {
        sku[i].forEach((e) => {
            if (!revertIndex[e]) revertIndex[e] = 0;
            revertIndex[e] |= 1 << i
        })
    }
```

判断是否选中使用 `(revertIndex[(line-1)*size + e -1] & union)`。

下面是一个简单的例子，取消选择和选中逻辑是一样的，所以就暂时没写：

!!!
<p class="codepen" data-height="300" data-theme-id="21453" data-default-tab="js,result" data-user="zhangchen915" data-slug-hash="poopMdP" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="SKU Demo">
  <span>See the Pen <a href="https://codepen.io/zhangchen915/pen/poopMdP">
  SKU Demo</a> by zhangchen (<a href="https://codepen.io/zhangchen915">@zhangchen915</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
!!!

