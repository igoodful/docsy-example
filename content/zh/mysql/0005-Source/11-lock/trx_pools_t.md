---
title: PoolManager
description: 事务池管理器
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

事务池管理器只有一个，可能有多个事务池

每个事务池有4228个事务对象




- 每个事务对象大小为：992 字节
- 每个事务池大小为：4194304 字节，也就是 4M，故每个事务池可存放4194304 / 992 = 4228 个事务对象。
  - 初始化事务池的过程中，不会初始化全部的 4228 块小内存，只会初始化最前面的 16 块小内存，得到 16 个事务对象并放入事务队列
  - 初始事务池完成之后，事务队列中只有 16 个事务对象。
  - 那么，剩余的 4212 块小内存什么时候会被初始化？启动过程中初始化的 16 个事务对象都被取走使用了，事务队列变成空队列了。此时，需要再分配一个事务对象用于启动新事务，InnoDB 就会把剩余的 4212 块小内存全部初始化，得到 4212 个事务对象并放入事务队列。

有一点需要说明，不管是启动过程中初始化的 16 块小内存，还是运行过程中初始化的 4212 块小内存，都是在循环里一个一个初始化的。每一轮循环都要干两件事：
  - 初始化一块小内存，得到一个事务对象。
  - 把事务对象放入事务池的事务队列中。

初始化小块内存的过程中，会初始化事务对象的各个属性。这里我们就不一一介绍这些属性了，等到该它们出场的时候，再按需介绍。

InnoDB 只有一个事务池管理器，用于管理 N 个事务池（N >= 1），每个事务池可以管理 4228 个事务对象。

MySQL 启动过程中，InnoDB 会先创建事务管理器。事务管理器会创建一个事务池，初始化 16 个事务对象放入事务池的事务队列。

MySQL 运行过程中，如果这 16 个事务对象都正在被使用，InnoDB 需要一个新的事务对象时，会一次性初始化剩余的 4212 个事务对象并放入事务池的事务队列。


```c++

/** The trx_t pool manager */
static trx_pools_t *trx_pools;

/** Size of on trx_t pool in bytes. */
static const ulint MAX_TRX_BLOCK_SIZE = 1024 * 1024 * 4;

/** Create the trx_t pool */
void trx_pool_init() {
        trx_pools = ut::new_withkey<trx_pools_t>(UT_NEW_THIS_FILE_PSI_KEY, MAX_TRX_BLOCK_SIZE);

        ut_a(trx_pools != nullptr);
}



```

- MAX_TRX_BLOCK_SIZE：指定每个事务池大小，这意味着每个事务池能用来存放事务对象的内存是 4194304 字节，也就是 4M。每个事务对象的大小为 992 字节，4M 内存能够存放 4194304 / 992 = 4228 个事务对象。


## PoolManager
```c++
template <typename Pool, typename LockStrategy>
struct PoolManager {
        typedef Pool PoolType;
        typedef typename PoolType::value_type value_type;

        PoolManager(size_t size) : m_size(size) {
                create();
        }

        ~PoolManager() {
                destroy();

                ut_a(m_pools.empty());
        }

        /** Get an element from one of the pools.
        @return instance or NULL if pool is empty. */
        value_type *get() {
                size_t index    = 0;
                size_t delay    = 1;
                value_type *ptr = nullptr;

                do {
                        m_lock_strategy.enter();

                        ut_ad(!m_pools.empty());

                        size_t n_pools = m_pools.size();

                        PoolType *pool = m_pools[index % n_pools];

                        m_lock_strategy.exit();

                        ptr = pool->get();

                        if (ptr == nullptr && (index / n_pools) > 2) {
                                if (!add_pool(n_pools)) {
                                        ib::error(ER_IB_MSG_FAILED_TO_ALLOCATE_WAIT, m_size, delay);

                                        /* There is nothing much we can do
                                        except crash and burn, however lets
                                        be a little optimistic and wait for
                                        a resource to be freed. */
                                        std::this_thread::sleep_for(std::chrono::seconds(delay));

                                        if (delay < 32) {
                                                delay <<= 1;
                                        }

                                } else {
                                        delay = 1;
                                }
                        }

                        ++index;

                } while (ptr == nullptr);

                return (ptr);
        }

        static void mem_free(value_type *ptr) {
                PoolType::mem_free(ptr);
        }

private:
        /** Add a new pool
        @param n_pools Number of pools that existed when the add pool was
                        called.
        @return true on success */
        bool add_pool(size_t n_pools) {
                bool added = false;

                m_lock_strategy.enter();

                if (n_pools < m_pools.size()) {
                        /* Some other thread already added a pool. */
                        added = true;
                } else {
                        PoolType *pool;

                        ut_ad(n_pools == m_pools.size());

                        pool = ut::new_withkey<PoolType>(UT_NEW_THIS_FILE_PSI_KEY, m_size);

                        if (pool != nullptr) {
                                ut_ad(n_pools <= m_pools.size());

                                m_pools.push_back(pool);

                                added = true;
                        }
                }

                ut_ad(n_pools < m_pools.size() || !added);

                m_lock_strategy.exit();

                return (added);
        }

        /** Create the pool manager. */
        void create() {
                ut_a(m_size > sizeof(value_type));
                m_lock_strategy.create();

                add_pool(0);
        }

        /** Release the resources. */
        void destroy() {
                typename Pools::iterator it;
                typename Pools::iterator end = m_pools.end();

                for (it = m_pools.begin(); it != end; ++it) {
                        PoolType *pool = *it;

                        ut::delete_(pool);
                }

                m_pools.clear();

                m_lock_strategy.destroy();
        }

private:
        // Disable copying
        PoolManager(const PoolManager &);
        PoolManager &operator=(const PoolManager &);

        typedef std::vector<PoolType *, ut::allocator<PoolType *>> Pools;

        /** Size of each block */
        size_t m_size;

        /** Pools managed this manager */
        Pools m_pools;

        /** Lock strategy to use */
        LockStrategy m_lock_strategy;
};


```





