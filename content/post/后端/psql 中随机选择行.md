---
title: "PostgreSQL 中随机选择行"
categories: ["SQL"]
tags: ["PostgreSQL"]
draft: false
slug: "postgresql_quick_random_row_selection"
date: "2024-05-31 18:01:45"
---
## 方法一：使用ORDER BY RANDOM()

最简单的方法是使用 `ORDER BY RANDOM()` 语句，它会将结果按照随机值的顺序返回。下面是一个例子：

```sql
SELECT * FROM table_name ORDER BY RANDOM() LIMIT 1;
```

上述语句将从 `table_name` 表中随机选择一条记录并返回。

虽然这种方法简单易用，但在处理大型表时性能不佳，因为它需要对表中的每一行进行排序。为了改进性能，我们可以使用其他方法。

## 方法二：使用OFFSET和FETCH语句

另一种方法是使用 `OFFSET` 和 `FETCH` 语句来选择随机行。我们可以先获取表中的总行数，然后使用 `OFFSET` 和 `FETCH` 来选择一个随机的偏移量。

下面是一个例子：

```sql
WITH rows AS (
  SELECT count(*) AS total_rows FROM table_name
)
SELECT * FROM table_name
OFFSET floor(random() * (SELECT total_rows FROM rows))
LIMIT 1;
```

上述语句首先获取了表中的总行数，并将其存储在 `rows` 表中。然后，使用 `OFFSET` 和 `FETCH` 语句来选择一个随机的偏移量并返回一条记录。

这种方法避免了对整个表进行排序，因此在处理大型表时性能较好。

## 方法三：使用表的主键

如果表中有一个自增的主键，我们可以使用该主键来实现快速随机行选择。

下面是一个例子：

```sql
SELECT * FROM table_name WHERE id = (
  SELECT floor(random() * (SELECT max(id) FROM table_name)) + 1
);
```

上述语句使用主键 `id` 来获取一个随机的行。首先，内部的子查询 `SELECT max(id) FROM table_name` 获取了主键 `id` 的最大值，然后将它与随机数相乘，并将结果向下取整，最后加1来获取一个随机的 `id` 值。然后，将该值用于外部查询条件中，从而返回一条随机的记录。

这种方法简单高效，适用于有自增主键的表。

## 方法四：使用 random()


下面是一个例子：

```sql
SELECT * FROM table_name WHERE random() < 0.01 LIMIT 1;
```

上述语句使用 `random()` 函数生成一个随机数，并将其与一个阈值进行比较。在这个例子中，我们选择了 0.01 作为阈值，意味着每次查询只有大约 1% 的概率返回记录。

这种方法简单方便，但要注意选择适当的阈值来控制查询返回的行数。


## 方法五：抽样

```sql
CREATE EXTENSION tsm_system_rows ;
SELECT * FROM data TABLESAMPLE system_rows(10);
```
system_rows 采样方式将在表中随机选择一个磁盘块，然后从那里依次获取行，也就是说第一行是随机选取的，但接下来的行是连续的。
需要注意的是，`TABLESAMPLE` 仅适用于表的实际扫描，不能直接与 WHERE 子句结合使用。


```sql
SELECT * FROM data tablesample bernoulli(0.0001)
```

BERNOULLI 伯努利采样抽样方式，每一行被选取的概率为 P/100，最终来获取表行的 P% 样本。

SYSTEM抽样方式适合抽样效率优先，针对大数据量，BERNOULLI适合随机性优先的场景
