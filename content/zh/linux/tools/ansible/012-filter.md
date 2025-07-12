---
title: 12. 过滤器
description: filter
date: 2023-10-12
weight: 1200


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}

{{< alert color="secondary" >}}

{{< /alert >}}




|                              |                                             |        |
|:-----------------------------|:--------------------------------------------|:-------|
| upper                        | 字符串/列表大写                             |        |
| lower                        | 字符串/列表小写                             |        |
| capitalize                   | 字符串首字母大写                            |        |
| first                        | 字符串/列表第一个字符                       |        |
| last                         | 字符串/列表最后一个字符                     |        |
| trim                         | 字符串去掉两端空格                          |        |
| count                        | 字符串/列表长度                             |        |
| length                       | 字符串/列表长度                             |        |
| list                         | 字符串转列表                                |        |
| shuffle                      | 字符串/列表转列表，且随机打乱顺序            |        |
| int                          | 转成整数                                    |        |
| float                        | 转成浮点数                                  |        |
| abs                          | 绝对值                                      |        |
| round                        | 四舍五入                                    |        |
| random                       | 随机数                                      |        |
| join                         | 列表转字符串                                |        |
| min                          | 列表最小值                                  |        |
| max                          | 列表最大值                                  |        |
| sort                         | 列表排序                                    |        |
| sum                          | 列表求和                                    |        |
| unique                       | 列表去重                                    |        |
| union                        | 列表并集                                    |        |
| intersect                    | 列表交集                                    |        |
| difference                   | 列表差集                                    |        |
| symmetric_difference         | 取出两个列表中各自独有元素，重复元素只留一个 |        |
| default                      | 如果变量没有定义，临时返回一个指定好的默认值 |        |
| basename                     | 获取路径字符串的文件名                      |        |
| dirname                      | 获取路径字符串的目录路径                    |        |
| realpath                     | 查看软链接所指向的真正文件路径              | pwd -P |
| relpath                      | 获取指定路径的相对路径                      |        |
| splitext                     | 将文件名与后缀名分开                        |        |
| bool                         |                                             |        |
| map                          |                                             |        |
| to_datetime                  |                                             |        |
| b64encode                    | base64编码方式对字符串进行编码              |        |
| b64decode                    | base64编码方式对字符串进行解码              |        |
| md5                          | 返回输入数据的MD5值                         |        |
| sha1                         | 使用SHA-1算法返回输入数据的散列值           |        |
| checksum                     | 使用SHA-1算法返回输入数据的散列值           |        |
| hash                         | 默认使用SHA-1算法                           |        |
| hash('sha1')                 | 使用sha1算法对字符串进行哈希                |        |
| hash('md5')                  | 使用md5算法对字符串进行哈希                 |        |
| password_hash('sha256')      | 使用sha256算法对字符串进行哈希              |        |
| password_hash('sha512')      | 使用sha512算法对字符串进行哈希              |        |
| password_hash('sha512','nb') | 可以使用指定字符串加盐                      |        |
| dict2items                   | 字典转列表                                  |        |
| items2dict                   | 列表转字典                                  |        |





random：取随机数。
- 从0到100中随机返回一个随机数：{{ 100 | random }}。
- 从5到10中随机返回一个随机数：{{ 10 | random(start=5) }}
- 从0到15中随机返回一个随机数，这个随机数是5的倍数：{{ 15 | random(step=5) }}




## length



```yaml
monitor_deploy_mode: "{{ groups['monitor'] | length }}"

```














