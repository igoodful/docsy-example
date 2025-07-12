---
title: 06. my_init
description: my_init
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

- 支持字符集列表：/root/mysql-server-8032-google-02/mysys/charset-def.cc


{{< /alert >}}


## 整体情况
```viz-dot
digraph "my_init" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "my_init" -> "my_init_done";
        "my_init_done" -> "my_umask=0640";
        "my_umask=0640" -> "my_umask_dir=0750";
        "my_umask_dir=0750" -> "UMASK";
        "UMASK" -> "UMASK_DIR";
        "UMASK_DIR" -> "my_thread_global_init()" ;
        "my_thread_global_init()" -> "my_thread_init()" ;
        "my_thread_init()" -> "HOME" ;
        "HOME" -> "home_dir" ;
        "home_dir" -> "MyFileInit()" ;

}
```


## 源码
```c++
bool my_init() {
        char *str;

        if (my_init_done)
                return false;

        my_init_done = true;

#if defined(MY_MSCRT_DEBUG)
        set_crt_report_leaks();
#endif
        // 新建文件权限为：0640
        my_umask     = 0640;
        // 新建目录权限为：0750
        my_umask_dir = 0750;

        // 获取环境变量：UMASK
        if ((str = getenv("UMASK")) != nullptr)
                my_umask = (int)(atoi_octal(str) | 0600);
        // 获取环境变量：UMASK_DIR
        if ((str = getenv("UMASK_DIR")) != nullptr)
                my_umask_dir = (int)(atoi_octal(str) | 0700);

        if (my_thread_global_init())
                return true;

        if (my_thread_init())
                return true;

        /* $HOME is needed early to parse configuration files located in ~/ */
        if ((home_dir = getenv("HOME")) != nullptr)
                home_dir = intern_filename(home_dir_buff, home_dir);

        {
                DBUG_TRACE;
                DBUG_PROCESS(my_progname ? my_progname : "unknown");
#ifdef _WIN32
                my_win_init();
#endif
                MyFileInit();

                DBUG_PRINT("exit", ("home: '%s'", home_dir));
                return false;
        }
}
```

- my_init_done 是一个全局布尔变量，用于标记 MySQL 初始化是否已经完成。如果已经完成初始化，则直接返回 false，表示初始化不需要再次执行。

- my_umask 和 my_umask_dir 分别用于设置新文件和新目录的默认权限掩码。如果系统环境变量中存在名为 UMASK 和 UMASK_DIR 的变量，则会读取其值并进行相应的设置，否则将采用默认的权限值。

- my_thread_global_init() 和 my_thread_init() 分别用于初始化 MySQL 线程全局变量和线程相关的功能。如果初始化失败，则返回 true，表示初始化过程出错。

- 获取环境变量 HOME 的值，该值通常表示用户的主目录路径。这个路径可能在解析配置文件时会用到。

总体来说，这段代码主要完成了 MySQL 的初始化工作，包括设置全局变量的默认值、读取系统环境变量、初始化线程相关的功能和文件系统相关的功能等。




### my_thread_global_init

```c++
bool my_thread_global_init() {
        if (my_thread_global_init_done)
                return false;
        my_thread_global_init_done = true;

#if defined(SAFE_MUTEX)
        safe_mutex_global_init();
#endif

#ifdef PTHREAD_ADAPTIVE_MUTEX_INITIALIZER_NP
        // adaptive
        pthread_mutexattr_init(&my_fast_mutexattr);
        // PTHREAD_MUTEX_ADAPTIVE_NP=3，将互斥锁类型设置为自适应类型，允许线程在等待互斥锁时尝试偷取其他线程的互斥锁，从而避免了不必要的线程上下文切换，提高了性能。
        // 但是需要注意的是，在高并发情况下，可能会导致某些线程长时间无法获取到互斥锁，造成饥饿现象
        pthread_mutexattr_settype(&my_fast_mutexattr, PTHREAD_MUTEX_ADAPTIVE_NP);
#endif

#ifdef PTHREAD_ERRORCHECK_MUTEX_INITIALIZER_NP
        // errorcheck
        pthread_mutexattr_init(&my_errorcheck_mutexattr);
        // PTHREAD_MUTEX_ERRORCHECK=2，将互斥锁类型设置为错误检查类型。这种类型的互斥锁在同一线程多次尝试对同一个互斥锁进行加锁时会返回错误码，而不会导致死锁
        pthread_mutexattr_settype(&my_errorcheck_mutexattr, PTHREAD_MUTEX_ERRORCHECK);
#endif

        mysql_mutex_init(key_THR_LOCK_malloc, &THR_LOCK_malloc, MY_MUTEX_INIT_FAST);
        mysql_mutex_init(key_THR_LOCK_open, &THR_LOCK_open, MY_MUTEX_INIT_FAST);
        mysql_mutex_init(key_THR_LOCK_charset, &THR_LOCK_charset, MY_MUTEX_INIT_FAST);
        mysql_mutex_init(key_THR_LOCK_lock, &THR_LOCK_lock, MY_MUTEX_INIT_FAST);
        mysql_mutex_init(key_THR_LOCK_myisam, &THR_LOCK_myisam, MY_MUTEX_INIT_SLOW);
        mysql_mutex_init(key_THR_LOCK_myisam_mmap, &THR_LOCK_myisam_mmap, MY_MUTEX_INIT_FAST);
        mysql_mutex_init(key_THR_LOCK_heap, &THR_LOCK_heap, MY_MUTEX_INIT_FAST);
        mysql_mutex_init(key_THR_LOCK_net, &THR_LOCK_net, MY_MUTEX_INIT_FAST);
#ifndef NDEBUG
        mysql_mutex_init(key_THR_LOCK_threads, &THR_LOCK_threads, MY_MUTEX_INIT_FAST);
        mysql_cond_init(key_THR_COND_threads, &THR_COND_threads);
#endif

        return false;
}
```

#### safe_mutex_global_init
这段代码的目的是确保安全互斥锁在程序运行过程中只进行一次全局初始化，以避免重复初始化导致的问题。通过检查 safe_mutex_inited 的值，可以确保初始化仅在第一次调用时执行
```c++
// 初始值为 false，表示尚未初始化
static bool safe_mutex_inited = false;
// 在函数内部，将 safe_mutex_inited 设置为 true，表示初始化已完成
void safe_mutex_global_init(void) {
        safe_mutex_inited = true;
}
```




#### 设置线程属性

| 值 | 名称                        | 别名                                       | 说明                                                         |
|:---|:----------------------------|:-------------------------------------------|:-----------------------------------------------------------|
| 0  | PTHREAD_MUTEX_TIMED_NP      | PTHREAD_MUTEX_NORMAL/PTHREAD_MUTEX_FAST_NP | 普通锁，默认值，加锁后任何其他或本线程的加锁都会阻塞           |
| 1  | PTHREAD_MUTEX_RECURSIVE_NP  | PTHREAD_MUTEX_RECURSIVE                    | 嵌套锁，允许同线程内对同一个锁加锁多次，记录加锁次数           |
| 2  | PTHREAD_MUTEX_ERRORCHECK_NP | PTHREAD_MUTEX_ERRORCHECK                   | 检错锁，禁止同线程内对同一个锁加锁多次                        |
| 3  | PTHREAD_MUTEX_ADAPTIVE_NP   | PTHREAD_MUTEX_DEFAULT                      | 适应锁，效率更高，等同于多次trylock() + PTHREAD_MUTEX_TIMED_NP |



```c++
typedef pthread_mutex_t native_mutex_t;
typedef pthread_mutexattr_t native_mutexattr_t;

#ifdef PTHREAD_ADAPTIVE_MUTEX_INITIALIZER_NP
native_mutexattr_t my_fast_mutexattr;
#endif
#ifdef PTHREAD_ERRORCHECK_MUTEX_INITIALIZER_NP
native_mutexattr_t my_errorcheck_mutexattr;
#endif

enum
{
  PTHREAD_MUTEX_TIMED_NP,
  PTHREAD_MUTEX_RECURSIVE_NP,
  PTHREAD_MUTEX_ERRORCHECK_NP,
  PTHREAD_MUTEX_ADAPTIVE_NP
#if defined __USE_UNIX98 || defined __USE_XOPEN2K8
  ,
  PTHREAD_MUTEX_NORMAL = PTHREAD_MUTEX_TIMED_NP,
  PTHREAD_MUTEX_RECURSIVE = PTHREAD_MUTEX_RECURSIVE_NP,
  PTHREAD_MUTEX_ERRORCHECK = PTHREAD_MUTEX_ERRORCHECK_NP,
  PTHREAD_MUTEX_DEFAULT = PTHREAD_MUTEX_NORMAL
#endif
#ifdef __USE_GNU
  /* For compatibility.  */
  , PTHREAD_MUTEX_FAST_NP = PTHREAD_MUTEX_TIMED_NP
#endif
};

// pthread,互斥锁的初始化
extern int pthread_mutexattr_init (pthread_mutexattr_t *__attr)
     __THROW __nonnull ((1));
extern int pthread_mutexattr_settype (pthread_mutexattr_t *__attr, int __kind)
     __THROW __nonnull ((1));
```
- pthread_mutexattr_init(&my_fast_mutexattr);
- pthread_mutexattr_settype(&my_fast_mutexattr, PTHREAD_MUTEX_ADAPTIVE_NP);
- pthread_mutexattr_init(&my_errorcheck_mutexattr);
- pthread_mutexattr_settype(&my_errorcheck_mutexattr, PTHREAD_MUTEX_ERRORCHECK);




#### mysql_mutex_init

**mysql_mutex_t**：
```viz-dot
digraph "mysql_mutex_t" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "mysql_mutex_t" -> "my_mutex_t" [label="m_mutex"];
        "mysql_mutex_t" -> "PSI_mutex" [label="m_psi"];
        "my_mutex_t" -> "native_mutex_t" [label="pthread_mutex_t"];
        "my_mutex_t" -> "safe_mutex_t";
        "safe_mutex_t" -> "file" [label="const char *"];
        "safe_mutex_t" -> "line" [label="uint"];
        "safe_mutex_t" -> "count" [label="uint"];
        "safe_mutex_t" -> "thread" [label="my_thread_t=pthread_t "];
        "safe_mutex_t" -> "global" [label="native_mutex_t=pthread_mutex_t "];
        "safe_mutex_t" -> "mutex" [label="native_mutex_t=pthread_mutex_t "];
}
```

**PSI_mutex_service_t**：
```viz-dot
digraph "PSI_mutex_service_t" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "PSI_mutex_service_t" -> "PSI_mutex_service_v1";
        "PSI_mutex_service_v1" -> "register_mutex" [label="register_mutex_v1_t"];
        "PSI_mutex_service_v1" -> "init_mutex" [label="init_mutex_v1_t"];
        "PSI_mutex_service_v1" -> "destroy_mutex" [label="destroy_mutex_v1_t"];
        "PSI_mutex_service_v1" -> "start_mutex_wait" [label="start_mutex_wait_v1_t"];
        "PSI_mutex_service_v1" -> "end_mutex_wait" [label="end_mutex_wait_v1_t"];
        "PSI_mutex_service_v1" -> "unlock_mutex" [label="unlock_mutex_v1_t"];
}
```



##### 初始化各个全局互斥锁对象

| 全局锁类型           | 功能                           | 说明 |
|:---------------------|:-----------------------------|:-----|
| THR_LOCK_malloc      | 内存分配互斥锁                 |      |
| THR_LOCK_open        | 文件打开互斥锁                 |      |
| THR_LOCK_charset     | 字符集相关操作的互斥锁         |      |
| THR_LOCK_lock        | 表锁相关操作的互斥锁           |      |
| THR_LOCK_myisam      | MyISAM 存储引擎的互斥锁        |      |
| THR_LOCK_myisam_mmap | MyISAM表的内存映射操作         |      |
| THR_LOCK_heap        | 堆操作互斥锁                   |      |
| THR_LOCK_net         | 网络操作互斥锁                 |      |
| THR_LOCK_threads     | 用于线程同步的互斥锁和条件变量 |      |
| THR_COND_threads     | 用于线程同步的互斥锁和条件变量 |      |



```c++
mysql_mutex_init(key_THR_LOCK_malloc, &THR_LOCK_malloc, MY_MUTEX_INIT_FAST);
// inline_mysql_mutex_init(key_THR_LOCK_open, &THR_LOCK_open, &my_fast_mutexattr, "/root/mysql-server-8032-google-02/mysys/my_thr_init.cc", 192)
mysql_mutex_init(key_THR_LOCK_open, &THR_LOCK_open, MY_MUTEX_INIT_FAST);
mysql_mutex_init(key_THR_LOCK_charset, &THR_LOCK_charset, MY_MUTEX_INIT_FAST);
mysql_mutex_init(key_THR_LOCK_lock, &THR_LOCK_lock, MY_MUTEX_INIT_FAST);
mysql_mutex_init(key_THR_LOCK_myisam, &THR_LOCK_myisam, MY_MUTEX_INIT_SLOW);
mysql_mutex_init(key_THR_LOCK_myisam_mmap, &THR_LOCK_myisam_mmap, MY_MUTEX_INIT_FAST);
mysql_mutex_init(key_THR_LOCK_heap, &THR_LOCK_heap, MY_MUTEX_INIT_FAST);
mysql_mutex_init(key_THR_LOCK_net, &THR_LOCK_net, MY_MUTEX_INIT_FAST);
mysql_mutex_init(key_THR_LOCK_threads, &THR_LOCK_threads, MY_MUTEX_INIT_FAST);
mysql_cond_init(key_THR_COND_threads, &THR_COND_threads);
```
最后，返回 false，表示初始化完成。

总的来说，该函数负责在 MySQL 中初始化全局线程相关资源，包括设置互斥锁类型和初始化各种全局互斥锁对象


```c++

#define MY_MUTEX_INIT_FAST &my_fast_mutexattr

typedef unsigned int PSI_mutex_key;

typedef unsigned int PSI_cond_key;

struct mysql_mutex_t {
        my_mutex_t m_mutex;
        struct PSI_mutex *m_psi{nullptr};
};

#ifdef _WIN32
typedef CRITICAL_SECTION native_mutex_t;
typedef int native_mutexattr_t;
#else
typedef pthread_mutex_t native_mutex_t;
typedef pthread_mutexattr_t native_mutexattr_t;
#endif

struct safe_mutex_t;

struct my_mutex_t {
        union u {
                native_mutex_t m_native;
                safe_mutex_t *m_safe_ptr;
        } m_u;
};
typedef struct my_mutex_t my_mutex_t;

#endif
// 全局变量
PSI_mutex_key key_IO_CACHE_append_buffer_lock, key_IO_CACHE_SHARE_mutex, key_KEY_CACHE_cache_lock, key_THR_LOCK_charset,
    key_THR_LOCK_heap, key_THR_LOCK_lock, key_THR_LOCK_malloc, key_THR_LOCK_mutex, key_THR_LOCK_myisam, key_THR_LOCK_net,
    key_THR_LOCK_open, key_THR_LOCK_threads, key_TMPDIR_mutex, key_THR_LOCK_myisam_mmap;

PSI_cond_key key_IO_CACHE_SHARE_cond, key_IO_CACHE_SHARE_cond_writer, key_THR_COND_threads;

// 全局变量
mysql_mutex_t THR_LOCK_myisam_mmap;
mysql_mutex_t THR_LOCK_myisam;
mysql_mutex_t THR_LOCK_heap;
mysql_mutex_t THR_LOCK_malloc;
mysql_mutex_t THR_LOCK_open;
mysql_mutex_t THR_LOCK_lock;
mysql_mutex_t THR_LOCK_net;
mysql_mutex_t THR_LOCK_charset;
#ifndef NDEBUG
mysql_mutex_t THR_LOCK_threads;
mysql_cond_t THR_COND_threads;
#endif


#define mysql_mutex_init(K, M, A) mysql_mutex_init_with_src(K, M, A, __FILE__, __LINE__)

#define mysql_mutex_init_with_src(K, M, A, F, L) inline_mysql_mutex_init(K, M, A, F, L)

#ifndef PSI_MUTEX_CALL
#define PSI_MUTEX_CALL(M) psi_mutex_service->M
#endif

static void register_mutex_noop(const char *, PSI_mutex_info *, int) {
        return;
}

static PSI_mutex *init_mutex_noop(PSI_mutex_key, const void *) {
        return nullptr;
}

static void destroy_mutex_noop(PSI_mutex *) {
        return;
}

static PSI_mutex_locker *start_mutex_wait_noop(PSI_mutex_locker_state *, PSI_mutex *, PSI_mutex_operation, const char *, uint) {
        return nullptr;
}

static void end_mutex_wait_noop(PSI_mutex_locker *, int) {
        return;
}

static void unlock_mutex_noop(PSI_mutex *) {
        return;
}

struct PSI_mutex_info_v1 {
        /**
          Pointer to the key assigned to the registered mutex.
        */
        PSI_mutex_key *m_key;
        /**
          The name of the mutex to register.
        */
        const char *m_name;
        /**
          The flags of the mutex to register.
          @sa PSI_FLAG_SINGLETON
        */
        unsigned int m_flags;
        /** Volatility index. */
        int m_volatility;
        /** Documentation. */
        const char *m_documentation;
};

typedef void (*register_mutex_v1_t)(const char *category, struct PSI_mutex_info_v1 *info, int count);
typedef struct PSI_mutex *(*init_mutex_v1_t)(PSI_mutex_key key, const void *identity);
typedef void (*destroy_mutex_v1_t)(struct PSI_mutex *mutex);
typedef void (*unlock_mutex_v1_t)(struct PSI_mutex *mutex);
typedef struct PSI_mutex_locker *(*start_mutex_wait_v1_t)(struct PSI_mutex_locker_state_v1 *state,
                                                          struct PSI_mutex *mutex,
                                                          enum PSI_mutex_operation op,
                                                          const char *src_file,
                                                          unsigned int src_line);
typedef void (*end_mutex_wait_v1_t)(struct PSI_mutex_locker *locker, int rc);

typedef struct PSI_mutex_info_v1 PSI_mutex_info_v1;
typedef PSI_mutex_info_v1 PSI_mutex_info;
typedef struct PSI_mutex_locker_state_v1 PSI_mutex_locker_state;

struct PSI_mutex_service_v1 {
        /** @sa register_mutex_v1_t. */
        register_mutex_v1_t register_mutex;
        /** @sa init_mutex_v1_t. */
        init_mutex_v1_t init_mutex;
        /** @sa destroy_mutex_v1_t. */
        destroy_mutex_v1_t destroy_mutex;
        /** @sa start_mutex_wait_v1_t. */
        start_mutex_wait_v1_t start_mutex_wait;
        /** @sa end_mutex_wait_v1_t. */
        end_mutex_wait_v1_t end_mutex_wait;
        /** @sa unlock_mutex_v1_t. */
        unlock_mutex_v1_t unlock_mutex;
};

typedef struct PSI_mutex_service_v1 PSI_mutex_service_t;

static PSI_mutex_service_t psi_mutex_noop =
        {register_mutex_noop, init_mutex_noop, destroy_mutex_noop, start_mutex_wait_noop, end_mutex_wait_noop, unlock_mutex_noop};

PSI_mutex_service_t *psi_mutex_service     = &psi_mutex_noop;

// 用于初始化一个 MySQL 互斥锁
// inline_mysql_mutex_init(key_THR_LOCK_open, &THR_LOCK_open, &my_fast_mutexattr, "/root/mysql-server-8032-google-02/mysys/my_thr_init.cc", 192)
static inline int inline_mysql_mutex_init(PSI_mutex_key key [[maybe_unused]],
                                          mysql_mutex_t *that,
                                          const native_mutexattr_t *attr,
                                          const char *src_file [[maybe_unused]],
                                          uint src_line [[maybe_unused]]) {
        #ifdef HAVE_PSI_MUTEX_INTERFACE
        that->m_psi = PSI_MUTEX_CALL(init_mutex)(key, &that->m_mutex);
        #else
        that->m_psi = nullptr;
        #endif
        return my_mutex_init(&that->m_mutex,
                             attr
        #ifdef SAFE_MUTEX
                             ,
                             src_file,
                             src_line
        #endif
        );
}

static inline int my_mutex_init(my_mutex_t *mp,
                                const native_mutexattr_t *attr
#ifdef SAFE_MUTEX
                                ,
                                const char *file,
                                uint line
#endif
) {
#ifdef SAFE_MUTEX
        assert(mp != nullptr);
        mp->m_u.m_safe_ptr = (safe_mutex_t *)malloc(sizeof(safe_mutex_t));
        return safe_mutex_init(mp->m_u.m_safe_ptr, attr, file, line);
#else
        return native_mutex_init(&mp->m_u.m_native, attr);
#endif
}


```

##### 初始化一个 MySQL 互斥锁
```c++
// 用于初始化一个 MySQL 互斥锁
// inline_mysql_mutex_init(key_THR_LOCK_open, &THR_LOCK_open, &my_fast_mutexattr, "/root/mysql-server-8032-google-02/mysys/my_thr_init.cc", 192)
static inline int inline_mysql_mutex_init(PSI_mutex_key key [[maybe_unused]],
                                          mysql_mutex_t *that,
                                          const native_mutexattr_t *attr,
                                          const char *src_file [[maybe_unused]],
                                          uint src_line [[maybe_unused]]) {
        #ifdef HAVE_PSI_MUTEX_INTERFACE
        that->m_psi = PSI_MUTEX_CALL(init_mutex)(key, &that->m_mutex);
        #else
        that->m_psi = nullptr;
        #endif
        return my_mutex_init(&that->m_mutex,
                             attr
        #ifdef SAFE_MUTEX
                             ,
                             src_file,
                             src_line
        #endif
        );
}

static inline int my_mutex_init(my_mutex_t *mp,
                                const native_mutexattr_t *attr
#ifdef SAFE_MUTEX
                                ,
                                const char *file,
                                uint line
#endif
) {
#ifdef SAFE_MUTEX
        assert(mp != nullptr);
        mp->m_u.m_safe_ptr = (safe_mutex_t *)malloc(sizeof(safe_mutex_t));
        return safe_mutex_init(mp->m_u.m_safe_ptr, attr, file, line);
#else
        return native_mutex_init(&mp->m_u.m_native, attr);
#endif
}

int safe_mutex_init(safe_mutex_t *mp, const native_mutexattr_t *attr, const char *file, uint line) {
        assert(safe_mutex_inited);
        memset(mp, 0, sizeof(*mp));
        native_mutex_init(&mp->global, MY_MUTEX_INIT_ERRCHK);
        native_mutex_init(&mp->mutex, attr);
        /* Mark that mutex is initialized */
        mp->file = file;
        mp->line = line;
        return 0;
}

static inline int native_mutex_init(native_mutex_t *mutex, const native_mutexattr_t *attr [[maybe_unused]]) {
#ifdef _WIN32
        InitializeCriticalSection(mutex);
        return 0;
#else
        return pthread_mutex_init(mutex, attr);
#endif
}

/* Initialize a mutex.  */
extern int pthread_mutex_init (pthread_mutex_t *__mutex,
			       const pthread_mutexattr_t *__mutexattr)
     __THROW __nonnull ((1));


```
这段代码是 inline_mysql_mutex_init 函数的实现，用于初始化一个 MySQL 互斥锁。下面是对代码的详细解释：

inline_mysql_mutex_init 函数接受以下参数：

- key: 互斥锁的键值，用于 PSI (Performance Schema Instrumentation) 接口。
- that: 要初始化的互斥锁对象。
- attr: 用于指定互斥锁的属性的指针。
- src_file 和 src_line: 可选参数，用于在安全模式下记录互斥锁的来源文件和行号。

- 在函数体内部，使用条件编译来决定是否启用 PSI 互斥锁接口。如果启用了 PSI 接口，则调用 PSI_MUTEX_CALL(init_mutex) 函数来初始化互斥锁，并将返回的 PSI 句柄赋值给 that->m_psi。否则，将 that->m_psi 置为 nullptr。

- 最后，调用 my_mutex_init 函数来初始化实际的互斥锁对象 that->m_mutex，并将属性 attr 传递给它。在安全模式下，还会传递源文件和行号信息。

这段代码的作用是在 MySQL 中封装了对互斥锁的初始化操作，根据是否启用 PSI 接口来选择不同的初始化方式，并在需要时记录源文件和行号信息。










#### mysql_cond_init





## my_thread_init()


```c++
mysql_mutex_t THR_LOCK_threads;
mysql_cond_t THR_COND_threads;

extern "C" bool my_thread_init() {
#ifndef NDEBUG
        struct st_my_thread_var *tmp;
#endif

        if (!my_thread_global_init_done)
                return true; /* cannot proceed with uninitialized library */

#ifdef _WIN32
        install_sigabrt_handler();
#endif

#ifndef NDEBUG
        if (mysys_thread_var())
                return false;

        if (!(tmp = (struct st_my_thread_var *)calloc(1, sizeof(*tmp))))
                return true;

        mysql_mutex_lock(&THR_LOCK_threads);
        tmp->id = ++thread_id;
        ++THR_thread_count;
        mysql_mutex_unlock(&THR_LOCK_threads);
        set_mysys_thread_var(tmp);
#endif

        return false;
}

struct st_my_thread_var {
        my_thread_id id;
        struct CODE_STATE *dbug;
};

static thread_local st_my_thread_var *THR_mysys = nullptr;

static struct st_my_thread_var *mysys_thread_var() {
        return THR_mysys;
}

static inline int inline_mysql_mutex_lock(mysql_mutex_t *that,
                                          const char *src_file [[maybe_unused]],
                                          uint src_line [[maybe_unused]]) {
        int result;

        #ifdef HAVE_PSI_MUTEX_INTERFACE
        if (that->m_psi != nullptr) {
                if (that->m_psi->m_enabled) {
                        /* Instrumentation start */
                        PSI_mutex_locker *locker;
                        PSI_mutex_locker_state state;
                        locker = PSI_MUTEX_CALL(start_mutex_wait)(&state, that->m_psi, PSI_MUTEX_LOCK, src_file, src_line);

                        /* Instrumented code */
                        result = my_mutex_lock(&that->m_mutex
                #ifdef SAFE_MUTEX
                                               ,
                                               src_file,
                                               src_line
                #endif
                        );

                        /* Instrumentation end */
                        if (locker != nullptr) {
                                PSI_MUTEX_CALL(end_mutex_wait)(locker, result);
                        }

                        return result;
                }
        }
        #endif

        /* Non instrumented code */
        result = my_mutex_lock(&that->m_mutex
        #ifdef SAFE_MUTEX
                               ,
                               src_file,
                               src_line
        #endif
        );

        return result;
}
```


```c++
int safe_mutex_lock(safe_mutex_t *mp, bool try_lock, const char *file, uint line) {
        int error;
        native_mutex_lock(&mp->global);
        if (!mp->file) {
                native_mutex_unlock(&mp->global);
                fprintf(stderr, "safe_mutex: Trying to lock uninitialized mutex at %s, line %d\n", file, line);
                fflush(stderr);
                abort();
        }

        if (mp->count > 0) {
                if (try_lock) {
                        native_mutex_unlock(&mp->global);
                        return EBUSY;
                } else if (my_thread_equal(my_thread_self(), mp->thread)) {
        #ifndef NDEBUG
                        fprintf(stderr,
                                "safe_mutex: Trying to lock mutex at %s, line %d, when the"
                                " mutex was already locked at %s, line %d in thread T@%u\n",
                                file,
                                line,
                                mp->file,
                                mp->line,
                                my_thread_var_id());
                        fflush(stderr);
        #endif
                        abort();
                }
        }
        native_mutex_unlock(&mp->global);

        if (try_lock) {
                error = native_mutex_trylock(&mp->mutex);
                if (error == EBUSY)
                        return error;
        } else
                error = native_mutex_lock(&mp->mutex);

        if (error || (error = native_mutex_lock(&mp->global))) {
                fprintf(stderr, "Got error %d when trying to lock mutex at %s, line %d\n", error, file, line);
                fflush(stderr);
                abort();
        }
        mp->thread = my_thread_self();
        if (mp->count++) {
                fprintf(stderr,
                        "safe_mutex: Error in thread libray: Got mutex at %s, \
line %d more than 1 time\n",
                        file,
                        line);
                fflush(stderr);
                abort();
        }
        mp->file = file;
        mp->line = line;
        native_mutex_unlock(&mp->global);
        return error;
}
```












