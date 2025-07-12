---
title: trx_rseg_t
description: trx_rseg_t
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}

- 在每台服务器上执行
- 在进行下一步之前，您必须确保错误日志中没有生成任何警告

{{</alert>}}




## trx_rseg_t对象

| 成员                 | 类型                  | 功能 |
| :------------------- | :-------------------- | :--- |
| `id`                 | `size_t`              |      |
| `mutex`              | `RsegMutex`           |      |
| `space_id`           | `space_id_t`          |      |
| `page_no`            | `page_no_t`           |      |
| `page_size`          | `page_size_t`         |      |
| `max_size`           | `page_no_t`           |      |
| `curr_size`          | `page_no_t`           |      |
| `update_undo_list`   | `Undo_list`           |      |
| `update_undo_cached` | `Undo_list`           |      |
| `insert_undo_list`   | `Undo_list`           |      |
| `insert_undo_cached` | `Undo_list`           |      |
| `last_page_no`       | `page_no_t`           |      |
| `last_offset`        | `size_t`              |      |
| `last_trx_no`        | `trx_id_t`            |      |
| `last_del_marks`     | `bool`                |      |
| `trx_ref_count`      | `std::atomic<size_t>` |      |



#### trx_rseg_t

```c++

/** The rollback segment memory object */
struct trx_rseg_t {
#ifdef UNIV_DEBUG
        /** Validate the curr_size member by re-calculating it.
        @param[in]  take_mutex  take the rseg->mutex. default is true.
        @return true if valid, false otherwise. */
        bool validate_curr_size(bool take_mutex = true);
#endif /* UNIV_DEBUG */

        /** Enter the rseg->mutex. */
        void latch() {
                mutex_enter(&mutex);
                ut_ad(validate_curr_size(false));
        }

        /** Exit the rseg->mutex. */
        void unlatch() {
                ut_ad(validate_curr_size(false));
                mutex_exit(&mutex);
        }

        /** Decrement the current size of the rollback segment by the given number
        of pages.
        @param[in]  npages  number of pages to reduce in size. */
        void decr_curr_size(page_no_t npages = 1) {
                ut_ad(curr_size >= npages);
                curr_size -= npages;
        }

        /** Increment the current size of the rollback segment by the given number
        of pages. */
        void incr_curr_size() {
                ++curr_size;
        }

        /* Get the current size of the rollback segment in pages.
         @return current size of the rollback segment in pages. */
        page_no_t get_curr_size() const {
                return (curr_size);
        }

        /* Set the current size of the rollback segment in pages.
        @param[in]  npages  new value for the current size. */
        void set_curr_size(page_no_t npages) {
                curr_size = npages;
        }

        /*--------------------------------------------------------*/
        /** rollback segment id == the index of its slot in the trx
        system file copy */
        size_t id{};

        /** mutex protecting the fields in this struct except id,space,page_no
        which are constant */
        RsegMutex mutex;

        /** space ID where the rollback segment header is placed */
        space_id_t space_id{};

        /** page number of the rollback segment header */
        page_no_t page_no{};

        /** page size of the relevant tablespace */
        page_size_t page_size;

        /** maximum allowed size in pages */
        page_no_t max_size{};

private:
        /** current size in pages */
        page_no_t curr_size{};

public:
        using Undo_list = UT_LIST_BASE_NODE_T_EXTERN(trx_undo_t, undo_list);
        /*--------------------------------------------------------*/
        /* Fields for update undo logs */
        /** List of update undo logs */
        Undo_list update_undo_list;

        /** List of update undo log segments cached for fast reuse */
        Undo_list update_undo_cached;

        /*--------------------------------------------------------*/
        /* Fields for insert undo logs */
        /** List of insert undo logs */
        Undo_list insert_undo_list;

        /** List of insert undo log segments cached for fast reuse */
        Undo_list insert_undo_cached;

        /*--------------------------------------------------------*/

        /** Page number of the last not yet purged log header in the history
        list; FIL_NULL if all list purged */
        page_no_t last_page_no{};

        /** Byte offset of the last not yet purged log header */
        size_t last_offset{};

        /** Transaction number of the last not yet purged log */
        trx_id_t last_trx_no;

        /** true if the last not yet purged log needs purging */
        bool last_del_marks{};

        /** Reference counter to track rseg allocated transactions. */
        std::atomic<size_t> trx_ref_count{};

        std::ostream &print(std::ostream &out) const {
                out << "[trx_rseg_t: this=" << (void *)this << ", id=" << id << ", space_id=" << space_id
                    << ", page_no=" << page_no << ", curr_size=" << curr_size << "]";
                return (out);
        }
};

```









