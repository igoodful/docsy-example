---
title: deploy
description: 部署
date: 2025-06-01
weight: 100
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{< alert >}}

{{< /alert >}}

##

```viz-dot
digraph "install" {
rankdir=LR;
edge [
        fontsize="12"
];
node [
        shape = octagon;
        fontsize = "12"
];


"install" -> "containerd";
"install" -> "runc";
"install" -> "cni";

}
```


## 安装
> 下载地址：https://github.com/containerd/containerd/releases

```sql
rm -rf /opt/cni
rm -rf /opt/containerd
rm -rf /opt/containerd
rm -rf /etc/cni/net.d/10-containerd-net.conflist
rm -rf /etc/crictl.yaml
rm -rf /etc/systemd/system/containerd.service
rm -rf /usr/local/sbin/runc
rm -rf /usr/local/bin/ctd-decoder
rm -rf /usr/local/bin/critest
rm -rf /usr/local/bin/containerd-shim-runc-v1
rm -rf /usr/local/bin/containerd-stress
rm -rf /usr/local/bin/containerd-shim-runc-v2
rm -rf /usr/local/bin/containerd
rm -rf /usr/local/bin/containerd-shim
rm -rf /usr/local/bin/ctr
rm -rf /usr/local/bin/crictl


rpm -qa |grep libseccomp

wget http://mirror.centos.org/centos/7/os/x86_64/Packages/libseccomp-2.3.1-4.el7.x86_64.rpm

yum install libseccomp-2.3.1-4.el7.x86_64.rpm -y

wget https://github.com/containerd/containerd/releases/download/v1.5.5/cri-containerd-cni-1.5.5-linux-amd64.tar.gz

wget https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz

tar -tf cri-containerd-cni-1.5.5-linux-amd64.tar.gz

tar -C / -xzf cri-containerd-cni-1.5.5-linux-amd64.tar.gz

tar Cxzvf /usr/local containerd-1.7.7-linux-amd64.tar.gz

echo "export PATH=\$PATH:/usr/local/bin:/usr/local/sbin" >> ~/.bashrc

source ~/.bashrc

mkdir -p /etc/containerd

containerd config default > /etc/containerd/config.toml

```




## 配置

### 配置service
> containerd.service文件内容如下：
> 地址：https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
```sql
shell> cat /etc/systemd/system/containerd.service

# Copyright The containerd Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

[Unit]
Description=containerd container runtime
Documentation=https://containerd.io
After=network.target local-fs.target

[Service]
#uncomment to fallback to legacy CRI plugin implementation with podsandbox support.
#Environment="DISABLE_CRI_SANDBOXES=1"
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/local/bin/containerd

Type=notify
Delegate=yes
KillMode=process
Restart=always
RestartSec=5

# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNPROC=infinity
LimitCORE=infinity

# Comment TasksMax if your systemd version does not supports it.
# Only systemd 226 and above support this version.
TasksMax=infinity
OOMScoreAdjust=-999

[Install]
WantedBy=multi-user.target

```
这里有两个重要的参数：

- Delegate: 这个选项允许 containerd 以及运行时自己管理自己创建容器的 cgroups。如果不设置这个选项，systemd 就会将进程移到自己的 cgroups 中，从而导致 containerd 无法正确获取容器的资源使用情况。

- KillMode: 这个选项用来处理 containerd 进程被杀死的方式。默认情况下，systemd 会在进程的 cgroup 中查找并杀死 containerd 的所有子进程。KillMode 字段可以设置的值如下。

  - control-group（默认值）：当前控制组里面的所有子进程，都会被杀掉
  - process：只杀主进程
  - mixed：主进程将收到 SIGTERM 信号，子进程收到 SIGKILL 信号
  - none：没有进程会被杀掉，只是执行服务的 stop 命令
我们需要将 KillMode 的值设置为 process，这样可以确保升级或重启 containerd 时不杀死现有的容器。

### 配置文件

1. 配置默认仓库：Containerd与docker的默认仓库均为docker.io
   如果配置中未指定 mirror 为 docker.io，containerd也会自动加载 docker.io 配置。
   与docker不同的是，containerd可以修改，docker.io对应的endpoint（默认为https://registry-1.docker.io ），而docker无法修改。
2. Containerd目前没有直接配置镜像加速的功能，但containerd中可以修改docker.io对应的 endpoint，所以可以通过修改endpoint来实现镜像加速下载。因为endpoint是轮询访问，所以可以给docker.io配置多个仓库地址来实现加速地址+默认仓库地址。

这个配置文件比较复杂，我们可以将重点放在其中的 plugins 配置上面，仔细观察我们可以发现每一个顶级配置块的命名都是 plugins."io.containerd.xxx.vx.xxx" 这种形式，每一个顶级配置块都表示一个插件，其中 io.containerd.xxx.vx 表示插件的类型，vx 后面的 xxx 表示插件的 ID，我们可以通过 ctr 查看插件列表：

```sql
mkdir -p /etc/containerd/

containerd config default > /etc/containerd/config.toml

```

> 在下面的 plugins."io.containerd.grpc.v1.cri".registry 下面填写仓库地址
> 在下面的 plugins."io.containerd.grpc.v1.cri".registry 下面填写仓库地址
> 套接字默认位置：/run/containerd/containerd.sock
> 设置使用systemd为cgroup驱动程序：SystemdCgroup = true
> 配置sandbox_image版本：sandbox_image = "registry.k8s.io/pause:3.6"  更改为：registry.aliyuncs.com/google_containers/pause:3.6
> bin_dir = "/opt/cni/bin"
> conf_dir = "/etc/cni/net.d"
```toml
    [plugins."io.containerd.grpc.v1.cri".registry]
      config_path = ""

      [plugins."io.containerd.grpc.v1.cri".registry.auths]

      [plugins."io.containerd.grpc.v1.cri".registry.configs]

      [plugins."io.containerd.grpc.v1.cri".registry.headers]

      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]

```

顶级配置块下面的子配置块表示该插件的各种配置，比如 cri 插件下面就分为 containerd、cni 和 registry 的配置，而 containerd 下面又可以配置各种 runtime，还可以配置默认的 runtime。比如现在我们要为镜像配置一个加速器，那么就需要在 cri 配置块下面的 registry 配置块下面进行配置 registry.mirrors：

```yaml
[plugins."io.containerd.grpc.v1.cri".registry]
  [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
      endpoint = ["https://bqr1dr1n.mirror.aliyuncs.com"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."k8s.gcr.io"]
      endpoint = ["https://registry.aliyuncs.com/k8sxio"]

```
- registry.mirrors."xxx": 表示需要配置 mirror 的镜像仓库，例如 registry.mirrors."docker.io" 表示配置 docker.io 的 mirror。
- endpoint: 表示提供 mirror 的镜像加速服务，比如我们可以注册一个阿里云的镜像服务来作为 docker.io 的 mirror。


## 启动

```bash
systemctl daemon-reload && systemctl restart containerd.service

systemctl enable containerd
systemctl status containerd
```














