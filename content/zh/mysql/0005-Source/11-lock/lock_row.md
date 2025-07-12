---
title: 行锁
description: 行锁
date: 2023-10-30
weight: 50


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}

本文基于 MySQL 8.0.32 源码，存储引擎为 InnoDB。

格式化：Markdown Table Prettifier

{{</alert>}}



## 一、锁模式兼容表


判断行锁的精确模式的强弱关系，没有强弱关系图可用，好在这个判断过程也不太复杂。

行锁的精确模式有四种：

- LOCK_REC_NOT_GAP（普通记录锁）。

- LOCK_GAP（间隙锁）。

- LOCK_ORDINARY（Next-Key 锁）。

- LOCK_INSERT_INTENTION（插入记录锁）。


插入意向锁，其实是一种特殊的间隙锁，它的精确模式包含 LOCK_GAP 和 LOCK_INSERT_INTENTION。但是，它的加锁场景，都是某个事务想要插入记录到某个间隙，而这个间隙被其它事务加了间隙锁或者 Next-Key 锁。

其它三种精确模式单独使用时，都用于查询、更新、删除记录，和插入意向锁的场景不一样。

所以，判断行锁的精确模式的强弱关系时，会排除插入意向锁。

也就是说，如果 hash 行锁结构的精确锁模式包含 LOCK_INSERT_INTENTION，遍历行锁结构的过程中，这个行锁结构会被忽略，不会进入判断锁模式、精确模式的强弱关系的逻辑。

如果 hash 行锁结构的精确锁模式是其它三种之一，只要这个精确模式和本次加锁的精确模式符合以下五种情况的一种：

- hash 行锁结构和本次加锁的精确模式都为 LOCK_REC_NOT_GAP（普通记录锁）。
- hash 行锁结构和本次加锁的精确模式都为 LOCK_GAP（间隙锁）。
- hash 行锁结构和本次加锁的精确模式都为 LOCK_ORDINARY（Next-Key 锁）。
- hash 行锁结构的精确模式为 LOCK_ORDINARY（Next-Key 锁），本次加锁的精确模式为 LOCK_REC_NOT_GAP（普通记录锁）。
- hash 行锁结构的精确模式为 LOCK_ORDINARY（Next-Key 锁），本次加锁的精确模式为 LOCK_GAP（间隙锁）。

说明 hash 行锁的精确模式，和本次加锁的精确模式相比，级别相同（前三种情况）或者更高（后两种情况）。

这意味着事务 T1 对记录 R1 加过的锁，满足本次加锁的要求，不需要再重复加锁，本次加锁流程就此结束。

如果 hash 行锁结构的精确模式，和本次加锁的精确模式，不符合以上五种情况，说明事务 T1 对记录 R1 加过的锁，不满足本次加锁的要求。

如果遍历完整个行锁结构链表，都没有找到符合本次加锁要求的行锁结构，本次加锁流程需要继续进行，以完成加锁操作。






| 锁模式     | 意向共享锁 | 意向排他锁 | 共享锁   | 排他锁   | AUTO-INC锁 |
| :--------- | :--------- | :--------- | :------- | :------- | :--------- |
| 意向共享锁 | &#9989;    | &#9989;    | &#9989;  | &#10060; | &#9989;    |
| 意向排他锁 | &#9989;    | &#9989;    | &#10060; | &#10060; | &#9989;    |
| 共享锁     | &#9989;    | &#10060;   | &#9989;  | &#10060; | &#10060;   |
| 排他锁     | &#10060;   | &#10060;   | &#10060; | &#10060; | &#10060;   |
| AUTO-INC锁 | &#9989;    | &#9989;    | &#10060; | &#10060; | &#10060;   |

- &#9989; 表示锁兼容
- &#10060; 表示锁不兼容


数据页大小

```c++
/** The universal page size of the database */
#define UNIV_PAGE_SIZE ((ulint)srv_page_size)
```


## lock_rec_t

```c++
/** Record lock for a page */
struct lock_rec_t {
        /** The id of the page on which records referenced by this lock's bitmap are
        located. */
        page_id_t page_id;
        /** number of bits in the lock bitmap;
        Must be divisible by 8.
        NOTE: the lock bitmap is placed immediately after the lock struct */
        uint32_t n_bits;

        /** Print the record lock into the given output stream
        @param[in,out]        out     the output stream
        @return the given output stream. */
        std::ostream &print(std::ostream &out) const;
};

```

- page_id：每个行锁结构都会根据 page_id 属性中保存的表空间 ID、数据页号计算得到一个哈希值。哈希值相同的多个行锁结构通过 hash 属性形成一个行锁结构链表。

- n_bits：属性的值是个无符号整数，表示这个锁结构能保存多少条记录的行锁状态，也就是最多有多少记录能共用这个行锁结构。








