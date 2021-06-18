---
title: "Jest mock 函数和模块"
categories: ["前端"]
tags: ["Jest"]
draft: false
slug: "801"
date: "2020-04-12 16:41:00"
---

Jset 中有几种创建模拟函数的方法。该jest.fn方法允许我们直接创建一个新的模拟函数。如果要模拟对象方法，则可以使用jest.spyOn。如果要模拟整个模块，可以使用jest.mock。
### mock 函数
Jest 可以捕获对函数的调用（包括调用中传递的参数），也可以直接擦除函数内部的实现，直接返回mock的结果。

```js
function getDouble(val, callback) {
  if(val < 0) {
    return;
  }
  setTimeout(() => {
    callback(val * val);
  }, 100);
};

const mockFn = jest.fn();
getDouble(10, mockFn);

expect(mockFn).not.toHaveBeenCalled()
setTimeout(() => {
  expect(mockFn).toHaveBeenCalledTimes(1);
  expect(mockFn).toHaveBeenCalledWith(20);
}, 110);

```

### mock 模块

```js
import {getParameter, getFilterInfo} from "../service";


jest.mock('../service');
getFilterInfo.mockResolvedValue([model])
getParameter.mockResolvedValue(parameter)
```

### 参考文章
https://www.pluralsight.com/guides/how-does-jest.fn()-work
