---
title: mdl
description: mdl
date: 2023-10-30
weight: 50


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32

{{< /alert >}}


## MDL Lock类型
```viz-dot
digraph "MDL Lock类型" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
                ];
        "mdl" -> "MDL_INTENTION_EXCLUSIVE";
        "mdl" -> "MDL_SHARED";
        "mdl" -> "MDL_SHARED_HIGH_PRIO";
        "mdl" -> "MDL_SHARED_READ";
        "mdl" -> "MDL_SHARED_WRITE";
        "mdl" -> "MDL_SHARED_WRITE_LOW_PRIO" ;
        "mdl" -> "MDL_SHARED_UPGRADABLE" ;
        "mdl" -> "MDL_SHARED_READ_ONLY" ;
        "mdl" -> "MDL_SHARED_NO_WRITE" ;
        "mdl" -> "MDL_SHARED_NO_READ_WRITE" ;
        "mdl" -> "MDL_EXCLUSIVE" ;
        "mdl" -> "MDL_TYPE_END" ;

}
```
| enum_mdl_type           | 功能                                       | 示例 |
|:------------------------|:-------------------------------------------|:-----|
| MDL_INTENTION_EXCLUSIVE |                                            |      |
| MDL_SHARED              | 这把锁一般用在 flush tables with read lock |      |
| MDL_SHARED_HIGH_PRIO    |                                            |      |
| MDL_SHARED_READ         |                                            |      |




## enum_mdl_type
```c++
enum enum_mdl_type {
        MDL_INTENTION_EXCLUSIVE = 0,
        MDL_SHARED,
        MDL_SHARED_HIGH_PRIO,
        MDL_SHARED_READ,
        MDL_SHARED_WRITE,
        MDL_SHARED_WRITE_LOW_PRIO,
        MDL_SHARED_UPGRADABLE,
        MDL_SHARED_READ_ONLY,
        MDL_SHARED_NO_WRITE,
        MDL_SHARED_NO_READ_WRITE,
        MDL_EXCLUSIVE,
        MDL_TYPE_END
};

```

## enum_mdl_duration

```c++
enum enum_mdl_duration {
        MDL_STATEMENT = 0,
        MDL_TRANSACTION,
        MDL_EXPLICIT,
        MDL_DURATION_END
};
```

| enum_mdl_duration | 功能       | 示例 |
|:------------------|:-----------|:-----|
| MDL_STATEMENT     | 语句级别锁 |      |
| MDL_TRANSACTION   | 事务级别锁 |      |
| MDL_EXPLICIT      | 显式锁     |      |
| MDL_DURATION_END  |            |      |

MDL Lock 持续周期

通常我们需要关注 MDL Lock 是事务提交后释放还是语句结束后释放，实际上就是这个，这对 MDL lock 堵塞的范围很重要。

这三种类型指的是MySQL中的锁类型，用于控制并发访问数据库对象（如表、行等）。下面对每种类型进行详细解释：

1. **MDL_STATEMENT**：
   - **语句级别锁**：这种类型的锁在语句执行期间获取，并在语句执行结束时自动释放。它们不会在事务结束时持续存在，只在执行单个语句期间有效。因此，它们适用于需要短暂访问对象的操作。

2. **MDL_TRANSACTION**：
   - **事务级别锁**：这种类型的锁在事务开始时获取，并在事务结束时自动释放。它们在整个事务期间持续有效，可以保护事务中的多个语句对数据库对象的访问。当事务提交或回滚时，这些锁会自动释放。

3. **MDL_EXPLICIT**：
   - **显式锁**：这种类型的锁不会在语句或事务结束时自动释放，需要通过调用`MDL_context::release_lock()`方法来显式释放。它们通常由用户在代码中明确请求，用于特定的场景，例如在复杂的并发控制逻辑中需要手动管理锁的释放。

总的来说，MDL_STATEMENT用于语句级别的锁，MDL_TRANSACTION用于事务级别的锁，而MDL_EXPLICIT用于需要手动管理锁释放的情况。在设计并发控制策略时，选择合适的锁类型非常重要，以确保数据的一致性和并发访问的效率







## MDL_key

```c++
struct MDL_key {
public:
#ifdef HAVE_PSI_INTERFACE
        static void init_psi_keys();
#endif

        enum enum_mdl_namespace {
                GLOBAL = 0,
                BACKUP_LOCK,
                TABLESPACE,
                SCHEMA,
                TABLE,
                FUNCTION,
                PROCEDURE,
                TRIGGER,
                EVENT,
                COMMIT,
                USER_LEVEL_LOCK,
                LOCKING_SERVICE,
                SRID,
                ACL_CACHE,
                COLUMN_STATISTICS,
                RESOURCE_GROUPS,
                FOREIGN_KEY,
                CHECK_CONSTRAINT,
                NAMESPACE_END
        };

        const uchar *ptr() const {
                return pointer_cast<const uchar *>(m_ptr);
        }
        uint length() const {
                return m_length;
        }

        const char *db_name() const {
                return m_ptr + 1;
        }
        uint db_name_length() const {
                return m_db_name_length;
        }

        const char *name() const {
                return (use_normalized_object_name() ? m_ptr + m_length : m_ptr + m_db_name_length + 2);
        }
        uint name_length() const {
                return m_object_name_length;
        }

        const char *col_name() const {
                assert(!use_normalized_object_name());

                if (m_db_name_length + m_object_name_length + 3 < m_length) {
                        /* A column name was stored in the key buffer. */
                        return m_ptr + m_db_name_length + m_object_name_length + 3;
                }

                /* No column name stored. */
                return nullptr;
        }

        uint col_name_length() const {
                assert(!use_normalized_object_name());

                if (m_db_name_length + m_object_name_length + 3 < m_length) {
                        /* A column name was stored in the key buffer. */
                        return m_length - m_db_name_length - m_object_name_length - 4;
                }

                /* No column name stored. */
                return 0;
        }

        enum_mdl_namespace mdl_namespace() const {
                return (enum_mdl_namespace)(m_ptr[0]);
        }

        void mdl_key_init(enum_mdl_namespace mdl_namespace, const char *db, const char *name) {
                m_ptr[0] = (char)mdl_namespace;

                assert(!use_normalized_object_name());

                assert(strlen(db) <= NAME_LEN);
                assert((mdl_namespace == TABLESPACE) || (strlen(name) <= NAME_LEN));
                m_db_name_length = static_cast<uint16>(strmake(m_ptr + 1, db, NAME_LEN) - m_ptr - 1);
                m_object_name_length =
                    static_cast<uint16>(strmake(m_ptr + m_db_name_length + 2, name, NAME_LEN) - m_ptr - m_db_name_length - 2);
                m_length = m_db_name_length + m_object_name_length + 3;
        }


        void mdl_key_init(enum_mdl_namespace mdl_namespace, const char *db, const char *name, const char *column_name) {
                m_ptr[0] = (char)mdl_namespace;
                char *start;
                char *end;

                assert(!use_normalized_object_name());

                assert(strlen(db) <= NAME_LEN);
                start            = m_ptr + 1;
                end              = strmake(start, db, NAME_LEN);
                m_db_name_length = static_cast<uint16>(end - start);

                assert(strlen(name) <= NAME_LEN);
                start                = end + 1;
                end                  = strmake(start, name, NAME_LEN);
                m_object_name_length = static_cast<uint16>(end - start);

                size_t col_len = strlen(column_name);
                assert(col_len <= NAME_LEN);
                start               = end + 1;
                size_t remaining    = MAX_MDLKEY_LENGTH - m_db_name_length - m_object_name_length - 3;
                uint16 extra_length = 0;

                static_assert(MAX_MDLKEY_LENGTH == 387, "UTF8MB3");

                if (remaining >= col_len + 1) {
                        end          = strmake(start, column_name, remaining);
                        extra_length = static_cast<uint16>(end - start) + 1;  // With \0
                }
                m_length = m_db_name_length + m_object_name_length + 3 + extra_length;
                assert(m_length <= MAX_MDLKEY_LENGTH);
        }

        void mdl_key_init(enum_mdl_namespace mdl_namespace,
                          const char *db,
                          const char *normalized_name,
                          size_t normalized_name_len,
                          const char *name) {
                m_ptr[0] = (char)mdl_namespace;

                assert(use_normalized_object_name());

                assert(strlen(db) <= NAME_LEN && strlen(name) <= NAME_LEN && normalized_name_len <= NAME_CHAR_LEN * 2);

                // Database name.
                m_db_name_length = static_cast<uint16>(strmake(m_ptr + 1, db, NAME_LEN) - m_ptr - 1);

                // Normalized object name.
                m_length = static_cast<uint16>(m_db_name_length + normalized_name_len + 3);
                memcpy(m_ptr + m_db_name_length + 2, normalized_name, normalized_name_len);
                *(m_ptr + m_length - 1) = 0;

                if (strlen(name) < static_cast<size_t>(MAX_MDLKEY_LENGTH - m_length)) {
                        m_object_name_length = static_cast<uint16>(
                            (strmake(m_ptr + m_length, name, MAX_MDLKEY_LENGTH - m_length - 1) - m_ptr - m_length));
                } else {
                        m_object_name_length = 0;
                        *(m_ptr + m_length)  = 0;
                }

                assert(m_length + m_object_name_length < MAX_MDLKEY_LENGTH);
        }

        void mdl_key_init(enum_mdl_namespace mdl_namespace, const char *part_key, size_t part_key_length, size_t db_length) {
                /*
                  Key suffix provided should be in compatible format and
                  its components should adhere to length restrictions.
                */
                assert(strlen(part_key) == db_length);
                assert(db_length + 1 + strlen(part_key + db_length + 1) + 1 == part_key_length);
                assert(db_length <= NAME_LEN);
                assert(part_key_length <= NAME_LEN + 1 + NAME_LEN + 1);

                m_ptr[0] = (char)mdl_namespace;
                /*
                  Partial key of objects with normalized object name can not be used to
                  initialize MDL key.
                */
                assert(!use_normalized_object_name());

                memcpy(m_ptr + 1, part_key, part_key_length);
                m_length             = static_cast<uint16>(part_key_length + 1);
                m_db_name_length     = static_cast<uint16>(db_length);
                m_object_name_length = m_length - m_db_name_length - 3;
        }
        void mdl_key_init(const MDL_key *rhs) {
                uint16 copy_length =
                    rhs->use_normalized_object_name() ? rhs->m_length + rhs->m_object_name_length + 1 : rhs->m_length;
                memcpy(m_ptr, rhs->m_ptr, copy_length);
                m_length             = rhs->m_length;
                m_db_name_length     = rhs->m_db_name_length;
                m_object_name_length = rhs->m_object_name_length;
        }
        void reset() {
                m_ptr[0]             = NAMESPACE_END;
                m_db_name_length     = 0;
                m_object_name_length = 0;
                m_length             = 0;
        }
        bool is_equal(const MDL_key *rhs) const {
                return (m_length == rhs->m_length && memcmp(m_ptr, rhs->m_ptr, m_length) == 0);
        }
        /**
          Compare two MDL keys lexicographically.
        */
        int cmp(const MDL_key *rhs) const {
                int res = memcmp(m_ptr, rhs->m_ptr, std::min(m_length, rhs->m_length));
                if (res == 0)
                        res = m_length - rhs->m_length;
                return res;
        }

        MDL_key(const MDL_key &rhs) {
                mdl_key_init(&rhs);
        }

        MDL_key &operator=(const MDL_key &rhs) {
                mdl_key_init(&rhs);
                return *this;
        }

        MDL_key(enum_mdl_namespace namespace_arg, const char *db_arg, const char *name_arg) {
                mdl_key_init(namespace_arg, db_arg, name_arg);
        }
        MDL_key() = default; /* To use when part of MDL_request. */

        const PSI_stage_info *get_wait_state_name() const {
                return &m_namespace_to_wait_state_name[(int)mdl_namespace()];
        }

private:
        bool use_normalized_object_name() const {
                return (mdl_namespace() == FUNCTION || mdl_namespace() == PROCEDURE || mdl_namespace() == EVENT ||
                        mdl_namespace() == RESOURCE_GROUPS || mdl_namespace() == TRIGGER);
        }

private:
        uint16 m_length{0};
        uint16 m_db_name_length{0};
        uint16 m_object_name_length{0};
        char m_ptr[MAX_MDLKEY_LENGTH]{0};
        static PSI_stage_info m_namespace_to_wait_state_name[NAMESPACE_END];
};
```


## MDL_lock

```c++
class MDL_lock {
public:
        typedef unsigned short bitmap_t;

        class Ticket_list {
        public:
                typedef I_P_List<MDL_ticket,
                                 I_P_List_adapter<MDL_ticket, &MDL_ticket::next_in_lock, &MDL_ticket::prev_in_lock>,
                                 I_P_List_null_counter,
                                 I_P_List_fast_push_back<MDL_ticket>>
                    List;
                operator const List &() const {
                        return m_list;
                }
                Ticket_list() : m_bitmap(0) {
                }

                void add_ticket(MDL_ticket *ticket);
                void remove_ticket(MDL_ticket *ticket);
                bool is_empty() const {
                        return m_list.is_empty();
                }
                bitmap_t bitmap() const {
                        return m_bitmap;
                }

        private:
                void clear_bit_if_not_in_list(enum_mdl_type type);

        private:
                /** List of tickets. */
                List m_list;
                /** Bitmap of types of tickets in this list. */
                bitmap_t m_bitmap;
        };

        typedef Ticket_list::List::Iterator Ticket_iterator;

        typedef longlong fast_path_state_t;


        struct MDL_lock_strategy {

                bitmap_t m_granted_incompatible[MDL_TYPE_END];

                bitmap_t m_waiting_incompatible[4][MDL_TYPE_END];
                /**
                  Array of increments for "unobtrusive" types of lock requests for locks.
                  @sa MDL_lock::get_unobtrusive_lock_increment().
                */
                fast_path_state_t m_unobtrusive_lock_increment[MDL_TYPE_END];
                /**
                  Indicates that locks of this type are affected by
                  the max_write_lock_count limit.
                */
                bool m_is_affected_by_max_write_lock_count;

#ifndef NDEBUG
                /**
                 Indicate that a type is legal with this strategy. Only for asserts and
                 debug-only checks.
                 */
                bool legal_type[MDL_TYPE_END];
#endif /* not defined NDEBUG */


                bool (*m_needs_notification)(const MDL_ticket *ticket);

                void (*m_notify_conflicting_locks)(MDL_context *ctx, MDL_lock *lock);

                bitmap_t (*m_fast_path_granted_bitmap)(const MDL_lock &lock);

                bool (*m_needs_connection_check)(const MDL_lock *lock);
        };

public:
        /** The key of the object (data) being protected. */
        MDL_key key;

        mysql_prlock_t m_rwlock;

        const bitmap_t *incompatible_granted_types_bitmap() const {
                return m_strategy->m_granted_incompatible;
        }

        const bitmap_t *incompatible_waiting_types_bitmap() const {
                return m_strategy->m_waiting_incompatible[m_current_waiting_incompatible_idx];
        }


        uint get_incompatible_waiting_types_bitmap_idx() const {
                mysql_prlock_assert_write_owner(&m_rwlock);

                uint idx = 0;
                if (m_piglet_lock_count >= max_write_lock_count)
                        idx += 1;
                if (m_hog_lock_count >= max_write_lock_count)
                        idx += 2;
                return idx;
        }


        bool switch_incompatible_waiting_types_bitmap_if_needed() {
                mysql_prlock_assert_write_owner(&m_rwlock);

                uint new_idx = get_incompatible_waiting_types_bitmap_idx();
                if (m_current_waiting_incompatible_idx == new_idx)
                        return false;
                m_current_waiting_incompatible_idx = new_idx;
                return true;
        }

        bool has_pending_conflicting_lock(enum_mdl_type type);

        bool can_grant_lock(enum_mdl_type type, const MDL_context *requestor_ctx) const;

        void reschedule_waiters();

        void remove_ticket(MDL_context *ctx, LF_PINS *pins, Ticket_list MDL_lock::*queue, MDL_ticket *ticket);

        bool visit_subgraph(MDL_ticket *waiting_ticket, MDL_wait_for_graph_visitor *gvisitor);

        bool needs_notification(const MDL_ticket *ticket) const {
                return m_strategy->m_needs_notification ? m_strategy->m_needs_notification(ticket) : false;
        }

        void notify_conflicting_locks(MDL_context *ctx) {
                if (m_strategy->m_notify_conflicting_locks)
                        m_strategy->m_notify_conflicting_locks(ctx, this);
        }

        bool needs_connection_check() const {
                return m_strategy->m_needs_connection_check ? m_strategy->m_needs_connection_check(this) : false;
        }

        inline static bool needs_hton_notification(MDL_key::enum_mdl_namespace mdl_namespace);

        bool is_affected_by_max_write_lock_count() const {

                return key.mdl_namespace() != MDL_key::ACL_CACHE && m_strategy->m_is_affected_by_max_write_lock_count;
        }


        bool count_piglets_and_hogs(enum_mdl_type type) {
                mysql_prlock_assert_write_owner(&m_rwlock);

                if ((MDL_BIT(type) & MDL_OBJECT_HOG_LOCK_TYPES) != 0) {
                        if (m_waiting.bitmap() & ~MDL_OBJECT_HOG_LOCK_TYPES) {
                                m_hog_lock_count++;
                                if (switch_incompatible_waiting_types_bitmap_if_needed())
                                        return true;
                        }
                } else if (type == MDL_SHARED_WRITE) {
                        if (m_waiting.bitmap() & MDL_BIT(MDL_SHARED_READ_ONLY)) {
                                m_piglet_lock_count++;
                                if (switch_incompatible_waiting_types_bitmap_if_needed())
                                        return true;
                        }
                }
                return false;
        }

        inline static fast_path_state_t get_unobtrusive_lock_increment(const MDL_request *request);

        /**
          @returns "Fast path" increment if type of lock is "unobtrusive" type,
                    0 - if it is "obtrusive" type of lock.
        */
        fast_path_state_t get_unobtrusive_lock_increment(enum_mdl_type type) const {
                return m_strategy->m_unobtrusive_lock_increment[type];
        }

        /**
          Check if type of lock requested is "obtrusive" type of lock.

          @sa MDL_lock::get_unobtrusive_lock_increment() description.
        */
        bool is_obtrusive_lock(enum_mdl_type type) const {
                return get_unobtrusive_lock_increment(type) == 0;
        }


        bitmap_t fast_path_granted_bitmap() const {
                return m_strategy->m_fast_path_granted_bitmap(*this);
        }

        /** List of granted tickets for this lock. */
        Ticket_list m_granted;
        /** Tickets for contexts waiting to acquire a lock. */
        Ticket_list m_waiting;

private:

        ulong m_hog_lock_count;

        ulong m_piglet_lock_count;

        uint m_current_waiting_incompatible_idx;

public:

        MDL_lock() : m_obtrusive_locks_granted_waiting_count(0) {
                mysql_prlock_init(key_MDL_lock_rwlock, &m_rwlock);
        }

        inline void reinit(const MDL_key *mdl_key);

        ~MDL_lock() {
                mysql_prlock_destroy(&m_rwlock);
        }

        inline static MDL_lock *create(const MDL_key *key);
        inline static void destroy(MDL_lock *lock);

        inline MDL_context *get_lock_owner() const;



        inline static const MDL_lock_strategy *get_strategy(const MDL_key &key) {
                switch (key.mdl_namespace()) {
                        case MDL_key::GLOBAL:
                        case MDL_key::TABLESPACE:
                        case MDL_key::SCHEMA:
                        case MDL_key::COMMIT:
                        case MDL_key::BACKUP_LOCK:
                        case MDL_key::RESOURCE_GROUPS:
                        case MDL_key::FOREIGN_KEY:
                        case MDL_key::CHECK_CONSTRAINT:
                                return &m_scoped_lock_strategy;
                        default:
                                return &m_object_lock_strategy;
                }
        }

public:

        uint m_obtrusive_locks_granted_waiting_count;

        static const fast_path_state_t IS_DESTROYED = 1ULL << 62;

        static const fast_path_state_t HAS_OBTRUSIVE = 1ULL << 61;

        static const fast_path_state_t HAS_SLOW_PATH = 1ULL << 60;

        std::atomic<fast_path_state_t> m_fast_path_state;

        /**
          Wrapper for atomic compare-and-swap operation on m_fast_path_state member
          which enforces locking and other invariants.
        */
        bool fast_path_state_cas(fast_path_state_t *old_state, fast_path_state_t new_state) {

#if !defined(NDEBUG)
                if (((*old_state & (IS_DESTROYED | HAS_OBTRUSIVE | HAS_SLOW_PATH)) !=
                     (new_state & (IS_DESTROYED | HAS_OBTRUSIVE | HAS_SLOW_PATH))) ||
                    *old_state & HAS_OBTRUSIVE) {
                        mysql_prlock_assert_write_owner(&m_rwlock);
                }
#endif
                /*
                  We should not change state of destroyed object
                  (fast_path_state_reset() being exception).
                */
                assert(!(*old_state & IS_DESTROYED));

                return atomic_compare_exchange_strong(&m_fast_path_state, old_state, new_state);
        }


        fast_path_state_t fast_path_state_add(fast_path_state_t value) {

                mysql_prlock_assert_write_owner(&m_rwlock);

                fast_path_state_t old_state = m_fast_path_state.fetch_add(value);

                assert(!(old_state & IS_DESTROYED));
                return old_state;
        }

        /**
          Wrapper for resetting m_fast_path_state enforcing locking invariants.
        */
        void fast_path_state_reset() {
                /* HAS_DESTROYED flag can be cleared only under protection of m_rwlock. */
                mysql_prlock_assert_write_owner(&m_rwlock);
                m_fast_path_state.store(0);
        }

        const MDL_lock_strategy *m_strategy;

        static bitmap_t scoped_lock_fast_path_granted_bitmap(const MDL_lock &lock) {
                return (lock.m_fast_path_state.load() & 0xFFFFFFFFFFFFFFFULL) ? MDL_BIT(MDL_INTENTION_EXCLUSIVE) : 0;
        }

        static bool object_lock_needs_notification(const MDL_ticket *ticket) {
                return (ticket->get_type() == MDL_EXCLUSIVE);
        }
        static void object_lock_notify_conflicting_locks(MDL_context *ctx, MDL_lock *lock);

        static bitmap_t object_lock_fast_path_granted_bitmap(const MDL_lock &lock) {
                bitmap_t result       = 0;
                fast_path_state_t fps = lock.m_fast_path_state;
                if (fps & 0xFFFFFULL)
                        result |= MDL_BIT(MDL_SHARED);
                if (fps & (0xFFFFFULL << 20))
                        result |= MDL_BIT(MDL_SHARED_READ);
                if (fps & (0xFFFFFULL << 40))
                        result |= MDL_BIT(MDL_SHARED_WRITE);
                return result;
        }


        static bool object_lock_needs_connection_check(const MDL_lock *lock) {
                return (lock->key.mdl_namespace() == MDL_key::USER_LEVEL_LOCK ||
                        lock->key.mdl_namespace() == MDL_key::LOCKING_SERVICE ||
                        lock->key.mdl_namespace() == MDL_key::ACL_CACHE);
        }


        static const bitmap_t MDL_OBJECT_HOG_LOCK_TYPES =
            (MDL_BIT(MDL_SHARED_NO_WRITE) | MDL_BIT(MDL_SHARED_NO_READ_WRITE) | MDL_BIT(MDL_EXCLUSIVE));

        static const MDL_lock_strategy m_scoped_lock_strategy;
        static const MDL_lock_strategy m_object_lock_strategy;
};
```








## MDL_request
也就是通过语句解析后需要获得的 MDL Lock 的需求，然后通过这个类对象在 MDL 子系统中进行 MDL Lock 申请


```c++
class MDL_request {
public:
        /** Type of metadata lock. */
        enum_mdl_type type{MDL_INTENTION_EXCLUSIVE};
        /** Duration for requested lock. */
        enum_mdl_duration duration{MDL_STATEMENT};

        MDL_request *next_in_list{nullptr};
        MDL_request **prev_in_list{nullptr};
        MDL_ticket *ticket{nullptr};

        /** A lock is requested based on a fully qualified name and type. */
        MDL_key key;

        const char *m_src_file{nullptr};
        uint m_src_line{0};

public:
        static void *operator new(size_t size,
                                  MEM_ROOT *mem_root,
                                  const std::nothrow_t &arg [[maybe_unused]] = std::nothrow) noexcept {
                return mem_root->Alloc(size);
        }

        static void operator delete(void *, MEM_ROOT *, const std::nothrow_t &) noexcept {
        }

        void init_with_source(MDL_key::enum_mdl_namespace namespace_arg,
                              const char *db_arg,
                              const char *name_arg,
                              enum_mdl_type mdl_type_arg,
                              enum_mdl_duration mdl_duration_arg,
                              const char *src_file,
                              uint src_line);
        void init_by_key_with_source(const MDL_key *key_arg,
                                     enum_mdl_type mdl_type_arg,
                                     enum_mdl_duration mdl_duration_arg,
                                     const char *src_file,
                                     uint src_line);
        void init_by_part_key_with_source(MDL_key::enum_mdl_namespace namespace_arg,
                                          const char *part_key_arg,
                                          size_t part_key_length_arg,
                                          size_t db_length_arg,
                                          enum_mdl_type mdl_type_arg,
                                          enum_mdl_duration mdl_duration_arg,
                                          const char *src_file,
                                          uint src_line);
        /** Set type of lock request. Can be only applied to pending locks. */
        inline void set_type(enum_mdl_type type_arg) {
                assert(ticket == nullptr);
                type = type_arg;
        }


        bool is_write_lock_request() const {
                return (type >= MDL_SHARED_WRITE && type != MDL_SHARED_READ_ONLY);
        }

        /** Is this a request for a strong, DDL/LOCK TABLES-type, of lock? */
        bool is_ddl_or_lock_tables_lock_request() const {
                return type >= MDL_SHARED_UPGRADABLE;
        }

        MDL_request() = default;

        MDL_request(const MDL_request &rhs) : type(rhs.type), duration(rhs.duration), ticket(nullptr), key(rhs.key) {
        }

        MDL_request(MDL_request &&) = default;

        MDL_request &operator=(MDL_request &&) = default;
};
```




## MDL_ticket
如同门票一样，如果获取了 MDL Lock 必然给 MDL_request 返回一张门票，如果等待则不会分配。




```c++
class MDL_wait_for_subgraph {
public:
        virtual ~MDL_wait_for_subgraph();

        /**
          Accept a wait-for graph visitor to inspect the node
          this edge is leading to.
        */
        virtual bool accept_visitor(MDL_wait_for_graph_visitor *gvisitor) = 0;

        static const uint DEADLOCK_WEIGHT_CO  = 0;
        static const uint DEADLOCK_WEIGHT_DML = 25;
        static const uint DEADLOCK_WEIGHT_ULL = 50;
        static const uint DEADLOCK_WEIGHT_DDL = 100;

        /* A helper used to determine which lock request should be aborted. */
        virtual uint get_deadlock_weight() const = 0;
};


class MDL_ticket : public MDL_wait_for_subgraph {
public:
        /**
          Pointers for participating in the list of lock requests for this context.
          Context private.
        */
        MDL_ticket *next_in_context;
        MDL_ticket **prev_in_context;

        /**
          Pointers for participating in the list of satisfied/pending requests
          for the lock. Externally accessible.
        */
        MDL_ticket *next_in_lock;
        MDL_ticket **prev_in_lock;

public:
        bool has_pending_conflicting_lock() const;

        MDL_context *get_ctx() const {
                return m_ctx;
        }
        bool is_upgradable_or_exclusive() const {
                return m_type == MDL_SHARED_UPGRADABLE || m_type == MDL_SHARED_NO_WRITE || m_type == MDL_SHARED_NO_READ_WRITE ||
                       m_type == MDL_EXCLUSIVE;
        }
        enum_mdl_type get_type() const {
                return m_type;
        }
        MDL_lock *get_lock() const {
                return m_lock;
        }
        const MDL_key *get_key() const;
        void downgrade_lock(enum_mdl_type type);

        bool has_stronger_or_equal_type(enum_mdl_type type) const;

        bool is_incompatible_when_granted(enum_mdl_type type) const;
        bool is_incompatible_when_waiting(enum_mdl_type type) const;

        /** Implement MDL_wait_for_subgraph interface. */
        bool accept_visitor(MDL_wait_for_graph_visitor *dvisitor) override;
        uint get_deadlock_weight() const override;

#ifndef NDEBUG
        enum_mdl_duration get_duration() const {
                return m_duration;
        }
        void set_duration(enum_mdl_duration dur) {
                m_duration = dur;
        }
#endif

public:
        /**
          Status of lock request represented by the ticket as reflected in P_S.
        */
        enum enum_psi_status {
                PENDING = 0,
                GRANTED,
                PRE_ACQUIRE_NOTIFY,
                POST_RELEASE_NOTIFY
        };

private:
        friend class MDL_context;

        MDL_ticket(MDL_context *ctx_arg,
                   enum_mdl_type type_arg
#ifndef NDEBUG
                   ,
                   enum_mdl_duration duration_arg
#endif
                   )
            : m_type(type_arg),
#ifndef NDEBUG
              m_duration(duration_arg),
#endif
              m_ctx(ctx_arg),
              m_lock(nullptr),
              m_is_fast_path(false),
              m_hton_notified(false),
              m_psi(nullptr) {
        }

        ~MDL_ticket() override {
                assert(m_psi == nullptr);
        }

        static MDL_ticket *create(MDL_context *ctx_arg,
                                  enum_mdl_type type_arg
#ifndef NDEBUG
                                  ,
                                  enum_mdl_duration duration_arg
#endif
        );
        static void destroy(MDL_ticket *ticket);

private:
        /** Type of metadata lock. Externally accessible. */
        enum enum_mdl_type m_type;
#ifndef NDEBUG
        /**
          Duration of lock represented by this ticket.
          Context private. Debug-only.
        */
        enum_mdl_duration m_duration;
#endif
        /**
          Context of the owner of the metadata lock ticket. Externally accessible.
        */
        MDL_context *m_ctx;

        /**
          Pointer to the lock object for this lock ticket. Externally accessible.
        */
        MDL_lock *m_lock;

        /**
          Indicates that ticket corresponds to lock acquired using "fast path"
          algorithm. Particularly this means that it was not included into
          MDL_lock::m_granted bitmap/list and instead is accounted for by
          MDL_lock::m_fast_path_locks_granted_counter
        */
        bool m_is_fast_path;

        /**
          Indicates that ticket corresponds to lock request which required
          storage engine notification during its acquisition and requires
          storage engine notification after its release.
        */
        bool m_hton_notified;

        PSI_metadata_lock *m_psi;

private:
        MDL_ticket(const MDL_ticket &);            /* not implemented */
        MDL_ticket &operator=(const MDL_ticket &); /* not implemented */
};
```



## m_namespace_to_wait_state_name
所有的等待标记

```c++
PSI_stage_info MDL_key::m_namespace_to_wait_state_name[NAMESPACE_END] = {
    {0, "Waiting for global read lock", 0, PSI_DOCUMENT_ME}, // 通常就是 namespace GLOBAL 级别的 MDL Lock，通常和 flush table with read lock 有关
    {0, "Waiting for backup lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for tablespace metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for schema metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for table metadata lock", 0, PSI_DOCUMENT_ME}, // 通常就是 namespace TABLE 级别的 MDL Lock
    {0, "Waiting for stored function metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for stored procedure metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for trigger metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for event metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for commit lock", 0, PSI_DOCUMENT_ME}, // 通常就是 namespace COMMIT 级别的 MDL Lock，通常和 flush table with read lock 有关
    {0, "User lock", 0, PSI_DOCUMENT_ME}, /* Be compatible with old status. */
    {0, "Waiting for locking service lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for spatial reference system lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for acl cache lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for column statistics lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for resource groups metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for foreign key metadata lock", 0, PSI_DOCUMENT_ME},
    {0, "Waiting for check constraint metadata lock", 0, PSI_DOCUMENT_ME}};

#ifdef HAVE_PSI_INTERFACE
void MDL_key::init_psi_keys() {
        int i;
        int count;
        PSI_stage_info *info [[maybe_unused]];

        count = static_cast<int>(array_elements(MDL_key::m_namespace_to_wait_state_name));
        for (i = 0; i < count; i++) {
                /* mysql_stage_register wants an array of pointers, registering 1 by 1. */
                info = &MDL_key::m_namespace_to_wait_state_name[i];
                mysql_stage_register("sql", &info, 1);
        }
}
#endif
```









