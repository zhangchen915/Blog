---
title: "Typescript 中泛型的高级用法"
categories: ["前端"]
tags: ["Typescript"]
draft: false
slug: "715"
date: "2019-11-17 17:08:00"
---

![typescript-generics.png][1]

### 继承类型
extends关键字不仅可以用在类和接口上，还可以用在类型上。
```ts
function myGenericFunction<T extends string>(arg: T): T {
    return arg;
}
```

### 条件类型
```ts
type MyType<T> = T extends string ? boolean : number;
```

### 映射类型
```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
```
他允许我们在T类型集合上进行迭代。

#### 通用映射类型
```ts
// 全键可选
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// 全键必需
type Required<T> = {
    [P in keyof T]-?: T[P];
};

// 全键只读
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```
我们还可以写出更多的通用映射类型，如：

```ts
// 可为空类型
type Nullable<T> {
    [P in keyof T]: T[P] | null
}

// 包装一个类型的属性
type Proxy<T> = {
    get(): T
    set(value: T): void
}
type Proxify<T> = {
    [P in keyof T]: Proxy<T[P]>
}
function proxify(o: T): Proxify<T> {
    // ...
}
let proxyProps = proxify(props)
```

### 元组类型
```ts
var user: [number, string] = [1, "Steve"];
var employee: [number, string][];
employee = [[1, "Steve"], [2, "Bill"], [3, "Jeff"]];
```

### 交叉类型 ( & )和联合类型 ( | )
- 交叉类型是将多个类型合并为一个类型。
- 联合类型，如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员。
```ts
type landAnimal = {
  name: string
  canLiveOnLand: true
}
type waterAnimal = {
  name: string
  canLiveInWater: true
}
// 交叉类型：两栖动物
type amphibian = landAnimal & waterAnimal
let toad: amphibian = {
  name: 'toad',
  canLiveOnLand: true,
  canLiveInWater: true,
}
```


### 全集和空集
- any 类型，泛指一切可能的类型，对应全集。
- never 类型对应空集。任何值，即使是 undefined 或 null 也不能赋值给 never 类型。

### 类型保护与区分类型
####  类型断言
类型断言有两种语法 `<类型>值` 和 `值 as 类型`

#### 使用类型保护
使用类型断言，需要多次判断十分麻烦。所以使用类型保护
这种param is SomeType的形式，就是类型保护，它用来明确一个联合类型变量的具体类型
类型谓词 谓词为 `parameterName is Type`这种形式，parameterName必须是来自于当前函数签名里的一个参数名。

- typeof类型保护只能用于 number, string, boolean, symbol(只有这几种类型会被认为是类型保护)

- instanceof类型保护用于类

**参考文章：**
https://www.freecodecamp.org/news/typescript-curry-ramda-types-f747e99744ab/

  [1]: https://img.bi-bo.cn/2019/11/963792284.png
