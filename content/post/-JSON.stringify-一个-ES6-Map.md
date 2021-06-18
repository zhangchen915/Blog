---
title: " JSON.stringify 一个 ES6 Map"
categories: ["前端"]
tags: []
draft: false
slug: "819"
date: "2020-07-20 23:26:59"
---

对于没有嵌套的一维Map可以使用解构来实现：
```js
JSON.stringify([...new Map()]);
let map = new Map(JSON.parse(map));
```

其实`JSON.stringify` 和`JSON.parse` 各自都支持第二个参数 replacer 和 reviver 。可以用下面的 replacer 和 reviver 来支持原生 Map 对象，包括嵌套的值。
```js
function replacer(key, value) {
  const originalObject = this[key];
  if(originalObject instanceof Map) {
    return {
      dataType: 'Map',
      value: Array.from(originalObject.entries()), // or with spread: value: [...originalObject]
    };
  } else {
    return value;
  }
}
function reviver(key, value) {
  if(typeof value === 'object' && value !== null) {
    if (value.dataType === 'Map') {
      return new Map(value.value);
    }
  }
  return value;
}
```

使用：
```js
const originalValue = new Map([['a', 1]]);
const str = JSON.stringify(originalValue, replacer);
const newValue = JSON.parse(str, reviver);
console.log(originalValue, newValue);
Deep nesting with combination of Arrays, Objects and Maps

const originalValue = [
  new Map([['a', {
    b: {
      c: new Map([['d', 'text']])
    }
  }]])
];
const str = JSON.stringify(originalValue, replacer);
const newValue = JSON.parse(str, reviver);
console.log(originalValue, newValue);
```

### 参考文章：
https://stackoverflow.com/questions/29085197/how-do-you-json-stringify-an-es6-map/56150320#56150320
