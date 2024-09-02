---
title: "MongoDB 中的 JOIN 等效操作"
categories: ["数据库"]
tags: ["MongoDB"]
draft: false
slug: "852"
date: "2020-10-09 01:05:00"
---

![703023-20161116113917935-437841031.png](https://img.bi-bo.cn/2020/10/2125607148.png)
MongoDB 3.2 介绍了一个新的$lookup操作，这个操作提供了一个类似于LEFT JOIN的操作。

## `$lookup` 聚合操作

```text
{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
}
```

语法类似SQL

```sql
SELECT *, <output array field>
FROM collection
WHERE <output array field> IN (SELECT *
                               FROM <collection to join>
                               WHERE <foreignField>= <collection.localField>);
```
`$lookup`实际上是对引用集合的`$in`查询，其中`$in`的值是要查找的管道中localField值的集合。
如果索引了foreignField，则该查询的复杂性为`O（log（n））`。如果foreignField没有索引，则查询的复杂性为`O（n）`。

## 优化

### `$lookup` `$unwind` 合并
当`$unwind`紧跟在另一个`$lookup`之后，`$unwind`在`$lookup`的`as`字段上运行时，优化器可以将`$unwind`合并到`$lookup`阶段。 这避免了创建大型中间文档。

### `$project` 投影优化
聚合管道可以确定是否仅需要文档中字段的子集即可获得结果。如果是这样，管道将仅使用那些必填字段，从而减少了通过管道的数据量。

### 用小结果集驱动大结果集
类似SQL，将筛选结果小的表首先连接，再去连接结果集比较大的表

