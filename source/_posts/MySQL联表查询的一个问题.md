---
title: MySQL联表查询的一个问题
date: 2018-06-15
tags: 数据库
---



##### 当用join关联另外一个表的时候，mysql默认选择以小表作为驱动表。而left join强制用左表作为驱动表，right join则强制用右表作为驱动表。

建表语句：

```mysql
CREATE TABLE student (
id bigint(3) NOT NULL AUTO_INCREMENT,
name varchar(15) NOT NULL,
class_id bigint(3) NOT NULL,
PRIMARY KEY (id),
KEY idx_class_id (class_id)
);

CREATE TABLE class (
id bigint(3) NOT NULL AUTO_INCREMENT,
name varchar(15) NOT NULL,
PRIMARY KEY (id)
);
```

<!-- more -->

执行SQL和执行结果分析：

```mysql
EXPLAIN SELECT FROM student s JOIN class c ON c.id=s.class_id;
EXPLAIN SELECT FROM student s LEFT JOIN class c ON c.id=s.class_id;

##结果1
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | filtered |
| — | ———– | —– | —- | ————- | ———— | ——- | ———- | —- | ——– |
| 1 | SIMPLE | c | ALL | PRIMARY | ———— | ——- | ———- | 29 | 100 |
| 1 | SIMPLE | s | ref | idx_class_id- | idx_class_id | 8 | test2.c.id | 1 | 100 |

##结果2
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | filtered |
| — | ———– | —– | —— | ————- | ——- | ——- | —————- | —- | ——– |
| 1 | SIMPLE | s | ALL | PRIMARY | — | ——- | —————- | 55 | 100 |
| 1 | SIMPLE | c | eq_ref | PRIMARY | PRIMARY | 8 | test2.s.class_id | 1 | 100 |
```

注意：当表的数据太少或者索引列存在大量相等值时，mysql不走索引。
准确的说，当扫描索引列等于某值的开销加上扫描索引树的开销大于扫描全表的开销时，mysql会不
走索引。