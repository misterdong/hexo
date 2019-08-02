---
title: redis基础入门
date: 2019-07-03 17:48:08
tags: redis
categories: redis
---

redis ,基于key-value的数据存储系统，存储在内存中，读取速度快，支持持久化。

### 基本数据类型

- String （字符串）
- Hash（哈希）
- List（列表）
- Set（集合）
- Sorted-Set（有序集合）

<!-- more -->
### 数据结构

不同的数据类型在redis当中有着不同的存储方式：

#### String

使用int/SDS进行存储，整数类型的数据使用int进行存储，复杂类型（字节类型，字符类型，浮点类型）使用SDS（simple dynamic string）进行存储。

当字符串长度小于1M时，每次扩容时加倍现有的空间，如果超过1M，每次扩容只会多扩容1M的空间，最大512M，

> 字符串最大长度512M

#### list

redis3.2之前使用linklist和ziplist进行存储，3.2之后采用quicklist（linklist+quicklist，由ziplist组成的双向链表，每个节点都是一个ziplist）进行存储。

应用场景：消息队列（lpush+brpop）、栈（lpush+lpop）

#### hash

数据结构：dict

Rehash：在dict内部，维护了两张哈希表，一张表时旧表，一张表时新表，当hashtable的大小需要动态变化时，旧表中的数据迁移到新表中，下一次变化，当前的新表又变成旧表。

渐进式rehash：为了避变rehash对服务器性能造成影响，服务器不是一次性将ht[0]中的所有键值对全部rehash到ht[1]中的，而是分多次、渐进式的：

1. 为ht[1]分配空间，让dict同时有ht[0]和ht[1]两个哈希表;
2. 在字典中维护一个索引计数器变量rehashidx，并将它的值设置为0；
3. 在rehash进行时，每次对字典执行添加、删除、查找或更新操作时，程序除了执行指定的操作以外，还会顺带将ht[0]在rehashidx索引上 所有键值对rehash到ht[1]，当rehash工作完成后，程序将rehash属性的值增1；
4. 随着dict字典操作的不断执行，最终在某个时间点，ht[0]上的键值对都会被rehash到ht[1]上，此时rehashidx的值变为-1，rehash过程结束。

使用场景：存储key-value格式的对象信息

#### set

数据结构：intset/hashtable(使用hashtable时，仅用key，value为null)。

使用场景：共同好友列表

#### sorted set

数据结构：ziplist/skiplist+hashtable

 使用场景：带有权重的元素列表，排行榜。