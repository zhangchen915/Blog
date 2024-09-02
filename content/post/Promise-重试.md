---
title: "Promise 重试"
categories: ["前端"]
tags: ["Promise"]
draft: false
slug: "736"
date: "2019-12-07 02:15:00"
---

<img src="https://img.bi-bo.cn/2019/12/4213688885.png" alt="Promise Retry (1).png" />


### Promise cache 链
Promise 常见的用法是 `.then()` 链式调用，其实`.cache()` 也是可以链式调用的。
首先初始化一个`Promise.reject()`  ，之后需要重试几次就后面添加几个cache，如果需要满足一定条件或需要延迟一定时间还可与再继续添加`.then(test).catch(rejectDelay);`


```js
const max = 5;
let p = Promise.reject();
const attempt = () => {
    console.log(`attempt`)
    return Promise.reject('error')
};

for(var i=0; i<max; i++) {
    p = p.catch(attempt);
    // 可以继续追加 then(test).catch(rejectDelay);
}

p.then(res => console.log(res)).catch(e => console.error(e));
```

这种方式需要保证：

- 只有在指定的最大重试次数下才有可能。（链的长度必须有限）
- 建议使用较小的重试次数。（Promise 链消耗的内存与其长度成正比）

如果不满足这几种情况，使用递归才是最好的选择。

### 递归
递归方式比较简洁，本质与Promise cache 链的实现是一样的，区别只是在失败之后才添加下一个catch链。

```js
function retry(fn, retries=3, err=null) {
  if (!retries) return Promise.reject(err);
  return fn().catch(err => {
      return retry(fn, (retries - 1), err,);
    });
}
```

**参考文章：**
[promise-retry-design-patterns](https://stackoverflow.com/questions/38213668/promise-retry-design-patterns)
