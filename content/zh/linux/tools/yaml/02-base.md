---
title: 二、纯量
description: 纯量是最基本的，不可再分的值
date: 2023-10-31
weight: 200


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- [yaml](https://www.cnblogs.com/nf01/articles/15686885.html)
{{< /alert >}}


{{< alert  color="primary">}}

{{< /alert >}}


## 一、整数

## 二、浮点

## 三、布尔


## 四、字符串

#### 1. 不用引号
- 字符串之中不能包含空格或特殊字符

#### 2. 双引号
- 不会转义字符串里面的特殊字符，特殊字符作为本身想表示的意思

#### 3. 单引号
- 会将字符串里面的特殊字符转义为字符串处理

单引号之中如果还有单引号，必须连续使用两个单引号转义
```yaml
str: 'labor''s day'
```
#### 4. 多行字符串
```yaml
str: 这是一段
  多行
  字符串
```


## 五、日期
> 日期必须使用ISO 8601格式，即yyyy-MM-dd
```yaml
mem: 2024-04-04
```

## 六、时间
> 时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区

```yaml
current: 2024-05-05T15:02:31+08:00
```


## 七、null
> 使用~表示
>
```yaml
name: ~
```













