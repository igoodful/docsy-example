---
title: config
description: config
date: 2025-03-18
weight: 2000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;



| 参数                   | 作用                                                            | 建议值                                            | 可能值                                         | 作用域     |
|:-----------------------|:----------------------------------------------------------------|:--------------------------------------------------|:-----------------------------------------------|:-----------|
| `daemon`               | 以守护进程的方式运行Nginx                                       | `on`                                              |                                                | `global`   |
| `user`                 | 指定Nginx工作进程运行的用户和用户组                             | `nobody nobody`                                   |                                                | `global`   |
| `worker_processes`     | 设置Nginx的工作进程数                                           | CPU核心数相同                                     | `auto`                                         | `global`   |
| `pid`                  | 指定Nginx进程ID文件的路径                                       | `/var/run/nginx.pid`                              |                                                | `global`   |
| `error_log`            | 设置错误日志的路径和级别                                        | `logs/error.log debug`                            |                                                | `global`   |
| `use`                  | 选择 Nginx的事件驱动模型                                        | `epoll`                                           | `select、poll、kqueue、epoll、/dev/poll、eventport` | `events`   |
| `worker_connections`   | 设置每个工作进程能够处理的最大连接数                            | `65535`                                           |                                                | `events`   |
| `accept_mutex`         | 防止多个工作进程同时接受新连接时可能出现的惊群现象              | `on`                                              |                                                | `events`   |
| `include`              | 用于包含其他配置文件，方便管理和维护                             |                                                   |                                                | `http`     |
| `default_type`         | 全局默认映射类型                                                | `application/octet-stream`                        |                                                | `http`     |
| `sendfile`             | 开启高效的文件传输模式，减少磁盘 I/O 开销，提高文件传输效率       | `on`                                              |                                                | `http`     |
| `keepalive_timeout`    | 设置客户端与服务器之间的长连接超时时间                          | `65`                                              |                                                | `http`     |
| `gzip`                 | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `on`                                              |                                                | `http`     |
| `gzip_types`           | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `image/jpeg image/gif image/png`                  |                                                | `http`     |
| `gzip_comp_level`      | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `5`                                               |                                                | `http`     |
| `gzip_vary`            | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `on`                                              |                                                | `http`     |
| `gzip_buffers`         | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `16 8k`                                           |                                                | `http`     |
| `gzip_disable`         | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `"MSIE [1-6]\."`                                  |                                                | `http`     |
| `gzip_http_version`    | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `1.1;`                                            |                                                | `http`     |
| `gzip_min_length`      | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `2k;`                                             |                                                | `http`     |
| `gzip_proxied`         | 开启 gzip 压缩功能，减少数据传输量，提高网站访问速度              | `off`                                             |                                                | `http`     |
| `server`               | 一个http中对应多个server                                        |                                                   |                                                | `http`     |
| `listen`               | 指定服务器监听的端口                                            | `80`、`443 ssl` 、`443 ssl http2`                   |                                                | `server`   |
| `server_name`          | 设置服务器的名称，可以是域名或IP地址                             | `igoodful.com;`                                   |                                                | `server`   |
| `ssl_certificate`      |                                                                 | `/etc/nginx/ssl/example.com.crt`                  |                                                | `server`   |
| `ssl_certificate_key`  |                                                                 | `/etc/nginx/ssl/example.com.key`                  |                                                | `server`   |
| `access_log`           | 指定访问日志的路径和格式                                        | `/var/log/nginx/access.log main;`                 |                                                | `server`   |
| `error_log`            | 仅记录当前虚拟主机相关的错误信息，覆盖http块中的全局错误日志设置 |                                                   |                                                | `server`   |
| `location`             | 一个http中对应多个location                                      |                                                   |                                                | `server`   |
| `root`                 | 在server设置的是location                                        | `/data/web`                                       |                                                | `server`   |
| `limit_conn`           | 限制每个IP最多10个并发连接                                      | `conn_limit_per_ip 10`                            |                                                | `server`   |
| `limit_conn_status`    | 返回 429 Too Many Requests 状态码                               | `429`                                             |                                                | `server`   |
| `limit_conn_log_level` | 超出限制时的错误日志级别                                        | `warn`                                            |                                                | `server`   |
| `root`                 | 在location设置的是location匹配访问路径的上一层目录              | `location /flv/;root /data/web; 》 /data/web/flv/` |                                                | `location` |
| `expires`              |                                                                 | `max`                                             |                                                | `location` |
| `access_log`           |                                                                 | `off`                                             |                                                | `location` |
| `log_not_found`        |                                                                 | `off`                                             |                                                | `location` |
| `rewrite`              | RUL地址的重定向                                                 | `^/das/(.*) /$1 break`                            |                                                | `location` |
| `index`                |                                                                 | `index.html index.htm`                            |                                                | `location` |
| `proxy_pass`           |                                                                 | `http://api/` ，`http://api`                       |                                                | `location` |
| `proxy_http_version`   |                                                                 | `1.1`                                             |                                                | `location` |
| `proxy_redirect`       |                                                                 | `off`                                             |                                                | `location` |
| `limit_rate`           | 限制下载速度                                                    | `1m`                                              |                                                | `location` |
| `limit_rate_after`     | 前 10MB 不限速                                                  | `10m`                                             |                                                | `location` |
| `limit_rate`           | 设置请求的根目录                                                | `off`                                             |                                                | `location` |
| `limit_rate`           | 设置请求的根目录                                                | `off`                                             |                                                | `location` |
| `alias`                | 只能位于location块中                                            | `/data/www/`                                      |                                                | `server`   |
| `error_page`           |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |
| `server`               |                                                                 |                                                   |                                                | `server`   |







## 追加：root

将请求的 URI 附加到 root 指定的路径后面。

适用于简单的目录映射，特别是当请求的 URI 结构与文件系统结构一致时。

路径结尾是否包含斜杠不影响最终路径。

root 通常比 alias 更高效，因为它只是简单地拼接路径。

作用：root 指令用于设置请求的根目录。当 Nginx 收到一个请求时，它会将请求的 URI 附加到 root 指定的路径后面，形成完整的文件路径


- `/images/example.png` $\Longrightarrow$ `/data/www/images/example.png`
```bash
location /images/ {
    root /data/www;
}

```



```bash


```


## 替换：alias

将请求的 URI 替换为 alias 指定的路径

适用于需要将请求的 URI 映射到不同的文件系统路径时

alias：路径结尾必须包含斜杠，否则会导致路径错误。

作用：alias 指令用于设置请求的别名。当 Nginx 收到一个请求时，它会将请求的 URI 替换为 alias 指定的路径。

- `/images/example.png` $\Longrightarrow$ `/data/www/example.png`
```bash
location /images/ {
    alias /data/www/;
}

```




## proxy_pass

1. 如果在proxy_pass后面的url加/，表示绝对根路径；

2. 如果没有/，表示相对路径，把匹配的路径部分也给代理走。


- `http://192.168.1.1/proxy/test.html` $\Longrightarrow$ `http://127.0.0.1/test.html`
```bash
location /proxy/ {
proxy_pass http://127.0.0.1/;
}

```

- 保留原始路径：`http://192.168.1.1/proxy/test.html` $\Longrightarrow$ `http://127.0.0.1/proxy/test.html`
如果 proxy_pass 后不带路径，则保留原始路径
```bash
location /proxy/ {
proxy_pass http://127.0.0.1;
}

```


- 路径重写：`http://192.168.1.1/proxy/test.html` $\Longrightarrow$ `http://127.0.0.1/aaa/test.html`
```bash
location /proxy/ {
proxy_pass http://127.0.0.1/aaa/;
}

```


- `http://192.168.1.1/proxy/test.html` $\Longrightarrow$ `http://127.0.0.1/aaatest.html`
```bash
location /proxy/ {
proxy_pass http://127.0.0.1/aaa;
}

```




## proxy_set_header

反向代理场景下，后端服务器默认只能看到 Nginx 的 IP 地址。通过 proxy_set_header 可以传递客户端的真实 IP：
1. `proxy_set_header Host $host;` 传递客户端请求的原始域名。
2. `proxy_set_header X-Real-IP $remote_addr;` 传递客户端的真实 IP 地址。
3. `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;` 追加客户端 IP 到代理链（多个代理时会记录所有 IP）。
4. `proxy_set_header X-Forwarded-Proto $scheme;` 传递客户端请求的协议（http 或 https）
5. `proxy_set_header X-Custom-Header "MyValue";` 添加自定义请求头，用于后端服务器识别或调试：
6. `proxy_set_header X-Debug-Info "$host $remote_addr $time_local";` 通过自定义请求头记录调试信息，后端服务器会收到 X-Debug-Info: example.com 192.168.1.1 10/Oct/2023:14:28:00 +0000
7. `proxy_set_header X-Unwanted-Header "";` 如果需要清除某些请求头，可以将值设置为空





## 负载均衡


```bash

upstream backend_servers {
server 192.168.1.10:80;
server 192.168.1.11:80;
server 192.168.1.12:80;
}
server {
listen 80;
server_name example.com;
location / {
proxy_pass http://backend_servers;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
}
}

```













