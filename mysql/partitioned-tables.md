## MySQL中的分区表使用入门

MySQL中的分区表可以将一个表中的数据，在物理上分成多个表（即不同分区）进行存储。分区表在MySQL Server层上是一个表，但在引擎层（如InnoDB的）上却是多个不同的物理表，一个分区对应一个物理表。


### 参考文档
1. [Chapter 23 Partitioning](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)


