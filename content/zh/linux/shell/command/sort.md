---
title: sort
description: 排序
date: 2023-10-31
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}
- -n    按照数字大小排序

- -r    倒序

- -u    去重，unique含义

- -k    按照第3列，举例说明：sort -k 5,6 file.txt  先以第5列，再以第6列排序

- -t    指定排序时列的分隔符

- -o    指定输出文件名称
{{< /alert >}}

> 只针对公司英文名称的第二个字母进行排序，如果相同的按照员工工资进行降序排序：
```sql
shell> sort -t ‘ ‘ -k 1.2,1.2 -k 3,3nr facebook.txt

baidu 100 5000

google 110 5000

sohu 100 4500

guge 50 3000
```


