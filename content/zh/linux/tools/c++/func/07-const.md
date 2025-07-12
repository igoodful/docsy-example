---
title: const
description: const
date: 2023-11-17
weight: 700


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

const 是 constant 的缩写，本意是不变的，不易改变的意思。在 C++ 中是用来修饰内置类型变量，自定义对象，成员函数，返回值，函数参数。

{{< /alert >}}


{{<alert>}}


{{</alert>}}

## 总体

```viz-dot
digraph "const" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "const" -> "指针变量";
        "指针变量" -> "不可通过指针变量修改指向的内容";
        "不可通过指针变量修改指向的内容" -> "指向const变量";
        "不可通过指针变量修改指向的内容" -> "指向常规变量";
        "指针变量" -> "指针变量不可指向其他地址";
        "const" -> "基本数据类型变量";

}
```
- 不允许将const地址赋给常规指针，但可通过const_cast运算符突破这种限制



## 一、修饰普通变量
- 常量在定义之后就不能修改，因此定义时必须初始化；若const常量没有初始化，其值是不确定的一个值，且不可修改
- 常量名称最好全部大写
- const常量比#define常量更好
  1. const明确数据类型
  2. 作用域规则
  3. 用const值声明数组大小，在c++中可以，但在C语言中不可行

```c++
const double pi = 3.14159;
const int a     = 7;
int b           = a; // 正确
a               = 8; // 错误，不能改变
```
a 被定义为一个常量，并且可以将 a 赋值给 b，但是不能给 a 再次赋值。对一个常量赋值是违法的事情，因为 a 被编译器认为是一个常量，其值不允许修改。








## 二、修饰指针变量
const 修饰指针变量有以下三种情况。

- const 修饰指针指向的内容，则内容为不可变量。
- const 修饰指针，则指针为不可变量。
- const 修饰指针和指针指向的内容，则指针和指针指向的内容都为不可变量。

#### 1. 修饰指针指向的内容
```c++
const int *p = 8;
```
则指针指向的内容 8 不可改变。简称左定值，因为 const 位于 * 号的左边。

#### 2. 修饰指针
```c++
int a        = 8;
int* const p = &a;
*p           = 9; // 正确
int  b       = 7;
p            = &b; // 错误
```
对于 const 指针 p 其指向的内存地址不能够被改变，但其内容可以改变。简称，右定向。因为 const 位于 * 号的右边。

#### 3. 修饰指针和指针指向的内容
对于 C: 则是 A 和 B的合并
```c++
int a                = 8;
const int * const  p = &a;
```
这时，const p 的指向的内容和指向的内存地址都已固定，不可改变。

对于 A，B，C 三种情况，根据 const 位于 * 号的位置不同，我总结三句话便于记忆的话："左定值，右定向，const修饰不变量"。



## 三、const参数

对于 const 修饰函数参数可以分为三种情况。

#### 1. 修饰值传递
值传递的 const 修饰传递，一般这种情况不需要 const 修饰，因为函数会自动产生临时变量复制实参值。

`2024-04-10T23:11:10 [root@dev /root/tmp] # cat e.cc`
```c++

#include<iostream>

using namespace std;
void add(const int a){
        // 编译不通过
        a = a + 10;
        cout<<a<<endl;
}

void sub(int b){
        // 正确，但不会改变实参
        b = b - 10;
        cout<<b<<endl;
}

int main(void){
        int a = 10;
        int b = 20;
        add(a);
        sub(b);
        cout<<a<<endl;
        cout<<b<<endl;
        return 0;
}
```

编译：`2024-04-10T23:11:26 [root@dev /root/tmp] # g++ e.cc`
```bash
e.cc: In function ‘void add(int)’:
e.cc:6:11: error: assignment of read-only parameter ‘a’
    6 |         a = a + 10;
      |         ~~^~~~~~~~
```

#### 2. 修饰指针参数

可以防止指针被意外篡改

```c++
bool write(const unsigned char *buffer, my_off_t length) override {
        assert(m_pipeline_head != nullptr);

        if (m_pipeline_head->write(buffer, length))
                return true;

        m_position += length;
        return false;
}

bool MYSQL_BIN_LOG::check_write_error(const THD *thd) {
        DBUG_TRACE;

        bool checked = false;

        if (!thd->is_error())
                return checked;

        switch (thd->get_stmt_da()->mysql_errno()) {
                case ER_TRANS_CACHE_FULL:
                case ER_STMT_CACHE_FULL:
                case ER_ERROR_ON_WRITE:
                case ER_BINLOG_LOGGING_IMPOSSIBLE:
                        checked = true;
                        break;
        }
        DBUG_PRINT("return", ("checked: %s", YESNO(checked)));
        return checked;
}

bool Field_temporal_with_date_and_time::convert_TIME_to_timestamp(const MYSQL_TIME *ltime,
                                                                  const Time_zone &tz,
                                                                  my_timeval *tm,
                                                                  int *warnings) {
        /*
          No need to do check_date(TIME_NO_ZERO_IN_DATE),
          because it has been done earlier in
          store_time(), number_to_datetime() or str_to_datetime().
        */
        if (datetime_with_no_zero_in_date_to_timeval(ltime, tz, tm, warnings)) {
                tm->m_tv_sec = tm->m_tv_usec = 0;
                return true;
        }
        // Check if the time since epoch fits in TIMESTAMP.
        if (tm->m_tv_sec > TYPE_TIMESTAMP_MAX_VALUE) {
                tm->m_tv_sec = tm->m_tv_usec = 0;
                *warnings |= MYSQL_TIME_WARN_OUT_OF_RANGE;
        }

        return false;
}

int vio_getnameinfo(const struct sockaddr *sa, char *hostname, size_t hostname_size, char *port, size_t port_size, int flags) {
        int sa_length = 0;

        switch (sa->sa_family) {
                case AF_INET:
                        sa_length = sizeof(struct sockaddr_in);
#ifdef HAVE_SOCKADDR_IN_SIN_LEN
                        ((struct sockaddr_in *)sa)->sin_len = sa_length;
#endif /* HAVE_SOCKADDR_IN_SIN_LEN */
                        break;

                case AF_INET6:
                        sa_length = sizeof(struct sockaddr_in6);
#ifdef HAVE_SOCKADDR_IN6_SIN6_LEN
                        ((struct sockaddr_in6 *)sa)->sin6_len = sa_length;
#endif /* HAVE_SOCKADDR_IN6_SIN6_LEN */
                        break;
        }

        return getnameinfo(sa, sa_length, hostname, hostname_size, port, port_size, flags);
}

int table_prepared_stmt_instances::rnd_pos(const void *pos) {
        PFS_prepared_stmt *pfs;

        set_position(pos);

        pfs = global_prepared_stmt_container.get(m_pos.m_index);
        if (pfs != nullptr) {
                return make_row(pfs);
        }

        return HA_ERR_RECORD_DELETED;
}

void write_read_flush_loop(const void *data, void *buffer, const size_t len, const int loops) {
        using namespace std::chrono;
        nanoseconds flushes(0);
        nanoseconds writes(0);
        nanoseconds reads(0);

        for (int i = 0; i < loops; ++i) {
                write_read_flush(data, buffer, len, writes, reads, flushes);
        }
        auto writes_ms  = duration_cast<milliseconds>(writes).count();
        auto reads_ms   = duration_cast<milliseconds>(reads).count();
        auto flushes_ms = duration_cast<milliseconds>(flushes).count();
        std::cout << "Write duration total: " << writes_ms << " ms" << std::endl;
        std::cout << "Read duration total: " << reads_ms << " ms" << std::endl;
        std::cout << "Flush duration total: " << flushes_ms << " ms" << std::endl;
}

void Bignum::BigitsShiftLeft(const int shift_amount) {
        DOUBLE_CONVERSION_ASSERT(shift_amount < kBigitSize);
        DOUBLE_CONVERSION_ASSERT(shift_amount >= 0);
        Chunk carry = 0;
        for (int i = 0; i < used_bigits_; ++i) {
                const Chunk new_carry = RawBigit(i) >> (kBigitSize - shift_amount);
                RawBigit(i)           = ((RawBigit(i) << shift_amount) + carry) & kBigitMask;
                carry                 = new_carry;
        }
        if (carry != 0) {
                RawBigit(used_bigits_) = carry;
                used_bigits_++;
        }
}

void Bignum::SubtractTimes(const Bignum &other, const int factor) {
        DOUBLE_CONVERSION_ASSERT(exponent_ <= other.exponent_);
        if (factor < 3) {
                for (int i = 0; i < factor; ++i) {
                        SubtractBignum(other);
                }
                return;
        }
        Chunk borrow            = 0;
        const int exponent_diff = other.exponent_ - exponent_;
        for (int i = 0; i < other.used_bigits_; ++i) {
                const DoubleChunk product   = static_cast<DoubleChunk>(factor) * other.RawBigit(i);
                const DoubleChunk remove    = borrow + product;
                const Chunk difference      = RawBigit(i + exponent_diff) - (remove & kBigitMask);
                RawBigit(i + exponent_diff) = difference & kBigitMask;
                borrow                      = static_cast<Chunk>((difference >> (kChunkSize - 1)) + (remove >> kBigitSize));
        }
        for (int i = other.used_bigits_ + exponent_diff; i < used_bigits_; ++i) {
                if (borrow == 0) {
                        return;
                }
                const Chunk difference = RawBigit(i) - borrow;
                RawBigit(i)            = difference & kBigitMask;
                borrow                 = difference >> (kChunkSize - 1);
        }
        Clamp();
}



```
#### 3. 修饰自定义类型
自定义类型的参数传递，需要临时对象复制参数，对于临时对象的构造，需要调用构造函数，比较浪费时间，因此我们采取 const 外加引用传递的方法。

并且对于一般的 int、double 等内置类型，我们不采用引用的传递方式。

```c++
#include<iostream>

using namespace std;

class Test{
public:
Test(){

}

Test(int _m):_cm(_m){

}

int get_cm()const{
        return _cm;
    }

private:
int _cm;
};



void Cmf(const Test& _tt){
        cout<<_tt.get_cm();
}

int main(void){
        Test t(8);
        Cmf(t);
        system("pause");
        return 0;
}
```

## 修饰函数返回值

Const 修饰返回值分三种情况。

#### 1.修饰内置类型
修饰内置类型的返回值，修饰与不修饰返回值作用一样

```c++
#include<iostream>

using namespace std;

const int Cmf(){
        return 1;
}

int Cpf(){
        return 0;
}

int main(void){
        int _m = Cmf();
        int _n = Cpf();
        cout<<_m<<" "<<_n;
        system("pause");
        return 0;
}
```
#### 2. 修饰自定义类型

修饰自定义类型的作为返回值，此时返回的值不能作为左值使用，既不能被赋值，也不能被修改

#### 3. 修饰返回的指针或者引用

修饰返回的指针或者引用，是否返回一个指向 const 的指针，取决于我们想让用户干什么


## const成员变量
const 成员变量的用法和普通 const 变量的用法相似，只需要在声明时加上 const 关键字。初始化 const 成员变量只有一种方法，就是通过构造函数的初始化列表
```c++
struct row_processlist {
        ulonglong m_processlist_id;
        PFS_user_name m_user_name;
        char m_hostname[HOST_AND_PORT_LENGTH];
        uint m_hostname_length;
        PFS_schema_name m_db_name;
        int m_command;
        time_t m_start_time;
        const char *m_processlist_state_ptr;
        uint m_processlist_state_length;
        const char *m_processlist_info_ptr;
        uint m_processlist_info_length;
        bool m_secondary;
};
```


## const成员函数
> 不希望成员函数修改成员变量，比如常见的获取成员变量值的成员函数，它保证不会修改对象的状态。
>
> 函数头部的结尾加上 const 表示常成员函数，这种函数只能读取成员变量的值，而不能修改成员变量的值
>
> 函数开头的 const 用来修饰函数的返回值，表示返回值是 const 类型，也就是不能被修改
```viz-dot
digraph "成员函数" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];
        "成员函数"[label="const成员函数"]
        "成员函数" -> "const成员函数";
        "成员函数" -> "非mutable成员变量"[label="可访问，不可修改"];
        "成员函数" -> "mutable成员变量"[label="可访问，可修改"];

}
```
注意：const 关键字不能与 static 关键字同时使用，因为 static 关键字修饰静态成员函数，静态成员函数不含有 this 指针，即不能实例化，const 成员函数必须具体到某一实例。


```c++
class Properties {
public:
        static constexpr char delimiter               = ':';
        static constexpr char truncated_prefix_mark[] = "...:";
        static const char version[];
        Properties(bool case_insensitive = false);
        Properties(const Properties &);
        Properties(const Property *, int len);
        Properties &operator=(const Properties &);
        virtual ~Properties();
        void setCaseInsensitiveNames(bool value);
        bool getCaseInsensitiveNames() const;
        void put(const Property *, int len);
        bool put(const char *name, Uint32 value, bool replace = false);
        bool put64(const char *name, Uint64 value, bool replace = false);
        bool put(const char *name, const char *value, bool replace = false);
        bool put(const char *name, std::string_view value, bool replace = false);
        bool put(const char *name, const Properties *value, bool replace = false);
        bool append(const char *name, const char *value);
        bool append(const char *name, std::string_view value);
        bool put(const char *, Uint32 no, Uint32, bool replace = false);
        bool put64(const char *, Uint32 no, Uint64, bool replace = false);
        bool put(const char *, Uint32 no, const char *, bool replace = false);
        bool put(const char *, Uint32 no, std::string_view, bool replace = false);
        bool put(const char *, Uint32 no, const Properties *, bool replace = false);
        bool getTypeOf(const char *name, PropertiesType *type) const;
        bool contains(const char *name) const;
        bool get(const char *name, Uint32 *value) const;
        bool get(const char *name, Uint64 *value) const;
        bool get(const char *name, const char **value) const;
        bool get(const char *name, BaseString &value) const;
        bool get(const char *name, const Properties **value) const;
        bool getCopy(const char *name, char **value) const;
        bool getCopy(const char *name, Properties **value) const;
        bool getTypeOf(const char *name, Uint32 no, PropertiesType *type) const;
        bool contains(const char *name, Uint32 no) const;
        bool get(const char *name, Uint32 no, Uint32 *value) const;
        bool get(const char *name, Uint32 no, Uint64 *value) const;
        bool get(const char *name, Uint32 no, const char **value) const;
        bool get(const char *name, Uint32 no, const Properties **value) const;
        bool getCopy(const char *name, Uint32 no, char **value) const;
        bool getCopy(const char *name, Uint32 no, Properties **value) const;
        void clear();
        void remove(const char *name);
        void print(FILE *file = stdout, const char *prefix = nullptr) const;
        class Iterator {
        public:
                Iterator(const Properties *prop);
                ~Iterator();

                const char *first();
                const char *next();

        private:
                const Properties *m_prop;
                class IteratorImpl *m_iterImpl;
        };
        friend class Properties::Iterator;

        Uint32 getPropertiesErrno() const {
                return propErrno;
        }
        Uint32 getOSErrno() const {
                return osErrno;
        }

private:
        mutable Uint32 propErrno;
        mutable Uint32 osErrno;

        friend class PropertiesImpl;
        class PropertiesImpl *impl;
        class Properties *parent;

        void setErrno(Uint32 pErr, Uint32 osErr = 0) const;
};
```

