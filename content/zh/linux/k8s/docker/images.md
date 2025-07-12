---
title: 镜像管理
description: images
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
- https://graphviz.org/download/
- https://hub.docker.com/explore

{{< /alert >}}


## 图解

```viz-dot
digraph "images" {
rankdir=TB;
edge [
        fontsize="12"
];
node [
        shape = octagon;
        fontsize = "12"
];
"images" -> "search";
"images" -> "images";
"images" -> "pull";
"images" -> "history";
"images" -> "rmi";
"images" -> "tag";
"images" -> "inspect";
"images" -> "build";
"images" -> "commit";
"images" -> "load";
"images" -> "save";

}
```



## images

```bash


```


## search

- 从`Docker Hub`查找镜像：
- https://hub.docker.com/explore

```bash
docker search --filter "is-official=true" --filter "stars=3" ubuntu




```


## save

```bash
docker save -o ubuntu-25.10.tar ubuntu:25.10

docker save 0fdf2b4c26d3 > hangge_server.tar

```



## load

```bash
docker load < ubuntu-25.10.tar

```




## export

```bash
docker ps

docker export container_id -o ubuntu-25.10.tar

docker export f299f501774c > ubuntu-25.10.tar

```

## import

```bash
docker import - ubuntu-25.10 < ubuntu-25.10.tar


```


## 拉取镜像: `pull`

```bash
docker pull --platform=arm64 nginx:latest

docker pull --platform=amd64 nginx:latest

#
docker pull docker.m.daocloud.io/hello-world
docker pull docker.m.daocloud.io/ubuntu:24.04

```


## 删除镜像: `rmi`

```bash
docker images

docker rmi  [imageID]

docker rmi -f [imageID]

```

- 删除所有镜像

```bash
docker stop $(docker ps -aq)

docker rm -f $(docker ps -aq)

docker rmi -f $(docker images -q)

```






## windows上的Docker Desktop利用vpn来下载镜像

#### Windows PowerShell

```bash
PS C:\Users> docker images
REPOSITORY   TAG                    IMAGE ID       CREATED         SIZE
ubuntu       25.10                  daf1e77671be   2 weeks ago     76MB
rockylinux   9.3.20231119-minimal   dfaa211c6b30   18 months ago   118MB
PS C:\Users> docker save -o ubuntu-25.10.tar ubuntu:25.10
PS C:\Users>

```






