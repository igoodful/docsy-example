---
title: compose管理
description: compose
date: 2023-10-26
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- [docker](https://docs.docker.com/reference/cli/docker/)

{{< /alert >}}




## 8字段
```viz-dot
digraph "node" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "node" -> "ls";
        "node" -> "ps";
        "node" -> "demote";
        "node" -> "update";
        "node" -> "inspect";
        "node" -> "promote";
        "node" -> "rm";




}
```


| 命令                   | 功能                                            | 示例 |
|:-----------------------|:----------------------------------------------|:-----|
| docker compose alpha   | 实验命令                                        |      |
| docker compose build   | 构建或重建服务                                  |      |
| docker compose config  | 以规范格式解析、解析和渲染撰写文件               |      |
| docker compose cp      | 在服务容器和本地文件系统之间复制文件/文件夹     |      |
| docker compose create  | 为服务创建容器                                  |      |
| docker compose down    | 停止并删除容器、网络                             |      |
| docker compose events  | 从容器接收实时事件                              |      |
| docker compose exec    | 在正在运行的容器中执行命令                      |      |
| docker compose images  | 列出创建的容器使用的镜像                        |      |
| docker compose kill    | 强制停止服务容器                                |      |
| docker compose logs    | 查看容器的输出                                  |      |
| docker compose ls      | 列出正在运行的撰写项目                          |      |
| docker compose pause   | 暂停服务                                        |      |
| docker compose port    | 打印端口绑定的公共端口                          |      |
| docker compose ps      | 列出容器                                        |      |
| docker compose pull    | 拉取服务镜像                                    |      |
| docker compose push    | 推送服务图片                                    |      |
| docker compose restart | 重启服务容器                                    |      |
| docker compose rm      | 删除已停止的服务容器                            |      |
| docker compose run     | 在服务上运行一次性命令                          |      |
| docker compose start   | 启动服务                                        |      |
| docker compose stop    | 停止服务                                        |      |
| docker compose top     | 显示正在运行的进程                              |      |
| docker compose unpause | 取消暂停服务                                    |      |
| docker compose up      | 创建并启动容器                                  |      |
| docker compose version | 显示 Docker Compose 版本信息                    |      |
| docker compose wait    | 阻塞直到第一个服务容器停止                      |      |
| docker compose watch   | 监视服务的构建上下文并在文件更新时重建/刷新容器 |      |