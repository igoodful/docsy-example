---
title: const_cast
description: const_cast
date: 2023-11-17
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}



{{< /alert >}}


{{<alert>}}


{{</alert>}}

const_cast是一种C++运算符，主要是用来去除复合类型中const和volatile属性（没有真正去除）。

变量本身的const属性是不能去除的，要想修改变量的值，一般是去除指针（或引用）的const属性，再进行间接修改。

用法:const_cast<type>(expression)

通过const_cast运算符，也只能将const type转换为type，将const type&转换为type&。

也就是说源类型和目标类型除了const属性不同，其他地方完全相同。




## 一、const

```c++
static void init_builtin_memory_class(PFS_builtin_memory_class *klass, const char *name, const char *documentation) {
        klass->m_class.m_type             = PFS_CLASS_MEMORY;
        klass->m_class.m_enabled          = true;
        klass->m_class.m_timed            = false;
        klass->m_class.m_flags            = PSI_FLAG_ONLY_GLOBAL_STAT;
        klass->m_class.m_volatility       = PSI_VOLATILITY_PERMANENT;
        klass->m_class.m_documentation    = const_cast<char *>(documentation);
        klass->m_class.m_event_name_index = 0;
        klass->m_class.m_name.set(PFS_CLASS_MEMORY, name);
        assert(klass->m_class.m_name.length() <= klass->m_class.m_name.max_length);

        klass->m_stat.reset();
}
```


```c++
const int constant = 21;
const int* const_p = &constant;
int* modifier      = const_cast<int*>(const_p);
*modifier          = 7;
```



## 二、volatile




