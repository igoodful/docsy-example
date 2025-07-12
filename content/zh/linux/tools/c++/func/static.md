---
title: static
description: static
date: 2023-11-17
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

全局变量
局部变量
全局函数
静态成员变量
静态成员函数

{{< /alert >}}


{{<alert>}}


{{</alert>}}


## 一、全局函数
在函数的返回类型前加上关键字static，函数就被定义成为静态函数。普通 函数的定义和声明默认情况下是extern的，但静态函数只是在声明他的文件当中可见，不能被其他文件所用。因此定义静态函数有以下好处：
- 其他文件中可以定义相同名字的函数，不会发生冲突
- 静态函数不能被其他文件所用

```c++
static void init_builtin_memory_class(PFS_builtin_memory_class *klass, const char *name, const char *documentation) {
        klass->m_class.m_type             = PFS_CLASS_MEMORY;
        klass->m_class.m_enabled          = true;  /* Immutable */
        klass->m_class.m_timed            = false; /* N/A */
        klass->m_class.m_flags            = PSI_FLAG_ONLY_GLOBAL_STAT;
        klass->m_class.m_volatility       = PSI_VOLATILITY_PERMANENT;
        klass->m_class.m_documentation    = const_cast<char *>(documentation);
        klass->m_class.m_event_name_index = 0;
        klass->m_class.m_name.set(PFS_CLASS_MEMORY, name);
        assert(klass->m_class.m_name.length() <= klass->m_class.m_name.max_length);

        klass->m_stat.reset();
}
```

## 二、成员函数
如果这个静态函数出现在类里，那么它是一个静态成员函数；

- 静态成员函数的作用在于：调用这个函数不会访问或者修改任何对象（非static）数据成员

- 其实很好理解，类的静态成员(变量和方法)属于类本身，在类加载的时候就会分配内存，可以通过类名直接去访问

- 非静态成员（变量和方法）属于类的对象，所以只有在类的对象产生（创建类的实例）时才会分配内存，然后通过类的对象（实例）去访问

如果它不是出现在类中，那么它是一个普通的全局的静态函数



## 三、修饰成员变量

```c++
/** Statistics for TABLE usage. */
struct PFS_table_stat {
        /**
          Statistics, per index.
          Each index stat is in [0, MAX_INDEXES-1],
          stats when using no index are in [MAX_INDEXES].
        */
        PFS_table_io_stat m_index_stat[MAX_INDEXES + 1];

        /**
          Statistics, per lock type.
        */
        PFS_table_lock_stat m_lock_stat;

        /** Reset table I/O statistic. */
        inline void reset_io(void) {
                PFS_table_io_stat *stat      = &m_index_stat[0];
                PFS_table_io_stat *stat_last = &m_index_stat[MAX_INDEXES + 1];
                for (; stat < stat_last; stat++) {
                        stat->reset();
                }
        }

        /** Reset table lock statistic. */
        inline void reset_lock(void) {
                m_lock_stat.reset();
        }

        /** Reset table statistic. */
        inline void reset(void) {
                reset_io();
                reset_lock();
        }

        inline void fast_reset_io(void) {
                memcpy(&m_index_stat, &g_reset_template.m_index_stat, sizeof(m_index_stat));
        }

        inline void fast_reset_lock(void) {
                memcpy(&m_lock_stat, &g_reset_template.m_lock_stat, sizeof(m_lock_stat));
        }

        inline void fast_reset(void) {
                memcpy(this, &g_reset_template, sizeof(*this));
        }

        inline void aggregate_io(const PFS_table_stat *stat, uint key_count) {
                PFS_table_io_stat *to_stat;
                PFS_table_io_stat *to_stat_last;
                const PFS_table_io_stat *from_stat;

                assert(key_count <= MAX_INDEXES);

                /* Aggregate stats for each index, if any */
                to_stat      = &m_index_stat[0];
                to_stat_last = to_stat + key_count;
                from_stat    = &stat->m_index_stat[0];
                for (; to_stat < to_stat_last; from_stat++, to_stat++) {
                        to_stat->aggregate(from_stat);
                }

                /* Aggregate stats for the table */
                to_stat   = &m_index_stat[MAX_INDEXES];
                from_stat = &stat->m_index_stat[MAX_INDEXES];
                to_stat->aggregate(from_stat);
        }

        inline void aggregate_lock(const PFS_table_stat *stat) {
                m_lock_stat.aggregate(&stat->m_lock_stat);
        }

        inline void aggregate(const PFS_table_stat *stat, uint key_count) {
                aggregate_io(stat, key_count);
                aggregate_lock(stat);
        }

        inline void sum_io(PFS_single_stat *result, uint key_count) {
                PFS_table_io_stat *stat;
                PFS_table_io_stat *stat_last;

                assert(key_count <= MAX_INDEXES);

                /* Sum stats for each index, if any */
                stat      = &m_index_stat[0];
                stat_last = stat + key_count;
                for (; stat < stat_last; stat++) {
                        stat->sum(result);
                }

                /* Sum stats for the table */
                m_index_stat[MAX_INDEXES].sum(result);
        }

        inline void sum_lock(PFS_single_stat *result) {
                m_lock_stat.sum(result);
        }

        inline void sum(PFS_single_stat *result, uint key_count) {
                sum_io(result, key_count);
                sum_lock(result);
        }

        static struct PFS_table_stat g_reset_template;
};
```
静态成员在类的所有对象中是共享的。如果不存在其他的初始化语句，在创建第一个对象时，所有的静态数据都会被初始化为零。我们不能把静态成员的初始化放置在类的定义中，但是可以在类的外部通过使用范围解析运算符 :: 来重新声明静态变量从而对它进行初始化

在这段代码中，g_reset_template 被定义为 static 的主要原因是为了确保它只有一个全局实例，并且可以在不实例化 PFS_table_stat 类的情况下直接访问。

由于 g_reset_template 是一个模板用于初始化 PFS_table_stat 结构体的默认值，将其定义为 static 可以确保它在程序的整个生命周期内只有一个实例。这样，在任何地方都可以通过 PFS_table_stat::g_reset_template 来访问它，而不必担心重复定义或多次实例化的问题。

另外，将 g_reset_template 定义为 static 还有助于节省内存空间，因为它只需要在程序的全局静态存储区中分配一次内存，而不是每次实例化 PFS_table_stat 类时都分配一次内存。这样可以减少不必要的内存开销，并提高程序的性能。

总之，将 g_reset_template 定义为 static 可以确保它在全局范围内只有一个实例，并且能够在不实例化 PFS_table_stat 类的情况下直接访问，同时也有助于节省内存空间。


```c++
#include <iostream>

using namespace std;

class Box
{
   public:
      static int objectCount;
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0){
        cout <<"Constructor called." << endl;
        length = l;
        breadth = b;
        height = h;
        // 每次创建对象时增加 1
        objectCount++;
      }
      double Volume(){
        return length * breadth * height;
      }
   private:
      double length;     // 长度
      double breadth;    // 宽度
      double height;     // 高度
};

// 初始化类 Box 的静态成员
int Box::objectCount = 0;

int main(void){
        Box Box1(3.3, 1.2, 1.5);    // 声明 box1
        Box Box2(8.5, 6.0, 2.0);    // 声明 box2
        cout << "Total objects: " << Box::objectCount << endl; // 输出对象的总数
        return 0;
}
```


