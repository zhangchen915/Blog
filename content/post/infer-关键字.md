---
title: "infer 关键字"
categories: ["前端"]
tags: ["Typescript"]
draft: false
slug: "889"
date: "2020-12-17 17:18:00"
---

infer 可以在 extends 的条件语句中推断待推断的类型，你可以理解成一个类型方程中的未知数。

- 只能出现在有条件类型的 extends 子语句中；
- 出现 infer 声明，会引入一个待推断的类型变量；
- 推断的类型变量可以在有条件类型的 true 分支中被引用；
- 允许出现多个同类型变量的 infer

### 用例

#### 推断返回值

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

type func = () => number;
type variable = string;
type funcReturnType = ReturnType<func>; // funcReturnType 类型为 number
type varReturnType = ReturnType<variable>; // varReturnType 类型为 string
```

#### 解包
```ts
type Unpacked<T> = T extends (infer R)[] ? R : T;

type idType = Unpacked<Ids>; // idType 类型为 number
type nameType = Unpacked<Names>; // nameType 类型为string
```

T extends (infer R)[] ? R : T的意思是，如果T是某个待推断类型的数组，则返回推断的类型，否则返回T\

#### 推断联合类型
```ts
type Foo<T> = T extends { a: infer U; b: infer U } ? U : never;

type T10 = Foo<{ a: string; b: string }>; // T10类型为 string
type T11 = Foo<{ a: string; b: number }>; // T11类型为 string | number
```

https://jkchao.github.io/typescript-book-chinese/tips/infer.html
https://github.com/LeetCode-OpenSource/hire/blob/master/typescript_zh.md
