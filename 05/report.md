# 数据库概论第五次实习作业

> 原梓轩 2200010825
> 陈润璘 2200010848
> 任子博 2200010626

## 任务一

## 任务二

## 任务三：SQL Hint

在这个任务中，我们分别测试了三种不同的 SQL Hint，分别是 `INDEX_MERGE`, `JOIN_ORDER` 和 `HASH_JOIN`。

### INDEX_MERGE

`INDEX_MERGE` 是一种优化方法，它可以将多个索引的结果集合并起来，从而减少查询的时间。在这个任务中，我们使用了 `INDEX_MERGE` 来优化查询。

在我们的例子中，表 `t1` 中的两个字段 `a` 和 `b` 都有索引，它们的取值范围是[1, 1000]，表中共有 10000 条记录。使用 `INDEX_MERGE` 的查询语句如下：

```mysql
select /*+ INDEX_MERGE(t1 idx_t1_a, idx_t1_b)*/ *
from t1
where a > 500
  and b > 500;
```

在测试中，不使用 `INDEX_MERGE` 和使用 `INDEX_MERGE` 的查询时间分别为 64ms 和 46ms，使用 `explain` 查看他们的查询计划可以发现使用 `INDEX_MERGE` 时，预估需要查询的行数大约是不适用时的一半，并且 `Extra` 中增加了 `Using index condition;`，说明使用了索引条件。

### JOIN_ORDER

使用 `JOIN_ORDER` 可以指定查询的表的连接顺序，从而减少查询的时间。在我们的例子中，我们连接两个分别有 20000，50000 条记录的表 `t1` 和 `t2`，使用 `JOIN_ORDER` 的查询语句如下：

```mysql
select /*+ JOIN_ORDER(t1, t2) */ *
from t1
         join t2 on t1.a = t2.a
    and t1.b = t2.b;
```

在测试中，不使用 `JOIN_ORDER` 和使用 `JOIN_ORDER` 的查询时间分别为 1s 931ms 和 1s 107ms，使用 `explain` 查看他们的查询计划可以发现使用 `JOIN_ORDER` 时，连接的顺序发生了变化，记录数较少的表 `t1` 先被连接，这样可以减少连接的次数。

### HASH_JOIN

使用 `HASH_JOIN` 可以指定两个表连接时的连接方式为哈希连接，在测试中，我们使用两个分别有 100，1000 条记录的表 `t3` 和 `t4` 进行连接，使用 `HASH_JOIN` 的查询语句如下：

```mysql
select /*+ HASH_JOIN(t3, t4) */ *
from t3
         join t4 on t3.a = t4.a;
```

在测试中，不使用 `HASH_JOIN` 和使用 `HASH_JOIN` 的查询时间分别为 97ms 和 58ms，使用 `explain` 查看他们的查询计划可以发现使用 `HASH_JOIN` 时，`Extra` 中增加了 `Using join buffer (Hash Join);`，说明使用了哈希连接加快了查询的速度。

## 任务四