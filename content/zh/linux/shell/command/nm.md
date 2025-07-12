---
title: nm
description: nm
date: 2025-02-19
weight: 300000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert title="curl参数详解" color="secondary">}}

{{< /alert >}}



## 1. 简介


## 2. 选项

| 选项    | 作用             | 示例                                   |
| :------ | :--------------- | :------------------------------------- |
| `-u`    | 指定账号密码     | `-u admin:'password'`                  |
| `-o`    | 指定文件名称     | `-o new_filename.log`                  |
| `-d -X` | 发送POST请求     | `-d "key1=value1&key2=value2" -X POST` |
| `-H`    | 附加头部信息     | `-H "Content-Type: application/json"`  |
| `-L`    | 跟随重定向       | `-L`                                   |
| `-k`    | 忽略SSL证书验证  | `-k`                                   |
| `-s`    | 静默模式发送请求 | `-s`                                   |
| `-v`    | 详细模式发送请求 | `-v`                                   |

## 3. 案例

```bash
curl -u admin:'password' http://172.17.140.146:9092/api/v1/query?query=up&time=2025-02-11T20:10:51.781Z

curl -u admin:'password' 'http://172.17.140.146:9092/api/v1/query_range?query=up&start=1739794251&end=1739880651&step=10' > a.log




```

## 4. 问题



## 5. 参考




