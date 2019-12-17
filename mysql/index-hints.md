# Index Hints

## Index Hints 是什么
index hints 用于指导优化器（optimizer）在查询过程中如何选择索引。index hints 只能用于 SELECT 语句，虽然用在 UPDATE 语句不会解析器（parser）报语法错误，但是会被忽略。

index hints 不同于 optimizer hints，二者可以分开或者一起使用。

## Index Hints 的语法
index hints 跟在 SELECT 语句中的表名後面：
```
tbl_name [[AS] alias] [index_hint_list]
```
其中，index_hint_list 由一个或多个 index_hint 组成。每个 index_hint 的具体语法格式为：
```
USE {INDEX|KEY} [FOR {JOIN|ORDER BY|GROUP BY}] ([index_list])
```
或
```
{IGNORE|FORCE} {INDEX|KEY} [FOR {JOIN|ORDER BY|GROUP BY}] (index_list)
```
每个 index_list 由一个或多个**索引名**组成：
```
index_name [, index_name] ...
```
指定主键用`PRIMARY`。

## 三种 Index Hints
index_hint 有三种，分别是USE、IGNORE、FORCE。三种 index hints 的具体含义如下：
> USE INDEX：告诉 MySQL 只使用 USE INDEX 指定的索引。

> IGNORE INDEX：告诉 optimizer 不要使用指定的索引。如果在 EXPLAIN 发现 MySQL 使用了不当的索引，可以通过 IGNORE 告诉 MySQL 不要使用。

> FORCE INDEX：作用与 USE INDEX 相似，但是它向 MySQL 表明全表扫描代价非常高，尽量使用该索引。此情况下，在 FORCE INDEX 指明的索引无法使用时，仍旧会走全表扫描。



**参考资料：**
1. [MySQL官方文档：Index Hints](https://dev.mysql.com/doc/refman/8.0/en/index-hints.html)

