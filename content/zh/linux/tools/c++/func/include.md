---
title: 预处理
description: 预处理
date: 2023-11-17
weight: 30000


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- 包含文件：由于它们被包含在其他文件中
- 头文件：由于它们被包含在文件起始处
- c头文件以.h结尾，而c++头文件没有拓展名，且可包含名称空间
- c头文件转换为c++头文件：math.h》cmath
- 预处理器只在编译阶段处理

{{< /alert >}}


{{<alert>}}

1. xxx.h和xxx.c都存在，若xxx.c包含xxx.h，则必须先包含xxx.h，再包含其他头文件
2. 查看预处理后的文件：`g++ -E my.cc -o my.i`

{{</alert>}}

## 预处理器

```viz-dot
digraph "预处理器" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "预处理器" -> "#include" [label="文件包含"];
        "预处理器" -> "#if" [label="条件编译"];
        "预处理器" -> "#elif " [label="条件编译"];
        "预处理器" -> "#else" [label="条件编译"];
        "预处理器" -> "#endif" [label="条件编译"];
        "预处理器" -> "#ifdef" [label="宏定义"];
        "预处理器" -> "#ifndef" [label="未定义宏"];
        "预处理器" -> "#define" [label="定义了宏"];
        "预处理器" -> "#undef" [label="取消宏定义"];
        "预处理器" -> "#include_next" [label="宏定义"];
        "预处理器" -> "#line" [label="宏定义"];
        "预处理器" -> "#" [label="字符串化"];
        "预处理器" -> "##" [label="字符串拼接"];
        "预处理器" -> "#pragma" [label="宏定义"];
        "预处理器" -> "#warning" [label="宏定义"];
        "预处理器" -> "#error" [label="宏定义"];
        "预处理器" -> "#using" [label="using"];
}
```


#### `#include`

```viz-dot
digraph "#include" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "#include" -> "作用" [label="文件替换"];
        "#include" -> "文件名" [label="xxx/cxxx/xxx.h"];
        "#include" -> "结果 " [label="g++ -E xxx.cc -o xxx.i"];
        "#include" -> "搜索路径" [label="标准路径"];

}
```

{{<alert>}}

1. xxx.h和xxx.c都存在，若xxx.c包含xxx.h，则必须先包含xxx.h，再包含其他头文件
2. 查看预处理后的文件：`g++ -E my.cc -o my.i`
3. `#include <xxx>`本质：`#include <xxx>`将被替换成`xxx`文件的内容，即将`xxx`文件的内容和源代码文件合并成一个复合文件，编译的下一个阶段会使用它

{{</alert>}}


> 项目头文件以项目根路径为基准
>
>

```c++
// 标准库和第三方库
#include <string.h>
#include <sys/types.h>
#include <time.h>
#include <atomic>
#include <utility>

// 项目include
#include "my_io.h"
#include "my_sys.h"
#include "mysql/components/services/bits/mysql_cond_bits.h"
#include "mysql/components/services/bits/mysql_mutex_bits.h"


// 项目子include目录，但需要指定头文件路径
#include "btr0cur.h"
// 项目非include目录
#include "sql/thr_malloc.h"
#include "sql/trigger_def.h"
```





#### `#define`

```c++
//
#define GET_NO_ARG 1
#define GET_BOOL 2
#define INT_MAX64 0x7FFFFFFFFFFFFFFFLL
#define INT_MIN32 (~0x7FFFFFFFL)

//
#define T_DESCRIPT (1UL << 7)
#define T_DONT_CHECK_CHECKSUM (1UL << 8)

// 宏函数最好都加上括号
#define NET_EXTENSION_PTR(N) ((NET_EXTENSION *)((N)->extension ? (N)->extension : NULL))
#define NET_ASYNC_DATA(M) ((NET_EXTENSION_PTR(M)) ? (NET_EXTENSION_PTR(M)->net_async_context) : NULL)
#define rot(x, k) (((x)<<(k)) ^ ((x)>>(32-(k))))

// #ifndef
#ifndef MY_TABLE_MAP_INCLUDED
#define MY_TABLE_MAP_INCLUDED

#include <stdint.h>

using table_map   = uint64_t;
using nesting_map = uint64_t;
using qep_tab_map = uint64_t;

static inline bool ContainsTable(uint64_t map, unsigned idx) {
        return map & (uint64_t{1} << idx);
}

#endif

//
```

#### `#undef`

```c++
static char *fieldflags2str(uint f) {
        static char buf[1024];
        char *s = buf;
        *s      = 0;
#define ff2s_check_flag(X)    \
  if (f & X##_FLAG) {         \
    s = my_stpcpy(s, #X " "); \
    f &= ~X##_FLAG;           \
  }
        ff2s_check_flag(NOT_NULL);
        ff2s_check_flag(PRI_KEY);
        ff2s_check_flag(UNIQUE_KEY);
        ff2s_check_flag(MULTIPLE_KEY);
        ff2s_check_flag(BLOB);
        ff2s_check_flag(UNSIGNED);
        ff2s_check_flag(ZEROFILL);
        ff2s_check_flag(BINARY);
        ff2s_check_flag(ENUM);
        ff2s_check_flag(AUTO_INCREMENT);
        ff2s_check_flag(TIMESTAMP);
        ff2s_check_flag(SET);
        ff2s_check_flag(NO_DEFAULT_VALUE);
        ff2s_check_flag(NUM);
        ff2s_check_flag(PART_KEY);
        ff2s_check_flag(GROUP);
        ff2s_check_flag(UNIQUE);
        ff2s_check_flag(BINCMP);
        ff2s_check_flag(ON_UPDATE_NOW);
#undef ff2s_check_flag
        if (f)
                sprintf(s, " unknown=0x%04x", f);
        return buf;
}


#define EVENT NDBStartStarted
                case NDB_LE_NDBStartStarted:
                        ndbout_c("Node %u: Start initiated (version %d.%d.%d)", R, QVERSION);
                        break;
#undef EVENT
#define EVENT NDBStartCompleted
                case NDB_LE_NDBStartCompleted:
                        ndbout_c("Node %u: Started (version %d.%d.%d)", R, QVERSION);
                        break;
#undef EVENT
#define EVENT NDBStopStarted
                case NDB_LE_NDBStopStarted:
                        ndbout_c("Node %u: %s shutdown initiated", R, (Q(stoptype) == 1 ? "Cluster" : "Node"));
                        break;
#undef EVENT
```


#### `#ifdef`

```c++
#ifdef __cplusplus
extern "C" {
#endif

struct curl_ws_frame {
        int age;
        int flags;
        curl_off_t offset;
        curl_off_t bytesleft;
};

#define CURLWS_TEXT       (1<<0)
#define CURLWS_BINARY     (1<<1)
#define CURLWS_CONT       (1<<2)
#define CURLWS_CLOSE      (1<<3)
#define CURLWS_PING       (1<<4)
#define CURLWS_OFFSET     (1<<5)

CURL_EXTERN CURLcode curl_ws_recv(CURL *curl, void *buffer, size_t buflen, size_t *recv, struct curl_ws_frame **metap);
#define CURLWS_PONG       (1<<6)
CURL_EXTERN CURLcode
curl_ws_send(CURL *curl, const void *buffer, size_t buflen, size_t *sent, curl_off_t framesize, unsigned int sendflags);

#define CURLWS_RAW_MODE (1<<0)

CURL_EXTERN struct curl_ws_frame *curl_ws_meta(CURL *curl);

#ifdef __cplusplus
}
#endif

#endif
```
#### `#ifndef`
最常见使用
```c++
#ifndef DISABLED_OPTION_INCLUDED
#define DISABLED_OPTION_INCLUDED

#include "client/base/abstract_option.h"
#include "my_getopt.h"

namespace Mysql {
namespace Tools {
namespace Base {
namespace Options {
class Disabled_option : public Abstract_option<Disabled_option> {
public:
        Disabled_option(std::string name, std::string description);
};

}  // namespace Options
}  // namespace Base
}  // namespace Tools
}  // namespace Mysql

#endif
```


举例说明：
```c++
LINE_BUFFER *batch_readline_init(ulong max_size, FILE *file) {
        LINE_BUFFER *line_buff;

#ifndef _WIN32
        MY_STAT input_file_stat;
        if (my_fstat(fileno(file), &input_file_stat) || MY_S_ISDIR(input_file_stat.st_mode) ||
            MY_S_ISBLK(input_file_stat.st_mode))
                return nullptr;
#endif

        if (!(line_buff = (LINE_BUFFER *)my_malloc(PSI_NOT_INSTRUMENTED, sizeof(*line_buff), MYF(MY_WME | MY_ZEROFILL))))
                return nullptr;
        if (init_line_buffer(line_buff, my_fileno(file), batch_io_size, max_size)) {
                my_free(line_buff);
                return nullptr;
        }
        return line_buff;
}

// class
class Previous_gtids_log_event : public binary_log::Previous_gtids_event, public Log_event {
public:
#ifdef MYSQL_SERVER
        Previous_gtids_log_event(const Gtid_set *set);
#endif

#ifdef MYSQL_SERVER
        int pack_info(Protocol *) override;
#endif

        Previous_gtids_log_event(const char *buf, const Format_description_event *description_event);
        ~Previous_gtids_log_event() override = default;

        size_t get_data_size() override {
                return buf_size;
        }

#ifndef MYSQL_SERVER
        void print(FILE *file, PRINT_EVENT_INFO *print_event_info) const override;
#endif
#ifdef MYSQL_SERVER
        bool write(Basic_ostream *ostream) override {
        #ifndef NDEBUG
                if (DBUG_EVALUATE_IF("skip_writing_previous_gtids_log_event", 1, 0) &&
                    !is_relay_log_event()) {
                        DBUG_PRINT("info",
                                   ("skip writing Previous_gtids_log_event because of"
                                    "debug option 'skip_writing_previous_gtids_log_event'"));
                        return false;
                }

                if (DBUG_EVALUATE_IF("write_partial_previous_gtids_log_event", 1, 0) &&
                    !is_relay_log_event()) {
                        DBUG_PRINT("info",
                                   ("writing partial Previous_gtids_log_event because of"
                                    "debug option 'write_partial_previous_gtids_log_event'"));
                        return (Log_event::write_header(ostream, get_data_size()) || Log_event::write_data_header(ostream));
                }
        #endif

                return (Log_event::write_header(ostream, get_data_size()) || Log_event::write_data_header(ostream) ||
                        write_data_body(ostream) || Log_event::write_footer(ostream));
        }
        bool write_data_body(Basic_ostream *ostream) override;
#endif

        const uchar *get_buf() {
                return buf;
        }

        char *get_str(size_t *length, const Gtid_set::String_format *string_format) const;
        int add_to_set(Gtid_set *gtid_set) const;

#if defined(MYSQL_SERVER)
        enum_skip_reason do_shall_skip(Relay_log_info *) override  // 1358
        {
                return EVENT_SKIP_IGNORE;
        }

        int do_apply_event(Relay_log_info const *) override {
                return 0;
        }
        int do_update_pos(Relay_log_info *rli) override;
#endif
};
```

#### `#if/#elif/#else/#endif`

```c++
// #if
#if defined(DUK_ALLOC_POOL_TRACK_HIGHWATER)
        #if defined(DUK_ALLOC_POOL_DEBUG)
        duk__alloc_pool_dprintf("duk_alloc_pool_init: global highwater mark tracking enabled, THIS IS VERY SLOW!\n");
        #endif
        global->hwm_used_bytes  = 0U;
        global->hwm_waste_bytes = 0U;
#endif

// #else
#if defined(_WIN32)
        #include <conio.h>
        #define LOG_USER 0
#else
        #include <readline.h>
        #include <syslog.h>
        #define HAVE_READLINE
        #define USE_POPEN
#endif

// #elif
#if defined(__minix)
        #define swrite(x, y, z) (ssize_t)write((SEND_TYPE_ARG1)(x), \
                                    (SEND_TYPE_ARG2)(y), \
                                    (SEND_TYPE_ARG3)(z))

#elif defined(HAVE_SEND)
        #define swrite(x, y, z) (ssize_t)send((SEND_TYPE_ARG1)(x), \
                                    (SEND_QUAL_ARG2 SEND_TYPE_ARG2)(y), \
                                    (SEND_TYPE_ARG3)(z), \
                                    (SEND_TYPE_ARG4)(SEND_4TH_ARG))
#else
        #ifndef swrite
    Error Missing_definition_of_macro_swrite
        #endif
#endif
```


#### `#`

```c++
#define STRINGIFY(x) #x
#define mb() asm ("membar #LoadLoad | #LoadStore | #StoreLoad | #StoreStore")
#define rmb() asm ("membar #LoadLoad")
#define wmb() asm ("membar #StoreStore")
```


#### `##`
常用于#define中字符串拼接
```c++
#define GET_DATA(TI) (TI)->trace_plugin_data
#define GET_STAGE(TI) (TI)->stage
#define TEST_STAGE(TI, X) (GET_STAGE(TI) == PROTOCOL_STAGE_##X)
#define STATS_MISS(conn, op, key, nkey) STATS_TWO(conn, op##_misses, cmd_##op, key, nkey)

//
#define PSI_STATEMENT_CALL(M) pfs_##M##_vc
#define PSI_DIGEST_CALL(M) pfs_##M##_vc
#define PSI_PS_CALL(M) pfs_##M##_vc
```

#### `#line`
> 修改行号和文件名称
```c++
// line_directive.cpp
// Compile by using: cl /W4 /EHsc line_directive.cpp
#include <stdio.h>

int main() {
        printf("This code is on line %d, in file %s\n", __LINE__, __FILE__);
#line 10
        printf("This code is on line %d, in file %s\n", __LINE__, __FILE__);
#line 20 "hello.cpp"
        printf("This code is on line %d, in file %s\n", __LINE__, __FILE__);
        printf("This code is on line %d, in file %s\n", __LINE__, __FILE__);
}

```
`2024-04-17T23:18:07 [root@dev /root/tmp] # ./a.out`输出：
```bash
This code is on line 6, in file line_directive.cpp
This code is on line 10, in file line_directive.cpp
This code is on line 20, in file hello.cpp
This code is on line 21, in file hello.cpp
```


#### `#pragma`

- `#pragma message( messagestring )`是在编译期间，将一个文字串（`messagestring`）发送到标准输出窗口。典型的使用方法是在编译时报告和显示信息



**pragma message**
```c++
int main() {
#if defined(__cplusplus)
        #pragma message("__cplusplus has defined!")
#endif
        return 0;
}

```
`[root@dev /root/tmp] # g++ v.cc`输出：
```bash
v.cc: In function 'int main()':
v.cc:3:51: note: '#pragma message: __cplusplus has defined!'
    3 |         #pragma message("__cplusplus has defined!")
      |
```
- `note:`为亮蓝色


**pragma optimize**

```c++
static size_t cookie_hash_domain(const char *domain, const size_t len) {
        const char *end = domain + len;
        size_t h        = 5381;

        while (domain < end) {
                h += h << 5;
                h ^= Curl_raw_toupper(*domain++);
        }

        return (h % COOKIE_HASH_SIZE);
}
// 代码优化只被Visual C++专业版和企业版支持
// off 它会禁用所有优化
// on 它会将优化重置为你使用 /O 编译器选项指定的优化
#if defined(_MSC_VER) && (_MSC_VER == 1900)
        #pragma optimize("", on)
#endif

```

**pragma warning**：禁用警告与恢复警告

让编译器忽略指定编号的警告，跳过警告直接运行程序，可用来忽略一部分不重要的警告报错

```c++
#ifdef _MSC_VER
        #pragma warning(disable : 4127)
#endif

#if defined(_MSC_VER) && defined(_DLL) && !defined(__POCC__)
        #pragma warning(default : 4232)
#endif
```

**pragma warning(push)**：保存当前的编译器警告状态

**pragma warning(pop)**：是恢复原先的警告状态
```c++
#ifdef __INTEL_COMPILER
        #pragma warning(push)
        // 在#pragma warning(pop)之前的#pragma warning(disable : xxx)的告警禁止
        #pragma warning(disable : 810)
#endif

        DEBUGASSERT(ulnum <= (unsigned long)CURL_MASK_USHORT);
        return (unsigned short)(ulnum & (unsigned long)CURL_MASK_USHORT);

#ifdef __INTEL_COMPILER
        #pragma warning(pop)
#endif
```

- #pragma   warning(push)是保存当前的编译器警告状态；
- #pragma   warning(pop)是恢复原先的警告状态。
例如：
```c++
#pragma   warning(push)
#pragma   warning(disable:4705)
#pragma   warning(disable:4706)
#pragma   warning(disable:4707)
// Some code
#pragma   warning(pop)
```
这样在编译Some code部分的代码时，4705、4706、4707三个警告将不会出现。

- `#pragma GCC diagnostic push`：记录当前的诊断状态
- `#pragma GCC diagnostic ignored "-Wstrict-aliasing"`：关闭指定警告
- `#pragma GCC diagnostic pop`：恢复到之前的诊断状态

```c++
#ifdef __GNUC__
        // 记录当前的诊断状态
        #pragma GCC diagnostic push
        // 关闭指定警告
        #pragma GCC diagnostic ignored "-Wstrict-aliasing"
#endif

inline void Duration::clear_seconds() {
        seconds_ = int64_t{0};
}
inline int64_t Duration::_internal_seconds() const {
        return seconds_;
}
inline int64_t Duration::seconds() const {
        return _internal_seconds();
}
inline void Duration::_internal_set_seconds(int64_t value) {
        seconds_ = value;
}


#ifdef __GNUC__
        // 恢复到之前的诊断状态
        #pragma GCC diagnostic pop
#endif

// ignored
#if defined(__clang__)
        #pragma clang diagnostic push
        #pragma clang diagnostic ignored "-Wdocumentation"
#endif

#if defined(__clang__)
        #pragma clang diagnostic pop
#endif
```

**#pragma comment**
```c++
// windows
#pragma comment(lib, "ws2_32")
```

**#pragma pack**：内存对齐
```c++
#pragma pack(push, 8)
struct THREADNAME_INFO {
        DWORD dwType;
        LPCSTR szName;
        DWORD dwThreadID;
        DWORD dwFlags;
};
#pragma pack(pop)
```


#### `#error`
```c++
int main() {
#if !defined(__cplusplus)
        #error C++ compiler required.
#endif

#if !defined(SB)
        #error not define SB
#endif
}

```

`[root@dev /root/tmp] # g++ v.cc`输出：
```bash
v.cc:7:10: error: #error not define SB
    7 |         #error not define SB
      |          ^~~~~

```


举例说明

```c++
// ifdef
#ifdef FIELD_INCLUDED
        #error "sql_class.h includes field.h directly or indirectly; it should not."
#endif

// ifndef
#ifndef __cplusplus
        #error "Don't include this C++ header file from a non-C++ file!"
#endif
// ifndef
#ifndef MY_CONFIG_H
        #error "This build is broken"
#endif

// defined
#if (defined(TEST_STD_MAP) && (defined(TEST_STD_UNORDERED_MAP) || defined(TEST_TBB))) || \
    (defined(TEST_STD_UNORDERED_MAP) && defined(TEST_TBB))
        #error TEST_STD_MAP, TEST_STD_UNORDERED_MAP and TEST_TBB are mutually exclusive
#endif
// defined
#if defined(VM_TRACE) && !defined(__sun)
        #if defined(_WIN32) || (defined(MADV_DONTDUMP) && defined(MAP_NORESERVE)) || defined(MAP_GUARD)
                #define USE_DO_VIRTUAL_ALLOC
        #elif defined(USE_DO_VIRTUAL_ALLOC)
                #error do_virtual_alloc is not supported, please undefine USE_DO_VIRTUAL_ALLOC.
        #endif
#endif
// defined
#if defined(HAVE_FDATASYNC) && defined(HAVE_DECL_FDATASYNC)
                res = fdatasync(fd);
#elif defined(HAVE_FSYNC)
                res = fsync(fd);
#elif defined(_WIN32)
                res = my_win_fsync(fd);
#else
        #error Cannot find a way to sync a file, durability in danger
                res = 0;
#endif

// if
#if SIZEOF_INT == 4
static const unsigned SPACE_INT = 0x20202020U;
#elif SIZEOF_INT == 8
static const unsigned SPACE_INT = 0x2020202020202020ULL;
#else
        #error define the appropriate constant for a word full of spaces
#endif
// if
#if (MAX_NDB_NODES > (1 << NODEID_BITS))
        #error "Sysfile node id is too small"
#endif
// if
#if SIZEOF_LONG != SIZEOF_VOIDP
#error "Error: InnoDB's ulint must be of the same size as void*"
#endif

```









## 预定义宏


```viz-dot
digraph "预定义宏" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "预定义宏" -> "__FILE__";
        "预定义宏" -> "__LINE__";
        "预定义宏" -> "__DATE__";
        "预定义宏" -> "__TIME__";
        "预定义宏" -> "__VERSION__";
        "预定义宏" -> "__cplusplus";

        "预定义宏" -> "__STDC__";



}
```

```c++
#include <iostream>
using namespace std;

int main() {
        cout << "Value of __FILE__ : " << __FILE__ << endl;
        cout << "Value of __LINE__ : " << __LINE__ << endl;
        cout << "Value of __DATE__ : " << __DATE__ << endl;
        cout << "Value of __TIME__ : " << __TIME__ << endl;
        cout << "Value of __VERSION__ : " << __VERSION__ << endl;
        cout << "Value of __STDC__ : " << __STDC__ << endl;
        cout << "Value of __cplusplus : " << __cplusplus << endl;
        cout << "Value of __func__ : " << __func__ << endl;


        return 0;
}


```
`[root@dev /root/tmp] # ./a.out`输出
```bash

Value of __FILE__ : a.cc
Value of __LINE__ : 6
Value of __DATE__ : Apr 17 2024
Value of __TIME__ : 20:57:12
Value of __VERSION__ : 12.2.1 20221121 (Red Hat 12.2.1-7)
Value of __STDC__ : 1
Value of __cplusplus : 201703
Value of __func__ : main

```


## include
```viz-dot
digraph "include" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "include" -> "头文件替换";
        "include" -> "模板类vector";
        "include" -> "模板类array";
        "include" -> "模板类valarray";

}
```



## 一、#ifndef / #define / #endif 宏
这是一种传统的头文件守卫方法，它利用预处理器的条件编译功能。当头文件第一次被包含时，它定义一个唯一的宏；在随后的包含中，这个宏的存在将阻止头文件的内容再次被包含。
```c++
// example.h
#ifndef EXAMPLE_H
#define EXAMPLE_H

// 头文件内容
class Example {
public:
    void doSomething();
};

#endif // EXAMPLE_H
```
在这个例子中，EXAMPLE_H是一个宏，用于标识这个头文件。如果EXAMPLE_H没有被定义，那么头文件的内容就会被包含；否则，内容将被跳过。

这种方法的主要优点是它的可移植性好，几乎所有的C++编译器都支持这种预处理指令。然而，它的缺点是必须为每个头文件选择一个唯一的宏名称，这可能会变得复杂且容易出错，特别是在大型项目中。


## pragma once

#pragma once是一种更简洁的头文件守卫方法，它只需要一行代码就可以防止头文件被多次包含。
```c++
// example.h
#pragma once

// 头文件内容
class Example {
public:
    void doSomething();
};
```
在这个例子中，#pragma once确保头文件只被编译器处理一次，即使在同一个编译单元中被多次包含。

#pragma once的主要优点是它的简洁性和易用性。你不需要为每个头文件选择一个唯一的宏名称，这减少了出错的可能性。此外，一些编译器可能对#pragma once进行了优化，使其在性能上略优于传统的#ifndef守卫。

然而，#pragma once的一个潜在缺点是它的可移植性。虽然大多数现代C++编译器都支持这个指令，但在一些非常老旧或特定的编译器上可能不受支持。此外，由于#pragma指令是编译器特定的，不同的编译器可能有不同的实现和行为。


在选择使用#ifndef/#define/#endif宏还是#pragma once时，应该考虑项目的具体需求和目标平台。如果你的项目需要在多种编译器上工作，特别是包括一些老旧或特定的编译器，那么传统的#ifndef守卫可能是更安全的选择。然而，如果你的项目主要针对现代编译器，并且你希望代码更简洁、易维护，那么#pragma once可能是一个更好的选择。

无论选择哪种方法，重要的是保持一致性和清晰性。确保你的项目中所有头文件都使用相同的守卫方法，并且守卫的命名清晰明了，这样可以最大限度地减少混淆和错误的可能性。






#### 重复包含问题
> `ifndf`
```c++
#include "add.h"
#include "add.h"
#include "add.h"
#include "add.h"

```






#### pfs_stat.h

```c++
// 防止重复定义
#ifndef _my_user_h_
#define _my_user_h_

// 条件编译
#if MAX_INDEXES > HA_MAX_POSSIBLE_KEY
#define MI_MAX_KEY HA_MAX_POSSIBLE_KEY
#else
#define MI_MAX_KEY MAX_INDEXES
#endif

#ifdef __cplusplus
extern "C" {
#endif

// 引用头文件
#include <stddef.h>
#include "my_macros.h"

// 宏定义
#define NOT_NULL_FLAG 1
#define PRI_KEY_FLAG 2
#define UNIQUE_KEY_FLAG 4
#define MULTIPLE_KEY_FLAG 8
#define BLOB_FLAG 16
#define UNSIGNED_FLAG 32
#define ZEROFILL_FLAG 64
#define BINARY_FLAG 128

// 别名定义
typedef struct lex_state_maps_st lex_state_maps_st;
typedef enum enum_sql_command enum_sql_command_t;

// 全局变量声明
extern thread_local THD *current_thd;
extern ulong myisam_data_pointer_size;
extern bool myisam_flush
extern my_off_t myisam_max_temp_length;

// static变量
static uint opt_ssl_mode = 2;
static char *opt_ssl_ca = nullptr;
static bool ssl_mode_set_explicitly = false;
static char *opt_ssl_session_data = nullptr;

// const 变量
const char *SSL_SET_OPTIONS_ERROR = "Failed to set ssl related options.\n";
const char *ssl_mode_names_lib[] = {"DISABLED", "PREFERRED", "REQUIRED", "VERIFY_CA", "VERIFY_IDENTITY", NullS};

// extern const
extern const char *myisam_log_filename;

// 全局函数声明，在C++头文件中，只需提供函数的声明，而无需使用 extern 关键字
extern int mi_rrnd(MI_INFO *file, uchar *buf, my_off_t pos);
extern int mi_scan_init(MI_INFO *file);
extern int mi_scan(MI_INFO *file, uchar *buf);
extern "C" void my_make_scrambled_password(char *to, const char *password, size_t pass_len);
int chk_status(MI_CHECK *param, MI_INFO *info);
int chk_size(MI_CHECK *param, MI_INFO *info);
int chk_key(MI_CHECK *param, MI_INFO *info);


// inline 函数
inline void net_server_ext_init(NET_SERVER *ns) {
        ns->m_user_data = nullptr;
        ns->m_before_header = nullptr;
        ns->m_after_header = nullptr;
        ns->compress_ctx.algorithm = MYSQL_UNCOMPRESSED;
        ns->timeout_on_full_packet = false;
}

// static inline 函数
static inline float mi_float4get(const uchar *M) {
        float def_temp;
        ((uchar *)&def_temp)[0] = M[3];
        ((uchar *)&def_temp)[1] = M[2];
        ((uchar *)&def_temp)[2] = M[1];
        ((uchar *)&def_temp)[3] = M[0];
        return def_temp;
}

// 模板函数定义
template <typename TLambda>
Scope_guard<TLambda> create_scope_guard(const TLambda rollback_lambda) {
        return Scope_guard<TLambda>(rollback_lambda);
}





// 定义 enum
enum my_xml_node_type {
        MY_XML_NODE_TAG,
        MY_XML_NODE_ATTR,
        MY_XML_NODE_TEXT
};


// struct 声明
struct MI_INFO;
struct st_mysql_trace_info;
struct mysql_async_connect;
// struct 定义
struct MI_CREATE_INFO {
        const char *index_file_name, *data_file_name; /* If using symlinks */
        ha_rows max_rows;
        ha_rows reloc_rows;
        ulonglong auto_increment;
        ulonglong data_file_length;
        ulonglong key_file_length;
        uint old_options;
        uint16 language;
        bool with_auto_increment;
};

struct PFS_memory_safe_stat {
        bool m_used;

        size_t m_alloc_count;
        size_t m_free_count;
        size_t m_alloc_size;
        size_t m_free_size;

        size_t m_alloc_count_capacity;
        size_t m_free_count_capacity;
        size_t m_alloc_size_capacity;
        size_t m_free_size_capacity;

        void reset(void);

        void rebase(void);

        PFS_memory_stat_alloc_delta *count_alloc(size_t size,
                                                 PFS_memory_stat_alloc_delta *delta);

        PFS_memory_stat_free_delta *count_free(size_t size,
                                               PFS_memory_stat_free_delta *delta);
};


// 类声明
class String;
class THD;

// 类定义
class rwlock_scoped_lock {
public:
        rwlock_scoped_lock(mysql_rwlock_t *lock, bool lock_for_write,
                           const char *file, int line);
        rwlock_scoped_lock(rwlock_scoped_lock &&lock);
        rwlock_scoped_lock(const rwlock_scoped_lock &) = delete;
        ~rwlock_scoped_lock();

        rwlock_scoped_lock &operator=(const rwlock_scoped_lock &) = delete;

private:
        mysql_rwlock_t *m_lock;
};

class Simple_cstring {
private:
        const char *m_str;
        size_t m_length;

public:

        void set(const char *str_arg, size_t length_arg) {
                assert(str_arg || length_arg == 0);
                assert(!str_arg || str_arg[length_arg] == '\0');
                m_str = str_arg;
                m_length = length_arg;
        }
        Simple_cstring() { set(nullptr, 0); }
        Simple_cstring(const char *str_arg, size_t length_arg) {
                set(str_arg, length_arg);
        }
        Simple_cstring(const LEX_STRING arg) { set(arg.str, arg.length); }
        Simple_cstring(const LEX_CSTRING arg) { set(arg.str, arg.length); }

        bool is_set() const { return m_str != nullptr; }
        size_t length() const { return m_length; }
};




// 模板类定义
template <typename TLambda>
class Scope_guard {
public:
        Scope_guard(const TLambda &rollback_lambda)
            : m_committed(false), m_rollback_lambda(rollback_lambda) {}
        Scope_guard(const Scope_guard<TLambda> &) = delete;
        Scope_guard(Scope_guard<TLambda> &&moved)
            : m_committed(moved.m_committed),
              m_rollback_lambda(moved.m_rollback_lambda) {
                /* Set moved guard to "invalid" state, the one in which the rollback lambda
                  will not be executed. */
                moved.m_committed = true;
        }
        ~Scope_guard() {
                if (!m_committed) {
                        m_rollback_lambda();
                }
        }

        inline void commit() { m_committed = true; }

        inline void rollback() {
                if (!m_committed) {
                        m_rollback_lambda();
                        m_committed = true;
                }
        }

private:
        bool m_committed;
        const TLambda m_rollback_lambda;
};




#endif
```



#### pfs_stat.cc
```c++
void PFS_memory_safe_stat::reset() {
        m_used = false;

        m_alloc_count = 0;
        m_free_count = 0;
        m_alloc_size = 0;
        m_free_size = 0;

        m_alloc_count_capacity = 0;
        m_free_count_capacity = 0;
        m_alloc_size_capacity = 0;
        m_free_size_capacity = 0;
}

void PFS_memory_safe_stat::rebase() {
        if (!m_used) {
                return;
        }

        size_t base;

        base = std::min<size_t>(m_alloc_count, m_free_count);
        m_alloc_count -= base;
        m_free_count -= base;

        base = std::min<size_t>(m_alloc_size, m_free_size);
        m_alloc_size -= base;
        m_free_size -= base;

        m_alloc_count_capacity = 0;
        m_free_count_capacity = 0;
        m_alloc_size_capacity = 0;
        m_free_size_capacity = 0;
}

PFS_memory_stat_alloc_delta *PFS_memory_safe_stat::count_alloc(
    size_t size, PFS_memory_stat_alloc_delta *delta) {
        m_used = true;

        m_alloc_count++;
        m_free_count_capacity++;
        m_alloc_size += size;
        m_free_size_capacity += size;

        if ((m_alloc_count_capacity >= 1) && (m_alloc_size_capacity >= size)) {
                m_alloc_count_capacity--;
                m_alloc_size_capacity -= size;
                return nullptr;
        }

        if (m_alloc_count_capacity >= 1) {
                m_alloc_count_capacity--;
                delta->m_alloc_count_delta = 0;
        } else {
                delta->m_alloc_count_delta = 1;
        }

        if (m_alloc_size_capacity >= size) {
                m_alloc_size_capacity -= size;
                delta->m_alloc_size_delta = 0;
        } else {
                delta->m_alloc_size_delta = size - m_alloc_size_capacity;
                m_alloc_size_capacity = 0;
        }

        return delta;
}

PFS_memory_stat_free_delta *PFS_memory_safe_stat::count_free(
    size_t size, PFS_memory_stat_free_delta *delta) {
        m_used = true;

        m_free_count++;
        m_alloc_count_capacity++;
        m_free_size += size;
        m_alloc_size_capacity += size;

        if ((m_free_count_capacity >= 1) && (m_free_size_capacity >= size)) {
                m_free_count_capacity--;
                m_free_size_capacity -= size;
                return nullptr;
        }

        if (m_free_count_capacity >= 1) {
                m_free_count_capacity--;
                delta->m_free_count_delta = 0;
        } else {
                delta->m_free_count_delta = 1;
        }

        if (m_free_size_capacity >= size) {
                m_free_size_capacity -= size;
                delta->m_free_size_delta = 0;
        } else {
                delta->m_free_size_delta = size - m_free_size_capacity;
                m_free_size_capacity = 0;
        }

        return delta;
}
```





