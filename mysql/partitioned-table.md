## MySQL中的分区表（Partitioned Table）使用入门

### 概述
MySQL中的分区表可以将一个表中的数据，在物理上分成多个表（即不同分区）进行存储。分区表在MySQL Server层上是一个表，但在引擎层（如InnoDB的）上却是多个不同的物理表，一个分区对应一个物理表。

### 分区方式
分区表的分区方式有多种，常见的有：
- 按某个字段的范围将其划到特定分区（RANGE partitioning）
- 按某个字段的取值将其划到特定分区（LIST partitioning）
- 按某个字段的哈希值将其划到特定分区（HASH partitioning）
- 按多个字段的取值将其划到特定分区（KEY partitioning）

**按某个字段分区后，若表中存在唯一健，则分区字段必须包含在唯一健中**。

### 常用MySQL语法
查看分区情况：

### 参考文档
1. [MySQL 8.0 Reference Manual - Chapter 23 Partitioning](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)
1. [数据库内核月报 － MySQL · 最佳实践 · 分区表基本类型](http://mysql.taobao.org/monthly/2017/11/09/)
1. [简书 － mysql表分区详解](https://www.jianshu.com/p/1cdd3e3c5b3c)

