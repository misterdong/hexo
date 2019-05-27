---
title: mysql中的锁
tags: mysql
date: 2019-05-27 20:54:16
---

#### 读写锁（共享锁，排他锁）
读锁是共享的，或者说是互相不阻塞的。多个用户在同一时刻可以同时读取一个资源，而互不干扰。
写锁是排他的，即一个写锁或阻塞其他的写锁和读锁。

加锁阶段：在该阶段能够进行加锁操作。在对不论什么数据进行读操作之前要申请并获得S锁（共享锁，其他事务能够继续加共享锁，但不能加排它锁），在进行写操作之前要申请并获得X锁（排它锁，其他事务不能再获得不论什么锁）。加锁不成功，则事务进入等待状态，直到加锁成功才继续运行。
解锁阶段：当事务释放了一个封锁以后，事务进入解锁阶段。在该阶段仅仅能进行解锁操作不能再进行加锁操作。
#### 锁粒度
一种提供共享资源并发性的方式就是让锁定对象更具有选择性。
##### 表锁（table lock）
表锁是MySQL中最基本的锁策略，并且是开销最小的策略，它会锁定整张表。一个用户在对表进行写操作（插入、删除、更新等）前，需要先获得写锁，这会阻塞其他用户对该表的所有读写操作。
在特定的场景中，表锁也可能有良好的性能。例如，READ LOCAL表锁支持某些类型的兵法写操作。另外，写锁闭读锁有更高的优先级，因此一个写锁请求可能会被插入到读锁队列到前面（写锁可以插入到锁队列读锁的前面，反之读锁不能插入到锁队列写锁的前面）。
##### 行级锁（row lock）
行级锁可以最大程度的支持并发处理（同时也带来了最大的锁开销）。

#### 行锁，间隙锁，next-key锁
 行锁：record lock，记录锁
 间隙锁（辅助索引时）：以（主键，辅助索引）为间隙点，两个间隙点间的区域进行加锁。
 next-key锁：包含了记录锁和间隙锁，即锁定一个范围，并且锁定记录本身，InnoDB默认的枷锁方式。
 
 表结构：
 ``` sql
CREATE TABLE `tbl_1` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(16) COLLATE utf8_unicode_ci DEFAULT NULL,
  `field` varchar(16) COLLATE utf8_unicode_ci DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `name` (`name`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
如上，id为唯一自增主键，name为普通索引，field字段没有索引

| id(主键) | name(二级索引) |
| --- | --- |
| 1 | 2 |
| 3 | 4 |
| 6| 5 |
| 8| 5 |
| 10|5 |
| 13|11|

 1）InnoDB是通过给索引上的索引项进行加锁来进行锁定的，只有通过索引进行数据的检索，才能使用行锁，否则，将使用表锁。
查询一：
 ``` sql
BEGIN;
SELECT * FROM tbl_1 FOR UPDATE;
```
查询二：
 ``` sql
BEGIN;
SELECT * FROM tbl_1 WHERE field="1" FOR UPDATE;
```
上面查询一和查询二都会进行全表锁定。
2）任何非主键索引上的锁，最终都会追溯到主键上，即对主键索引进行加锁
查询三：
 ``` sql
BEGIN;
SELECT * FROM tbl_1 WHERE name="2" FOR UPDATE;
```
查询四：
 ``` sql
BEGIN;
SELECT * FROM tbl_1 WHERE id="1" FOR UPDATE;
```
上面查询三和查询四都锁定的都是id=1的那条记录。

##### 间隙锁
**间隙锁的目的是为了防止幻读，其主要通过两个方面实现这个目的:**
（1）防止间隙内有新数据被插入
（2）防止已存在的数据，更新成间隙内的数据
**innodb自动使用间隙锁的条件：**
（1）必须在RR级别下
（2）检索条件必须有索引（没有索引的话，mysql会全表扫描，那样会锁定整张表所有的记录，包括不存在的记录，此时其他事务不能修改不能删除不能添加）

**区间的划分方式**
根据name列，我们可以将记录分为以下几个区间，（-∞，2），（2，4），（4，5），（5，11），（11，+∞）。
只要区间内可以插入数据，则可以认为有间隙
**区间的锁定方式**
当锁定name=4的记录时，锁定的区间范围为左边最近的区间加上右边最近的区间，即（2，4），（4，5），与之对应被锁定的记录为{id=1，name=2},{id=3，name=4},{id=6，name=5}。
验证：
``` sql
BEGIN;
SELECT * FROM tbl_1  WHERE `name`='4' FOR UPDATE;
```
``` sql
BEGIN;
INSERT INTO tbl_1 VALUES (2,'3','2');
-- 阻塞
INSERT INTO tbl_1 VALUES (2,'4','2');
-- 阻塞
INSERT INTO tbl_1 VALUES (4,'5','2');
-- 阻塞
INSERT INTO tbl_1 VALUES (4,'6','2');
-- 成功
```