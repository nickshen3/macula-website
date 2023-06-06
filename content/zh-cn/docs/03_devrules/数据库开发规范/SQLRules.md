---
title: "SQL编写规范"
linkTitle: "SQL编写规范"
weight: 2
---

## SQL编写规范

+ 禁止使用select *，只获取必要字段
  > select *会增加cpu/io/内存/带宽的消耗
  > 指定字段能有效利用索引覆盖
  > 指定字段查询，在表结构变更时，能保证对应用程序无影响
+ insert必须指定字段，禁止使用insert into T values()。指定字段插入，在表结构变更时，能保证对应用程序无影响
+ update语句禁止使用别名（update T t SET t.xx = 1），以免出现数据库不兼容问题
+ update语句更新时间，使用CURRENT_TIMESTAMP，不要使用特定数据库方言函数，隐式类型转换会使索引失效，导致全表扫描
+ 禁止在where条件列使用函数或者表达式， 导致不能命中索引，全表扫描
+ 禁止负向查询以及%开头的模糊查询，会导致不能命中索引，全表扫描
+ 禁止大表JOIN和子查询
+ 同一个字段上的OR必须改写成IN，IN的值必须少于50个
+ 不要使用 count(列名)或 count(常量)来替代 count(\*)， count(*)是 SQL92 定义的标准统计行数的语法，跟数据库无关，跟 NULL 和非 NULL 无关。
  > 说明： count(*)会统计值为 NULL 的行，而 count(列名)不会统计此列为 NULL 值的行。count(distinct col) 计算该列除 NULL 之外的不重复行数，注意 count(distinct col1, col2) 如果其中一列全为 NULL，那么即使另一列有不同的值，也返回为 0。当某一列的值全是 NULL 时， count(col)的返回结果为 0，但 sum(col)的返回结果为 NULL，因此使用sum()时需注意NPE问题。

