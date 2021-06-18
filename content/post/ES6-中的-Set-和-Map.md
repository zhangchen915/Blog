---
title: "ES6 中的 Set 和 Map"
categories: ["前端"]
tags: ["javascript","ES6"]
draft: false
slug: "ES6-中的-Set-和-Map"
date: "2016-06-27 16:53:00"
---

一、Set
---
set类似于数组，但是成员的值都是唯一的，没有重复的值。在向Set加入值的时候，不会发生类型转换。
为了与Map结构保持一致，Set结构也有keys和entries方法，这时每个值的键名就是键值。

**属性:**
Set.prototype.constructor：构造函数，默认就是Set函数。
Set.prototype.size：返回Set的成员总数。

**方法:**
add(value)：添加某个值，返回Set结构本身。
delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
has(value)：返回一个布尔值，表示该值是否为Set的成员。
clear()：清除所有成员，没有返回值。

    var s = new Set();
    
    [2,3,5,4,5,2,2].map(x => s.add(x))
    
    for (i of s) {console.log(i)}
    // 2 3 4 5
二、Map
---
Map 类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object结构提供了“字符串—值”的对应，Map结构提供了“值—值”的对应。

**属性和方法**
size：返回成员总数。
set(key, value)：设置key所对应的键值，然后返回整个Map结构。如果key已经有值，则键值会被更新，否则就新生成该键。
get(key)：读取key对应的键值，如果找不到key，返回undefined。
has(key)：返回一个布尔值，表示某个键是否在Map数据结构中。
delete(key)：删除某个键，返回true。如果删除失败，返回false。
clear()：清除所有成员，没有返回值。

**遍历**
keys()：返回键名的遍历器。
values()：返回键值的遍历器。
entries()：返回所有成员的遍历器。

    let map0 = new Map()
      .set(1, 'a')
      .set(2, 'b')
      .set(3, 'c');
    
    let map1 = new Map(
      [...map0].filter(([k, v]) => k < 3) 
    );
    // 产生Map结构 {1 => 'a', 2 => 'b'}
    
    let map2 = new Map(
      [...map0].map(([k, v]) => [k * 2, '_' + v])
        );
    // 产生Map结构 {2 => '_a', 4 => '_b', 6 => '_c'}

三、应用场景
----

> 当你的元素或者键值有可能不是字符串时，无条件地使用Map和Set。 有移除操作的需求时，使用Map和Set。
> 当仅需要一个不可重复的集合时，使用Set优先于普通对象，而不要使用{foo: true}这样的对象。
> 当需要遍历功能时，使用Map和Set，因为其可以简单地使用for..of进行遍历。
> 因此，事实上仅有一种情况我们会使用普通的对象，即使用普通对象来表达一个仅有增量Map，且这个Map的键值是字符串。

**注：**文章摘自 [ES6入门][1] [使用ES6进行开发的思考][2]


  [1]: https://wohugb.gitbooks.io/ecmascript-6/content/docs/set-map.html
  [2]: http://efe.baidu.com/blog/es6-develop-overview/
