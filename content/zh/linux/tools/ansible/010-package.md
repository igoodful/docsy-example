---
title: 10. 软件包管理
description: 软件包管理
date: 2023-10-12
weight: 1000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
- [官网](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/)
{{< /alert >}}

{{< alert color="primary" >}}

ansible的输出的颜色提示说明:

黄色：对远程节点进行相应修改

绿色：对远程节点不进行相应修改，或者只是对远程节点信息进行查看

红色：操作命令有异常

紫色：表示对命令执行发出警告信息会给一下建议

{{< /alert >}}


```viz-dot
digraph "system" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"系统管理"->"service";
"系统管理"->"systemd";
"系统管理"->"cron";
"系统管理"->"firewalld";
"系统管理"->"hostname";
"系统管理"->"timezone";
"系统管理"->"hostname";
"系统管理"->"hostname";
}
```



## 一、yum

#### 安装软件
```yaml
 - name: "Add the user: igoodful"
   become: yes
   ansible.builtin.yum:
     name: httpd
     state: present

# 安装指定版本的软件包
- name: install one specific version of Apache
  become: yes
  yum:
    name: httpd24u-2.4.41
    state: present

# 通过本地rpm包安装软件
- name: install nginx rpm from a local file
  become: yes
  yum:
    name: "/data/nginx-1.16.1-1.el7.x86_64.rpm"
    state: present

```


#### 只下载软件
```yaml
- name: Download the nginx package but do not install it specified download_dir
  become: yes
  yum:
    name: nginx
    state: latest
    download_only: yes
    download_dir: /data/
```


#### 卸载软件
```yaml
 - name: "Add the user: igoodful"
   become: yes
   ansible.builtin.yum:
     name: httpd
     state: absent
```

#### 列出包
```yaml
- name: List httpd* packages and register result to print with debug later.
  yum:
    list: httpd*
  register: result

- name: print the result
  debug:
    var: result
```

## 二、yum_repository


#### 示例
```yaml
 - name: Add multiple repositories into the same file
   yum_repository:
   name: test
   description: EPEL YUM repo
   file: external_repos
   baseurl: https://download.fedoraproject.org/pub/epel/$releasever/$basearch/
   gpgcheck: no

 - name: remove repo
   yum_repository:
     name: test
     file: external_repos
     state: absent


```




## 三、get_url




#### 1. 选项
| 选项           | 默认值 | 作用                                       |
|:---------------|:-------|:-----------------------------------------|
| url            |        | 下载资源的URL（支持http、https、ftp协议）      |
| dest           |        | 下载的资源在目标主机上的保存路径（绝对路径） |
| owner          |        | 属主                                       |
| group          |        | 属组                                       |
| mode           |        | 权限                                       |
| force          | no     | 是否强制下载                               |
| checksum       |        | 下载后计算摘要                             |
| timeout        |        | URL请求的超时时间，单位为s                  |
| url_username   |        | 基于http basic认证的用户名                 |
| url_password   |        | 基于http basic认证的密码                   |
| validate_certs | yes    | 是否校验SSL证书                            |



## 四、pip


#### 1. 选项


| 选项                     | 默认值  | 作用                                       |
|:-------------------------|:--------|:-----------------------------------------|
| chdir                    |         | 在运行命令之前 cd 进入该目录               |
| name                     |         | 下载的资源在目标主机上的保存路径（绝对路径） |
| version                  |         | 指定的要安装的 Python library 的版本号     |
| state                    | present | 模块状态                                   |
| virtualenv               |         | 权限                                       |
| virtualenv_command       |         | 用于创建虚拟环境的命令或命令的路径名       |
| virtualenv_python        |         | 用于创建虚拟环境的 Python 可执行文         |
| virtualenv_site_packages |         | URL请求的超时时间，单位为s                  |
| requirements             |         | pip需求文件的路径                          |
| extra_args               |         | 传递给pip的额外参数                        |
| umask                    |         | 是否校验SSL证书                            |


#### 2. 示例
```yaml
- name: Install bottle python package
  ansible.builtin.pip:
    name: bottle

- name: Install bottle python package on version 0.11
  ansible.builtin.pip:
    name: bottle==0.11

- name: Install bottle python package with version specifiers
  ansible.builtin.pip:
    name: bottle>0.10,<0.20,!=0.11

- name: Install multi python packages with version specifiers
  ansible.builtin.pip:
    name:
      - django>1.11.0,<1.12.0
      - bottle>0.10,<0.20,!=0.11

- name: Install python package using a proxy
  ansible.builtin.pip:
    name: six
  environment:
    http_proxy: 'http://127.0.0.1:8080'
    https_proxy: 'https://127.0.0.1:8080'

# You do not have to supply '-e' option in extra_args
- name: Install MyApp using one of the remote protocols (bzr+,hg+,git+,svn+)
  ansible.builtin.pip:
    name: svn+http://myrepo/svn/MyApp#egg=MyApp

- name: Install MyApp using one of the remote protocols (bzr+,hg+,git+)
  ansible.builtin.pip:
    name: git+http://myrepo/app/MyApp

- name: Install MyApp from local tarball
  ansible.builtin.pip:
    name: file:///path/to/MyApp.tar.gz

- name: Install bottle into the specified (virtualenv), inheriting none of the globally installed modules
  ansible.builtin.pip:
    name: bottle
    virtualenv: /my_app/venv

- name: Install bottle into the specified (virtualenv), inheriting globally installed modules
  ansible.builtin.pip:
    name: bottle
    virtualenv: /my_app/venv
    virtualenv_site_packages: yes

- name: Install bottle into the specified (virtualenv), using Python 2.7
  ansible.builtin.pip:
    name: bottle
    virtualenv: /my_app/venv
    virtualenv_command: virtualenv-2.7

- name: Install bottle within a user home directory
  ansible.builtin.pip:
    name: bottle
    extra_args: --user

- name: Install specified python requirements
  ansible.builtin.pip:
    requirements: /my_app/requirements.txt

- name: Install specified python requirements in indicated (virtualenv)
  ansible.builtin.pip:
    requirements: /my_app/requirements.txt
    virtualenv: /my_app/venv

- name: Install specified python requirements and custom Index URL
  ansible.builtin.pip:
    requirements: /my_app/requirements.txt
    extra_args: -i https://example.com/pypi/simple

- name: Install specified python requirements offline from a local directory with downloaded packages
  ansible.builtin.pip:
    requirements: /my_app/requirements.txt
    extra_args: "--no-index --find-links=file:///my_downloaded_packages_dir"

- name: Install bottle for Python 3.3 specifically, using the 'pip3.3' executable
  ansible.builtin.pip:
    name: bottle
    executable: pip3.3

- name: Install bottle, forcing reinstallation if it's already installed
  ansible.builtin.pip:
    name: bottle
    state: forcereinstall

- name: Install bottle while ensuring the umask is 0022 (to ensure other users can use it)
  ansible.builtin.pip:
    name: bottle
    umask: "0022"
  become: True
```

## 五、git




