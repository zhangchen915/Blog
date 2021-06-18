---
title: "Python 与 ES6 句法比较"
categories: ["Python"]
tags: ["javascript","Python"]
draft: false
slug: "300"
date: "2017-05-01 11:21:00"
---

Python syntax here : 2.7 - [online REPL](https://repl.it/languages/Python)

Javascript ES6 via [Babel](http://babeljs.org/) transpilation - [online REPL](https://babeljs.io/repl)

## Imports

```python
import math
print math.log(42)

from math import log
print log(42)

# not a good practice (pollutes local scope) :
from math import *
print log(42)
```

```javascript
import math from 'math';
console.log(math.log(42));

import { log } from 'math';
console.log(log(42));

import * from 'math';
console.log(log(42));
```

## Range

```python
print range(5)
# 0, 1, 2, 3, 4
```

```javascript
console.log(Array.from(new Array(5), (x,i) => i));
// 0, 1, 2, 3, 4
```

## Generators

```python
def foo():
    yield 1
    yield 2
    yield 3
```

```javascript
function *foo() {
    yield 1;
    yield 2;
    yield 3;
}
```

### Lambdas

```python
lambda a: a * 2
```

```javascript
a => a * 2
```

### Destructuring

```python
status, data = getResult()
```

```javascript
var [status, data] = getResult();
```

### Spread

```python
search_db(**parameters)
```

```javascript
searchDb(...parameters);
```

### Iterators

```python
def fibonacci():
    pre, cur = 0, 1
    while True:
        pre, cur = cur, pre + cur
        yield cur

for x in fibonacci():
    if (x > 1000):
        break
    print x,
```

```javascript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}
for (var n of fibonacci) {
  if (n > 1000)
    break;
  console.log(n);
}
```

### Classes

(Python has builtin support for multiple inheritance)

```python
class SpiderMan(Human, SuperHero):
    def __init__(self, age):
        super(SpiderMan, self).__init__(age)
        self.age = age
    def attack(self):
        print 'launch web'
```

```javascript
class SpiderMan extends SuperHero {
    constructor(age) {
        super();
        this.age = age;
    }
    attack() {
        console.log('launch web')
    }
}
```

### Comprehensions

```python
names = [c.name for c in customers if c.admin]
```

(Experimental in Babel)

```javascript
var names = [for (c of customers) if (c.admin) c.name];
```

## What's better in Python

- `help(anything)` ：获得任何 module/method/function 的文档
- 列表推理，类魔法方法！
- 强大的 OOP
- 庞大而一致的标准库，例如：string有38种有用的方法
- 内置 [字符串和数组切片](http://stackoverflow.com/a/509295/174027).

## What's better in Javascript

- 内置 JSON 支持
- NPM 包管理是一个杀手锏：简单快速，甩了 pip+virtualenv 几条街
- 在浏览器中工作 ：）
