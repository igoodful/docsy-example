---
title: 安装、配置、启动
date: 2023-10-12
weight: 10000
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.


---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}


## 安装

```bash
# 配置 yum 源
cat > /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
EOF

yum list kubelet --showduplicates | sort -r

yum install -y kubectl-1.27.0-0 kubelet-1.27.0-0 kubeadm-1.27.0-0

# /etc/sysconfig/kubelet 配置 systemd
echo "KUBELET_EXTRA_ARGS=\"--cgroup-driver=systemd\"" > /etc/sysconfig/kubelet

systemctl start kubelet && systemctl enable kubelet && systemctl status kubelet

# 查看指定版本的k8s所需的镜像
kubeadm config images list --kubernetes-version=v1.27.0

# 在master节点执行
kubeadm init --kubernetes-version=v1.27.0 --pod-network-cidr=10.96.0.0/16 --image-repository=registry.aliyuncs.com/google_containers --apiserver-advertise-address=172.17.120.144 --v=6



# 在work节点执行
kubeadm join 172.16.50.150:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:68d48107177ec63c7e36276e2087b00767884c56304fff8ab013efbe498cd61b
```
{{<alert color="secondary">}}

## 重新初始化
1. master节点上执行：kubeadm reset -f

2. work节点上执行：kubeadm reset -f

## k8s节点加入时[kubelet-check] 提示Initial timeout of 40s passed：

报错信息如下：
```bash
[root@k8s-node-71 ~]# kubeadm join 172.17.134.70:6443 --token tn0ulo.zkj0u9n9plmvuf57 --discovery-token-ca-cert-hash sha256:68bc26818ac9a9e8b61dd24134bfc6260dd961bd0928b3382326998243b7fcfb
[preflight] Running pre-flight checks
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...
[kubelet-check] Initial timeout of 40s passed.
error execution phase kubelet-start: error uploading crisocket: Unauthorized
To see the stack trace of this error execute with --v=5 or higher

```
### 详细报错信息如下：
```bash
[root@k8s-node-71 ~]# kubeadm join 172.17.134.70:6443 --token tn0ulo.zkj0u9n9plmvuf57 --discovery-token-ca-cert-hash sha256:68bc26818ac9a9e8b61dd24134bfc6260dd961bd0928b3382326998243b7fcfb  --v=5
I1015 11:32:27.098739   23172 join.go:412] [preflight] found NodeName empty; using OS hostname as NodeName
I1015 11:32:27.099272   23172 initconfiguration.go:117] detected and using CRI socket: unix:///var/run/containerd/containerd.sock
[preflight] Running pre-flight checks
I1015 11:32:27.099374   23172 preflight.go:93] [preflight] Running general checks
I1015 11:32:27.099450   23172 checks.go:280] validating the existence of file /etc/kubernetes/kubelet.conf
I1015 11:32:27.099464   23172 checks.go:280] validating the existence of file /etc/kubernetes/bootstrap-kubelet.conf
I1015 11:32:27.099488   23172 checks.go:104] validating the container runtime
I1015 11:32:27.158213   23172 checks.go:639] validating whether swap is enabled or not
I1015 11:32:27.158330   23172 checks.go:370] validating the presence of executable crictl
I1015 11:32:27.158380   23172 checks.go:370] validating the presence of executable conntrack
I1015 11:32:27.158414   23172 checks.go:370] validating the presence of executable ip
I1015 11:32:27.158447   23172 checks.go:370] validating the presence of executable iptables
I1015 11:32:27.158483   23172 checks.go:370] validating the presence of executable mount
I1015 11:32:27.158522   23172 checks.go:370] validating the presence of executable nsenter
I1015 11:32:27.158565   23172 checks.go:370] validating the presence of executable ebtables
I1015 11:32:27.158595   23172 checks.go:370] validating the presence of executable ethtool
I1015 11:32:27.158621   23172 checks.go:370] validating the presence of executable socat
I1015 11:32:27.158651   23172 checks.go:370] validating the presence of executable tc
I1015 11:32:27.158673   23172 checks.go:370] validating the presence of executable touch
I1015 11:32:27.158738   23172 checks.go:516] running all checks
I1015 11:32:27.173544   23172 checks.go:401] checking whether the given node name is valid and reachable using net.LookupHost
I1015 11:32:27.173967   23172 checks.go:605] validating kubelet version
I1015 11:32:27.255668   23172 checks.go:130] validating if the "kubelet" service is enabled and active
I1015 11:32:27.267286   23172 checks.go:203] validating availability of port 10250
I1015 11:32:27.267566   23172 checks.go:280] validating the existence of file /etc/kubernetes/pki/ca.crt
I1015 11:32:27.267599   23172 checks.go:430] validating if the connectivity type is via proxy or direct
I1015 11:32:27.267680   23172 checks.go:329] validating the contents of file /proc/sys/net/bridge/bridge-nf-call-iptables
I1015 11:32:27.267745   23172 checks.go:329] validating the contents of file /proc/sys/net/ipv4/ip_forward
I1015 11:32:27.267784   23172 join.go:529] [preflight] Discovering cluster-info
I1015 11:32:27.267846   23172 token.go:80] [discovery] Created cluster-info discovery client, requesting info from "172.17.134.70:6443"
I1015 11:32:27.283846   23172 token.go:118] [discovery] Requesting info from "172.17.134.70:6443" again to validate TLS against the pinned public key
I1015 11:32:27.296703   23172 token.go:135] [discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "172.17.134.70:6443"
I1015 11:32:27.296752   23172 discovery.go:52] [discovery] Using provided TLSBootstrapToken as authentication credentials for the join process
I1015 11:32:27.296767   23172 join.go:543] [preflight] Fetching init configuration
I1015 11:32:27.296776   23172 join.go:589] [preflight] Retrieving KubeConfig objects
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
I1015 11:32:27.307995   23172 kubelet.go:74] attempting to download the KubeletConfiguration from ConfigMap "kubelet-config"
I1015 11:32:27.311980   23172 interface.go:432] Looking for default routes with IPv4 addresses
I1015 11:32:27.311996   23172 interface.go:437] Default route transits interface "eth0"
I1015 11:32:27.312269   23172 interface.go:209] Interface eth0 is up
I1015 11:32:27.312322   23172 interface.go:257] Interface "eth0" has 2 addresses :[172.17.134.71/20 fe80::216:3eff:feeb:f904/64].
I1015 11:32:27.312338   23172 interface.go:224] Checking addr  172.17.134.71/20.
I1015 11:32:27.312346   23172 interface.go:231] IP found 172.17.134.71
I1015 11:32:27.312375   23172 interface.go:263] Found valid IPv4 address 172.17.134.71 for interface "eth0".
I1015 11:32:27.312385   23172 interface.go:443] Found active IP 172.17.134.71
I1015 11:32:27.317290   23172 preflight.go:104] [preflight] Running configuration dependant checks
I1015 11:32:27.317323   23172 controlplaneprepare.go:225] [download-certs] Skipping certs download
I1015 11:32:27.317339   23172 kubelet.go:121] [kubelet-start] writing bootstrap kubelet config file at /etc/kubernetes/bootstrap-kubelet.conf
I1015 11:32:27.318019   23172 kubelet.go:136] [kubelet-start] writing CA certificate at /etc/kubernetes/pki/ca.crt
I1015 11:32:27.318615   23172 kubelet.go:157] [kubelet-start] Checking for an existing Node in the cluster with name "k8s-node-71" and status "Ready"
I1015 11:32:27.321324   23172 kubelet.go:172] [kubelet-start] Stopping the kubelet
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...
I1015 11:32:28.496846   23172 cert_rotation.go:137] Starting client certificate rotation controller
I1015 11:32:28.497502   23172 kubelet.go:220] [kubelet-start] preserving the crisocket information for the node
I1015 11:32:28.497554   23172 patchnode.go:31] [patchnode] Uploading the CRI Socket information "unix:///var/run/containerd/containerd.sock" to the Node API object "k8s-node-71" as an annotation
[kubelet-check] Initial timeout of 40s passed.
Unauthorized
error uploading crisocket
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/join.runKubeletStartJoinPhase
	cmd/kubeadm/app/cmd/phases/join/kubelet.go:222
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).Run.func1
	cmd/kubeadm/app/cmd/phases/workflow/runner.go:259
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).visitAll
	cmd/kubeadm/app/cmd/phases/workflow/runner.go:446
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).Run
	cmd/kubeadm/app/cmd/phases/workflow/runner.go:232
k8s.io/kubernetes/cmd/kubeadm/app/cmd.newCmdJoin.func1
	cmd/kubeadm/app/cmd/join.go:179
github.com/spf13/cobra.(*Command).execute
	vendor/github.com/spf13/cobra/command.go:916
github.com/spf13/cobra.(*Command).ExecuteC
	vendor/github.com/spf13/cobra/command.go:1040
github.com/spf13/cobra.(*Command).Execute
	vendor/github.com/spf13/cobra/command.go:968
k8s.io/kubernetes/cmd/kubeadm/app.Run
	cmd/kubeadm/app/kubeadm.go:50
main.main
	cmd/kubeadm/kubeadm.go:25
runtime.main
	/usr/local/go/src/runtime/proc.go:250
runtime.goexit
	/usr/local/go/src/runtime/asm_amd64.s:1598
error execution phase kubelet-start
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).Run.func1
	cmd/kubeadm/app/cmd/phases/workflow/runner.go:260
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).visitAll
	cmd/kubeadm/app/cmd/phases/workflow/runner.go:446
k8s.io/kubernetes/cmd/kubeadm/app/cmd/phases/workflow.(*Runner).Run
	cmd/kubeadm/app/cmd/phases/workflow/runner.go:232
k8s.io/kubernetes/cmd/kubeadm/app/cmd.newCmdJoin.func1
	cmd/kubeadm/app/cmd/join.go:179
github.com/spf13/cobra.(*Command).execute
	vendor/github.com/spf13/cobra/command.go:916
github.com/spf13/cobra.(*Command).ExecuteC
	vendor/github.com/spf13/cobra/command.go:1040
github.com/spf13/cobra.(*Command).Execute
	vendor/github.com/spf13/cobra/command.go:968
k8s.io/kubernetes/cmd/kubeadm/app.Run
	cmd/kubeadm/app/kubeadm.go:50
main.main
	cmd/kubeadm/kubeadm.go:25
runtime.main
	/usr/local/go/src/runtime/proc.go:250
runtime.goexit
	/usr/local/go/src/runtime/asm_amd64.s:1598


```
### 解决办法：
1. 在该work节点上执行：kubeadm reset -f

2. 清理kubelet的配置：rm -rf $HOME/.kube/config

3. 加入集群：kubeadm join 172.17.134.70:6443 --token tn0ulo.zkj0u9n9plmvuf57 --discovery-token-ca-cert-hash sha256:68bc26818ac9a9e8b61dd24134bfc6260dd961bd0928b3382326998243b7fcfb  --v=5

{{</alert>}}
Is there any initial setup users need to do after installation to try your project?

## 在master节点上安装网络插件calico

```bash
# https://docs.tigera.io/archive/v3.24/manifests/calico.yaml

wget -o calico.yaml https://docs.tigera.io/archive/v3.24/manifests/calico.yaml

# CALICO_IPV4POOL_CIDR 设置为kubeadm init时的--pod-network-cidr的值10.96.0.0/16
# CALICO_DISABLE_FILE_LOGGING 设置为true

kubectl apply -f calico.yaml

```










