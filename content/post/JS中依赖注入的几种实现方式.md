---
title: "JS中依赖注入的几种实现方式"
categories: ["前端"]
tags: ["设计模式"]
draft: false
slug: "576"
date: "2018-08-03 14:50:00"
---

![Di.jpg][1]
## 依赖注入与控制反转
- 控制反转（Inversion of Control）是一种思想
- 依赖注入（Dependency injection）是一种设计模式

## 依赖注入的实现
其实依赖注入原理很简单，就两点：
1. 实现一个服务池
2. 解析依赖项，并将对应的服务传入需要它的函数

服务池简单，可以直接用对象模拟。
```js
   class Injector {
        constructor() {
            this.dependencies = {};
        }

        register(key, value) {
            this.dependencies[key] = value;
        }
    }

   const service = function () {
        return {name: 'Service'};
    }
    const router = function () {
        return {name: 'Router'};
    }

    const injector = new Injector();

    injector.register('service', service)
    injector.register('router', router)
```
关键是解析函数的依赖，不同的库有不同的方式，但原理大同小异。

###直接声明依赖
先描述依赖，然后再写函数就是RequireJS / AMD的思想。
这种方法让解析函数的依赖非常简单。
```js
    class Injector {
        constructor() {
            this.dependencies = {};
        }

        register(key, value) {
            this.dependencies[key] = value;
        }
        resolve(deps, func, scope = {}) {
            for (const d of deps) {
                if (this.dependencies[d]) {
                    scope[d] = this.dependencies[d];
                } else {
                    throw new Error('Can\'t resolve ' + d);
                }
            }

            return function () {
                func.apply(scope, Array.from(arguments))
            };
        }
    }
```
结果：
```js
    var doSomething = injector.resolve(['service', 'router'], other => {
        console.log(this.service().name) //Service
        console.log(this.router().name)  //Router
        console.log(other)               //Other
    });
    doSomething("Other");
```
这种方式还需要我们先描述依赖，有没有一种方法能让注入器自动识别要注入那种依赖？

###正则
这种方法就是`Angular.js`获取依赖的方法的方法。
```js
app.controller('MainCtrl', function (service, router) {
    // do something
});
```
`Angular.js`通过这段正则`/^function\s*[^\(]*\(\s*([^\)]*)\)/m`匹配参数，获得函数的依赖项。
我们只需要简单修改一下`resolve`函数。

```js
resolve(func, scope = {}) {
    const deps = func.toString().match(/^function\s*[^\(]*\(\s*([^\)]*)\)/m)[1].replace(/ /g, '').split(',');
    return function () {
        const args = Array.from(arguments);
        deps.forEach((e, i) => {
            if (this.dependencies[e]) args.splice(i, 0, this.dependencies[e]);
        });
        func.apply(scope, args);
    }.bind(this)
}
```
结果：
```js
    var doSomething = injector.resolve(function (service, router, other) {
        console.log(service().name)
        console.log(router().name)
        console.log(other)
    });

    doSomething("Other");
```

但这种方式压缩代码会改变参数名，从而找不到依赖，`Angular.js`采用如下方式避免这个问题：
```js
app.controller('MainCtrl', ['service', 'router', function (service, router, other) { }]);
```
这种方式其实和第一种差不多，具体实现就不再赘述。

###reflect-metadata
在`Typescript`中利用[reflect-metadata][2]库，通过反射机制，获取参数类型列表，来实现依赖注入。
```typescript
import "reflect-metadata";

const classPool: Array<Function> = [];

// 标记可被注入类
export function injectable(constructor: Function) {
    let paramsTypes: Array<Function> = Reflect.getMetadata('design:paramtypes', constructor);
    if (classPool.indexOf(constructor) !== -1) return;
    if (paramsTypes.length) {
        paramsTypes.forEach(v => {
            if (v === constructor) {
                throw new Error('不可以依赖自身');
            } else if (classPool.indexOf(v) === -1) {
                throw new Error(`参数[${(v as any).name}]不可被注入`);
            }
        });
    }
    classPool.push(constructor);
}


export function create<T>(constructor: { new(...args: Array<any>): T }): T {
    let paramsTypes: Array<Function> = Reflect.getMetadata('design:paramtypes', constructor);
    let paramInstances = paramsTypes.map(v => {
        if (classPool.indexOf(v) === -1) throw new Error(`参数[${(v as any).name}]不可被注入`);
        return v.length ? create(v as any) : new (v as any)();  // 参数有依赖项则进行递归
    });
    return new constructor(...paramInstances);
}
```

参考文章：
https://modernweb.com/dependency-injection-in-javascript/
[TypeScript 实现依赖注入][3]


  [1]: https://img.zhangchen915.com/2018/08/3260314313.jpg
  [2]: https://github.com/rbuckton/reflect-metadata
  [3]: https://zhuanlan.zhihu.com/p/22962797?refer=angular2
