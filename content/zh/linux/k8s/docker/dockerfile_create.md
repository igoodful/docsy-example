---
title: 定制镜像
description: Dockerfile
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
digraph "Dockerfile" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "Dockerfile" -> "search";
        "Dockerfile" -> "Dockerfile";
        "Dockerfile" -> "pull";
        "Dockerfile" -> "history";
        "Dockerfile" -> "rmi";
        "Dockerfile" -> "tag";
        "Dockerfile" -> "inspect";

        "Dockerfile" -> "build";



}
```




| 命令 | 功能           | 示例          | 备注                        |
|:-----|:---------------|:--------------|:----------------------------|
| FROM | 指定基础镜像   | FROM centos:7 | 必须是第一条指令            |
| RUN  | 运行指定的命令 |               | 多少个RUN就构建了多少层镜像 |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |
|      |                |               |                             |














## FROM

## LABEL


## RUN




## CMD




## ENV



## ADD


## COPY



## ENTRYPOINT




## VOLUME



## USER


## WORKDIR



## ARG



## STOPSIGNAL




## HEALTHCHECK








## 构建


#### build

```bash
docker build -f dockerfile -t centos7_nginx:test  .


```

#### run
```bash
docker run -d --name  nginx_img -p 36128:80  centos7_nginx:test bash

```





```dockerfile
FROM ubuntu:24.04
LABEL python.ci-image.authors="Barry Warsaw <barry@python.org>"

ARG SERIES
ENV SERIES=${SERIES}

# Enable source repositories so we can use `apt build-dep` to get all required
# build dependencies.
RUN sed -i 's/^Types: deb$/Types: deb deb-src/' /etc/apt/sources.list.d/ubuntu.sources

ADD get-pythons.sh /usr/local/bin/get-pythons.sh
ADD get_versions.py /usr/local/bin/get_versions.py
ADD test_pythons.py /usr/local/bin/test_pythons.py

# Set Debian front-end to non-interactive so that apt doesn't ask for
# prompts later.
ENV  DEBIAN_FRONTEND=noninteractive

RUN useradd runner --create-home && \
    # Create and change permissions for builds directory.
    mkdir /builds && \
    chown runner /builds && \
    export LC_ALL=C.UTF-8 && export LANG=C.UTF-8

# Use a new layer here so that these static changes are cached from above
# layer.  Update Ubuntu and install the build-deps.
RUN apt -qq -o=Dpkg::Use-Pty=0 update && \
    apt -qq -o=Dpkg::Use-Pty=0 -y dist-upgrade && \
    # Use python3.12 build-deps for Ubuntu 22.04
    apt -qq -o=Dpkg::Use-Pty=0 build-dep -y python3.12 && \
    apt -qq -o=Dpkg::Use-Pty=0 install -y python3-pip python3-venv && \
    apt -qq -o=Dpkg::Use-Pty=0 install -y wget unzip git jq curl && \
    # Remove apt's lists to make the image smaller.
    rm -rf /var/lib/apt/lists/*

# Get and install all versions of Python.
RUN ./usr/local/bin/get_versions.py && ./usr/local/bin/get-pythons.sh > /dev/null
RUN python3.12 -m pip install mypy codecov tox pipx
RUN mv versions.txt /usr/local/bin/versions.txt

# Install uv into /usr/local for convenience: https://docs.astral.sh/uv/
WORKDIR /tmp
RUN curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/usr/local/bin" sh

# Switch to runner user and set the workdir.
USER runner
WORKDIR /home/runner


```



