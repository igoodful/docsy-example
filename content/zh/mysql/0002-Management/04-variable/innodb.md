---
title: innodb
description: innodb
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
> [max_allowed_packet](https://opensource.actionsky.com/20220712-mysql/)
{{< /alert >}}


## 参数


| 参数                             | 全局动态        | 建议值：默认值：可选值                       | 描述                                                       |
| :------------------------------- | :-------------- | :------------------------------------------- | :--------------------------------------------------------- |
| innodb_buffer_pool_size          | &#9989;         | 内存一半：128M ：`5M-2^64-1`                 | database缓冲池的字节大小，InnoDB缓存表和索引数据的内存区域 |
| innodb_buffer_pool_instances     | &#9989;         | 8：8：`1~64`                                 | database开启多个内存缓冲池                                 |
| innodb_thread_concurrency        | &#9989;         | CPU核数：0：`0-1000`                         | 0表示无限并发                                              |
| innodb_doublewrite               | &#10060;&#9989; | ON：ON：`ON/OFF`                             | version≥8.0.30时为动态，否则为静态                         |
| innodb_file_per_table            | &#9989;         | ON：ON：`ON/OFF`                             |                                                            |
| innodb_flush_log_at_trx_commit   | &#9989;         | 1：1：`0/1/2 `                               |                                                            |
| sync_binlog                      | &#9989;         | 1：1：`0-4294967295`                         |                                                            |
| innodb_flush_method              | &#10060;        | O_DIRECT：fsync：                            | fsync,O_DSYNC,littlesync,nosync,O_DIRECT,O_DIRECT_NO_FSYNC |
| innodb_io_capacity               | &#9989;         | 8000：200：`100-2^64-1`                      |                                                            |
| innodb_io_capacity_max           | &#9989;         |                                              |                                                            |
| innodb_log_buffer_size           | &#9989;         | 128M：16M：`1M-4G `                          |                                                            |
| innodb_log_file_size             | &#10060;        | 1G：48M：`4M-512G/innodb_log_files_in_group` |                                                            |
| innodb_log_files_in_group        | &#10060;        | 4：2：`2-100`                                |                                                            |
| innodb_print_all_deadlocks       | &#9989;         | ON：OFF：`ON/OFF`                            | 事务中所有死锁的InnoDB信息都会记录在mysqld错误日志中       |
| innodb_read_io_threads           | &#10060;        | 4：4：`1-64 `                                | 读取操​​作的 I/O 线程数                                    |
| innodb_write_io_threads          | &#10060;        | 4：4：`1-64`                                 | 写入操作的 I/O 线程数                                      |
| innodb_temp_data_file_path       | &#10060;        | `ibtmp1:12M:autoextend:max:81920M`：         | 默认值： `ibtmp1:12M:autoextend`                           |
| transaction_isolation            | &#9989;session  | READCOMMITTED                                | READCOMMITTED,REPEATABLEREAD database 设置默认事务隔离级别 |
| transaction_write_set_extraction | &#9989;session  | XXHASH64：XXHASH64：`XXHASH64/OFF/MURMUR32`  |                                                            |
| innodb_stats_persistent          |                 |                                              |                                                            |
|                                  |                 |                                              |                                                            |
|                                  |                 |                                              |                                                            |






- innodb_buffer_pool_size: Buffer Pool 总的大小.
- innodb_buffer_pool_instances: Buffer Pool 中 Instance 的数量
- innodb_buffer_pool_chunk_size: Buffer Pool 中 Chunk 的大小, 默认为128M


### innodb_buffer_pool_size


- 对于支持大页的 Linux 系统，InnoDB 使用shmget()申请共享内存, 而一般的 Linux 环境下使用mmap() (MAP_PRIVATE | OS_MAP_ANON) 申请指定大小的匿名页内存, 所以 Buffer Pool 的物理内存并不是启动时就全部分配，而是随着用户的使用动态分配物理内存，因此即使innodb_buffer_pool_size设置远远超过了物理总内存大小也能启动成功
- Buffer Pool 中 Chunk 的大小可以自定义设置, 所以整个 Buffer Pool 的内存大小innodb_buffer_pool_size分配公式为n * innodb_buffer_pool_chunk_size * innodb_buffer_pool_instances, n 为 Chunk 的数量. n 由 Buffer Pool 初始化阶段计算得出.
- 如果我们指定的innodb_buffer_pool_size大于innodb_buffer_pool_chunk_size * innodb_buffer_pool_instances并且不是innodb_buffer_pool_chunk_size * innodb_buffer_pool_instances的整数倍，那么服务器会自动的把innodb_buffer_pool_size的值调整为innodb_buffer_pool_chunk_size * innodb_buffer_pool_instances的整数倍



### innodb_buffer_pool_instances

Buffer Pool 这块内存又会分为很多instance，每个instance都有自己的锁、信号量、物理块(Buffer chunks)以及逻辑链表，即各个instance之间没有竞争关系，可以并发读取与写入。

InnoDB Buffer Pool有三种核心的数据结构：buf_pool_t，buf_block_t，buf_page_t，buf_chunk_t。先有个直观印象，其中 buf_pool_t 是instance级别控制体，存储了诸多逻辑链表、hash表、mutex等；buf_block_t buf_page_t 是数据页的两个控制体，两种类型指针可转换，其中frame是数据指针；buf_chunk_t: buffer pool 按chunk分配。


### LRU Free Flush
在MySQL的InnoDB缓冲池中，LRU（Least Recently Used）列表、Free列表和Flush列表是三个关键的列表，它们用于管理内存中的页。

- LRU列表（LRU List）：
  - 包含了缓冲池中所有的页，按照它们最近被使用的顺序排列。
  - 页在LRU列表中根据最近的使用情况进行排序，最近使用的页靠近列表的头部，而最久未使用的页靠近列表的尾部。

- Free列表（Free List）：
  - 包含了空闲的页，即未被使用的页。
  - 在LRU列表的尾部，Free列表的头部可能有一些空闲的页。

- Flush列表（Flush List）：
  - 包含了被修改但还未写入磁盘的页。
  - 当页被修改时，它被移动到Flush列表，然后在适当的时候写入磁盘。写入后，它可能会被移动回LRU列表的头部。

**包含关系**：
- LRU列表包含所有缓冲池中的页，其中包括Free列表的页。
- Free列表包含一些未被使用的页，这些页也同时存在于LRU列表的尾部。
- Flush列表包含被修改但未写入磁盘的页，这些页也同时存在于LRU列表的某个位置。

这三个列表共同协作，以确保内存中的页在频繁使用时保持在LRU列表的头部，而未被使用时可以成为Free列表的一部分。 Flush列表用于异步将修改的页写回磁盘，以确保数据的持久性。

**page_hash**: 每个Buffer Pool Instance有一个page hash链表，通过它，使用space_id和page_no就能快速找到已经被读入内存的数据页，而不用线性遍历LRU List去查找。注意这个hash表不是 InnoDB的自适应哈希，自适应哈希是为了减少Btree的扫描，而page hash是为了避免扫描LRU List。--后面对InnoDB自适应哈希和普通hash表简单分析一下。






