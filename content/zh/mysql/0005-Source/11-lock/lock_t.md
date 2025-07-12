---
title: lock_t
description: lock_t
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

加锁操作自然也是在事务中进行的，trx 属性就是加这个表锁的事务对象。

一个事务T1对应多个表锁

一个事务T1对应多个行锁

一个事务T1可能同时对应多个表锁和多个行锁

同一时刻，可能有多个事务已经或者想要对同一个表加锁。
  - 对于兼容的表锁，多个事务可以同时加锁
  - 对于不兼容的表锁，后加锁的事务就会处于等待状态。


## lock_t

| lock_t            | 数据结构                | 功能           | 说明                           |
| :---------------- | :---------------------- | :------------- | :----------------------------- |
| trx               | trx_t                   | 表锁行锁共有   | 加这个表锁或行锁的事务对象     |
| trx_locks         | UT_LIST_NODE_T(lock_t)  | 表锁行锁共有   | 该事务拥有的表锁和行锁混合链表 |
| type_mode         | uint32_t                | 表锁行锁共有   | 锁类型和锁模式                 |
| tab_lock/rec_lock | lock_table_t/lock_rec_t | 区分表锁和行锁 | &#10060;                       |
| index             | dict_index_t            | 行锁专用       | 该行锁结构所属的索引对象       |
| hash              | lock_t                  | 行锁专用       | &#9989;                        |


**type_mode**

type_mode 属性的第 5 位用于标识锁结构是否为表锁（LOCK_TABLE）。

对于表锁，锁结构中 type_mode 属性的第 5 位会被设置为 1，第 1 ~ 4 位会写入锁模式对应的整数值。

如果事务不能立即获得表锁，type_mode 属性的第 9 位会被设置为 1，表示处于锁等待状态。

**index**：主表的记录存储在主键索引中，二级索引（包括唯一索引、非唯一索引）的记录存储在二级索引中，行锁都是对主键索引或二级索引的记录加锁。index 属性就是这个行锁结构所属的索引对象。


```c++
/** Lock struct; protected by lock_sys latches */
struct lock_t {
        /** transaction owning the lock */
        trx_t *trx;

        /** list of the locks of the transaction */
        UT_LIST_NODE_T(lock_t) trx_locks;

        /** Index for a record lock */
        dict_index_t *index;

        /** Hash chain node for a record lock. The link node in a singly
        linked list, used by the hash table. */
        lock_t *hash;

        union {
                /** Table lock */
                lock_table_t tab_lock;

                /** Record lock */
                lock_rec_t rec_lock;
        };

#ifdef HAVE_PSI_THREAD_INTERFACE
        #ifdef HAVE_PSI_DATA_LOCK_INTERFACE
        /** Performance schema thread that created the lock. */
        ulonglong m_psi_internal_thread_id;

        /** Performance schema event that created the lock. */
        ulonglong m_psi_event_id;
        #endif /* HAVE_PSI_DATA_LOCK_INTERFACE */
#endif         /* HAVE_PSI_THREAD_INTERFACE */

        /** The lock type and mode bit flags.
        LOCK_GAP or LOCK_REC_NOT_GAP, LOCK_INSERT_INTENTION, wait flag, ORed */
        uint32_t type_mode;

#if defined(UNIV_DEBUG)
        /** Timestamp when it was created. */
        uint64_t m_seq;
#endif /* UNIV_DEBUG */

        /** Unlock the GAP Lock part of this Next Key Lock */
        void unlock_gap_lock() {
                ut_ad(!is_gap());
                ut_ad(!is_insert_intention());
                ut_ad(is_next_key_lock());

                type_mode |= LOCK_REC_NOT_GAP;
        }

        /** Determine if the lock object is a record lock.
        @return true if record lock, false otherwise. */
        bool is_record_lock() const {
                return (type() == LOCK_REC);
        }

        /** Determine if it is predicate lock.
        @return true if predicate lock, false otherwise. */
        bool is_predicate() const {
                return (type_mode & (LOCK_PREDICATE | LOCK_PRDT_PAGE));
        }

        /** @return true if the lock wait flag is set */
        bool is_waiting() const {
                return (type_mode & LOCK_WAIT);
        }

        /** @return true if the gap lock bit is set */
        bool is_gap() const {
                return (type_mode & LOCK_GAP);
        }

        /** @return true if the not gap lock bit is set */
        bool is_record_not_gap() const {
                return (type_mode & LOCK_REC_NOT_GAP);
        }

        /** @return true iff the lock is a Next Key Lock */
        bool is_next_key_lock() const {
                return is_record_lock() && lock_mode_is_next_key_lock(type_mode);
        }

        /** @return true if the insert intention bit is set */
        bool is_insert_intention() const {
                return (type_mode & LOCK_INSERT_INTENTION);
        }

        /** @return true iff this lock is (at least) on supremum pseudo-record */
        bool includes_supremum() const {
                return (lock_rec_get_nth_bit(this, PAGE_HEAP_NO_SUPREMUM));
        }

        /** @return the lock mode */
        uint32_t type() const {
                return (type_mode & LOCK_TYPE_MASK);
        }

        /** @return the precise lock mode */
        lock_mode mode() const {
                return (static_cast<lock_mode>(type_mode & LOCK_MODE_MASK));
        }

        /** Get lock hash table
        @return lock hash table */
        hash_table_t *hash_table() const {
                return (lock_hash_get(type_mode));
        }

        /** @return the transaction's query thread state. */
        trx_que_t trx_que_state() const {
                return (trx->lock.que_state);
        }

        /** Print the lock object into the given output stream.
        @param[in,out]        out     the output stream
        @return the given output stream. */
        std::ostream &print(std::ostream &out) const;

        /** Convert the member 'type_mode' into a human readable string.
        @return human readable string */
        std::string type_mode_string() const;

        /* @return the string/text representation of the record type. */
        const char *type_string() const {
                switch (type_mode & LOCK_TYPE_MASK) {
                        case LOCK_REC:
                                return ("LOCK_REC");
                        case LOCK_TABLE:
                                return ("LOCK_TABLE");
                        default:
                                ut_error;
                }
        }
};

```












## lock_table_t

| lock_table_t | 数据结构               | 功能         | 说明     |
| :----------- | :--------------------- | :----------- | :------- |
| table        | dict_table_t           | 表对象       | &#10060; |
| locks        | UT_LIST_NODE_T(lock_t) | 该表已有的锁 |          |

**locks**：事务想要对某个表加锁，InnoDB 怎么判断事务可以立即获得锁，还是要进入等待状态？这就是 locks 属性的用武之地了。
  - 多个事务对同一个表加了表锁，这些表锁的锁结构会通过 locks 属性形成一个链表。
  - 事务想要对某个表加表锁，InnoDB 就会遍历这个链表。
  - 如果链表中还没有表锁结构，或者所有锁结构对应的表锁都和事务当前要加的表锁兼容，事务就可以立即获得锁，否则就需要进入等待状态。




```c++
/** A table lock */
struct lock_table_t {
        dict_table_t *table; /*!< database table in dictionary
                             cache */
        UT_LIST_NODE_T(lock_t)
        locks; /*!< list of locks on the same
               table */
        /** Print the table lock into the given output stream
        @param[in,out]        out     the output stream
        @return the given output stream. */
        std::ostream &print(std::ostream &out) const;
};


```







## lock_rec_t


| lock_table_t | 数据结构  | 功能   | 说明     |
| :----------- | :-------- | :----- | :------- |
| page_id      | page_id_t | 表对象 | &#10060; |
| n_bits       | uint32_t  | bitmap |          |




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





