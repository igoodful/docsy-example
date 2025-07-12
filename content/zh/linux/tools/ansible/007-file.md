---
title: 07. 文件操作
description: 文件操作
date: 2023-10-12
weight: 700


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
digraph "ansible" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"文件"->"file";
"文件"->"copy";
"文件"->"template";
"文件"->"fetch";
"文件"->"lineinfile";
"文件"->"blockinfile";
"文件"->"get_url";
"文件"->"stat";
"文件"->"archive";
"文件"->"unarchive";

}
```


## 一、stat
> stat 模块将获取指定文件或目录的信息，并使用 register 参数将其保存

| 选项         | 值   | 备注     |
| :----------- | :--- | :------- |
| path         |      | 文件路径 |
| follow       |      |          |
| get_checksum |      |          |
| get_mime     |      |          |



```yaml
- name: Check file
  stat: path=/data/mysql
  register: st
```

## 二、fetch

```bash
ansible-doc -s fetch
```
ansible-doc -s copy
- name: Copies files to remote locations.
action: copy
    backup=[yes|no]   # 拷贝的同时也创建一个包含时间戳信息的备份文件，默认为no
    dest=             # 目标路径，只能是绝对路径，如果拷贝的文件是目录，则目标路径必须也是目录
    content           # 直接以content给定的字符串或变量值作为文件内容保存到远程主机上，它会替代src选项
    directory_mode    # 当对目录做递归拷贝时，设置了directory_mode将会使得只拷贝新建文件，
                      # 旧文件不会被拷贝。默认未设置
    follow=[yes|no]   # 是否追踪到链接的源文件。
    force=[yes|no]    # 设置为yes(默认)时，将覆盖远程同名文件。设置为no时，忽略同名文件的拷贝。
    group             # 设置远程文件的所属组
    owner             # 设置远程文件的所有者
    mode=             # 设置远程文件的权限。使用数值表示时不能省略第一位，如0644。
                      # 也可以使用'u+rwx'或'u=rw,g=r,o=r'等方式设置。
    src=              # 拷贝本地源文件到远程，可使用绝对路径或相对路径。如果路径是目录，且目录后加了
                      # 斜杠"/"，则只会拷贝目录中的内容到远程，如果目录后不加斜杠，则拷贝目录本身和
                      # 目录内的内容到远程。

## 三、file

```bash
ansible-doc -s file
```


| 选项    | 值                                    | 备注         |
| :------ | :------------------------------------ | :----------- |
| path    |                                       | 路径         |
| src     |                                       | 链接时源文件 |
| state   | directory/file/touch/link/hard/absent | 文件类型     |
| owner   |                                       | 属主         |
| group   |                                       | 属组         |
| mode    |                                       | 权限         |
| recurse |                                       | 递归         |



```yaml

- name: create file
  file: path=/tmp/a.txt state=touch

- name: create directory
  file: path=/tmp/mysql_3306/data state=directory recurse=yes

- name: create more directory
  file: "{{ item }}" state=directory
  with_items:
    - /tmp/dir1
    - /tmp/dir2

- name: create soft link
  file: path=/tmp/hosts src=/etc/hosts state=link


- name: create hard link
  file: path=/tmp/hosts src=/etc/hosts state=hard


- name: remove file
  file: path=/tmp/hosts state=absent


```


```yaml
---
- hosts: localhost
  gather_facts: false
  tasks:
    - file:
        name: "/tmp/{{item}}"
        state: touch
      with_list:
        - "filename1"
        - "filename2"
```




## 四、copy


#### 1. 概览
```viz-dot
digraph "copy" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"copy"->"src";
"copy"->"dest";
"copy"->"content";
"copy"->"owner";
"copy"->"group";
"copy"->"mode";
"copy"->"backup";
"copy"->"checksum";
"copy"->"force";

}
```

#### 2. 选项
```bash
ansible-doc -s copy
```
src参数 ：用于指定需要copy的文件或目录。拷贝本地源文件到远程，可使用绝对路径或相对路径。如果路径是目录，且目录后加了斜杠"/"，则只会拷贝目录中的内容到远程，如果目录后不加斜杠，则拷贝目录本身和目录内的内容到远程。

dest参数 ：用于指定文件将被拷贝到远程主机的哪个目录中，dest为必须参数。目标路径，只能是绝对路径，如果拷贝的文件是目录，则目标路径必须也是目录

content参数 ：当不使用src指定拷贝的文件时，可以使用content直接指定文件内容，src与content两个参数必有其一，否则会报错。

force参数 : 当远程主机的目标路径中已经存在同名文件，并且与ansible主机中的文件内容不同时，是否强制覆盖，可选值有yes和no，默认值为yes，表示覆盖，如果设置为no，则不会执行覆盖拷贝操作，远程主机中的文件保持不变。

backup参数 : 当远程主机的目标路径中已经存在同名文件，并且与ansible主机中的文件内容不同时，是否对远程主机的文件进行备份，可选值有yes和no，当设置为yes时，会先备份远程主机中的文件，然后再将ansible主机中的文件拷贝到远程主机。

follow=[yes|no]   # 是否追踪到链接的源文件。

directory_mode    # 当对目录做递归拷贝时，设置了directory_mode将会使得只拷贝新建文件，

owner参数 : 指定文件拷贝到远程主机后的属主，但是远程主机上必须有对应的用户，否则会报错。

group参数 : 指定文件拷贝到远程主机后的属组，但是远程主机上必须有对应的组，否则会报错。

mode参数 : 指定文件拷贝到远程主机后的权限，如果你想将权限设置为”rw-r--r--“，则可以使用mode=0644表示，如果你想要在user对应的权限位上添加执行权限，则可以使用mode=u+x表示。使用数值表示时不能省略第一位，如0644。也可以使用'u+rwx'或'u=rw,g=r,o=r'等方式设置。


#### 3. 示例
```yaml

- name: copy file
  file: src=/root/tmp/a.txt dest=/root/
```


```bash
ansible mysql -m copy -a "src=/etc/my.cnf dest=/etc/"

# 该命令的作用是拷贝本机的/etc/passwd文件到本机的/tmp目录下。
ansible localhost -m copy -a 'src=/etc/passwd dest=/tmp'
```


`ansible localhost -m copy -a 'src=/etc/passwd dest=/tmp'`
- localhost 指定主机
- \-m copy 指定模块
- \-a 'src=/etc/passwd dest=/tmp'  指定模块参数




` ansible-doc -l | grep 'copy'`



```bash
# 详细的模块描述手册
ansible-doc copy

# 只包含模块参数用法的模块描述手册
ansible-doc -s copy
```

## 五、template
> template模块使用了Jinjia2模版语言，进行文档内变量的替换的模块

#### 1. 选项

```viz-dot
digraph "ansible.builtin.template" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"ansible.builtin.template"->"src";
"ansible.builtin.template"->"dest";
"ansible.builtin.template"->"owner";
"ansible.builtin.template"->"group";
"ansible.builtin.template"->"mode";
"ansible.builtin.template"->"force"[label="default:yes"];
"ansible.builtin.template"->"backup"[label="default:no"];
"ansible.builtin.template"->"validate"[label="目的路径检验"];
"ansible.builtin.template"->"newline_sequence"[label="指定模板换行符"];

}
```

-  mode: u=rw,g=r,o=r
-  mode: '0644'
-  backup：在覆盖之前将原文件备份，备份文件包含时间戳信息
-  validate：复制前是否检验需要复制目的地的路径


{{< alert >}}
file 模块可以帮助我们完成一些对文件的基本操作。比如，创建文件或目录、删除文件或目录、修改文件权限等
{{< /alert >}}

{{< alert color="secondary" >}}

ansible的输出的颜色提示说明:

黄色：对远程节点进行相应修改

绿色：对远程节点不进行相应修改，或者只是对远程节点信息进行查看

红色：操作命令有异常

紫色：表示对命令执行发出警告信息会给一下建议

{{< /alert >}}



## 选项介绍

```bash

[root@k8s-node-77 ansible]# ansible-doc -s file
[root@k8s-node-77 ansible]# ansible-doc -s template
- name: Template a file out to a remote server
  template:
      attributes:            # The attributes the resulting file or directory should have. To get supported flags look at the man page for `chattr' on the target system. This string should contain the attributes in the same order as the one displayed by `lsattr'. The
                               `=' operator is assumed as default, otherwise `+' or `-' operators need to be included in the string.
      backup:                # Create a backup file including the timestamp information so you can get the original file back if you somehow clobbered it incorrectly.
      block_end_string:      # The string marking the end of a block.
      block_start_string:    # The string marking the beginning of a block.
      dest:                  # (required) Location to render the template to on the remote machine.
      follow:                # Determine whether symbolic links should be followed. When set to `yes' symbolic links will be followed, if they exist. When set to `no' symbolic links will not be followed. Previous to Ansible 2.4, this was hardcoded as `yes'.
      force:                 # Determine when the file is being transferred if the destination already exists. When set to `yes', replace the remote file when contents are different than the source. When set to `no', the file will only be transferred if the destination
                               does not exist.
      group:                 # Name of the group that should own the file/directory, as would be fed to `chown'.
      lstrip_blocks:         # Determine when leading spaces and tabs should be stripped. When set to `yes' leading spaces and tabs are stripped from the start of a line to a block. This functionality requires Jinja 2.7 or newer.
      mode:                  # The permissions the resulting file or directory should have. For those used to `/usr/bin/chmod' remember that modes are actually octal numbers. You must either add a leading zero so that Ansible's YAML parser knows it is an octal number
                               (like `0644' or `01777') or quote it (like `'644'' or `'1777'') so Ansible receives a string and can do its own conversion from string into number. Giving Ansible a number without following one of these rules
                               will end up with a decimal number which will have unexpected results. As of Ansible 1.8, the mode may be specified as a symbolic mode (for example, `u+rwx' or `u=rw,g=r,o=r').
      newline_sequence:      # Specify the newline sequence to use for templating files.
      output_encoding:       # Overrides the encoding used to write the template file defined by `dest'. It defaults to `utf-8', but any encoding supported by python can be used. The source template file must always be encoded using `utf-8', for homogeneity.
      owner:                 # Name of the user that should own the file/directory, as would be fed to `chown'.
      selevel:               # The level part of the SELinux file context. This is the MLS/MCS attribute, sometimes known as the `range'. When set to `_default', it will use the `level' portion of the policy if available.
      serole:                # The role part of the SELinux file context. When set to `_default', it will use the `role' portion of the policy if available.
      setype:                # The type part of the SELinux file context. When set to `_default', it will use the `type' portion of the policy if available.
      seuser:                # The user part of the SELinux file context. By default it uses the `system' policy, where applicable. When set to `_default', it will use the `user' portion of the policy if available.
      src:                   # (required) Path of a Jinja2 formatted template on the Ansible controller. This can be a relative or an absolute path. The file must be encoded with `utf-8' but `output_encoding' can be used to control the encoding of the output template.
      trim_blocks:           # Determine when newlines should be removed from blocks. When set to `yes' the first newline after a block is removed (block, not variable tag!).
      unsafe_writes:         # Influence when to use atomic operation to prevent data corruption or inconsistent reads from the target file. By default this module uses atomic operations to prevent data corruption or inconsistent reads from the target files, but
                               sometimes systems are configured or just broken in ways that prevent this. One example is docker mounted files, which cannot be updated atomically from inside the container and can only be written in an
                               unsafe manner. This option allows Ansible to fall back to unsafe methods of updating files when atomic operations fail (however, it doesn't force Ansible to perform unsafe writes). IMPORTANT! Unsafe writes
                               are subject to race conditions and can lead to data corruption.
      validate:              # The validation command to run before copying into place. The path to the file to validate is passed in via '%s' which must be present as in the examples below. The command is passed securely so shell features like expansion and pipes will
                               not work.
      variable_end_string:   # The string marking the end of a print statement.
      variable_start_string:   # The string marking the beginning of a print statement.



```

{{< alert color="secondary" >}}

ansible-doc -s template

- name: Templates a file out to a remote server.

  action: template

      backup    # 拷贝的同时也创建一个包含时间戳信息的备份文件，默认为no

      dest=     # 目标路径

      force     # 设置为yes (默认)时，将覆盖远程同名文件。设置为no时，忽略同名文件的拷贝

      group     # 设置远程文件的所属组

      owner     # 设置远程文件的所有者

      mode      # 设置远程文件的权限。使用数值表示时不能省略第一位，如0644。

                # 也可以使用'u+rwx' or 'u=rw,g=r,o=r'等方式设置

      src=      # ansible控制器上Jinja2格式的模板所在位置，可以是相对或绝对路径

      validate  # 在复制到目标主机后但放到目标位置之前，执行此选项指定的命令。

                # 一般用于检查配置文件语法，语法正确则保存到目标位置。

                # 如果要引用目标文件名，则使用%s，下面的示例中的%s即表示目标机器上的/etc/nginx/nginx.conf。s

{{< /alert >}}



```yaml

- name: create directory
  file: path=/tmp/xyz state=directory owner=root group=root mode=0755 recurse=yes

```



#### 2. 示例

```yaml
- name: Copy my.cnf.j2 Template to /data/mysql/my.cnf
  become: yes
  ansible.builtin.template:
    src: my.cnf.j2
    dest: /data/mysql/my.cnf
    owner: mysql
    group: mysql
    mode: '0644'
    backup: yes
    validate: /usr/sbin/sshd -t -f %s
```







## 六、archive

#### 1. 选项
| 选项          | 默认值 | 作用                                |
| :------------ | :----- | :---------------------------------- |
| path          |        | 远程主机上需要被打包/压缩的源文件   |
| dest          |        | 打包/压缩后的包文件路径             |
| format        | gz     | 指定压缩类型：bz2、gz、tar、xz、zip |
| owner         |        | 属主                                |
| group         |        | 属组                                |
| mode          |        | 权限                                |
| remove        | no     | 在打包/压缩后，不删除源文件         |
| force_archive |        | 是否从ansible主机复制到远程主机     |
| exclude_path  |        | 基于http basic认证的用户名          |




#### 2. 示例

```yaml
- name: archive
  become: yes
  archive:
    path: /data/sb
    dest: /data/sb.tar.gz
    format: gz
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: "0644"

- name: Compress directory /path/to/foo/ into /path/to/foo.tgz
  archive:
    path: /path/to/foo
    dest: /path/to/foo.tgz

- name: Compress regular file /path/to/foo into /path/to/foo.gz and remove it
  archive:
    path: /path/to/foo
    remove: yes

- name: Create a zip archive of /path/to/foo
  archive:
    path: /path/to/foo
    format: zip

- name: Create a bz2 archive of multiple files, rooted at /path
  archive:
    path:
    - /path/to/foo
    - /path/wong/foo
    dest: /path/file.tar.bz2
    format: bz2

- name: Create a bz2 archive of a globbed path, while excluding specific dirnames
  archive:
    path:
    - /path/to/foo/*
    dest: /path/file.tar.bz2
    exclude_path:
    - /path/to/foo/bar
    - /path/to/foo/baz
    format: bz2

- name: Create a bz2 archive of a globbed path, while excluding a glob of dirnames
  archive:
    path:
    - /path/to/foo/*
    dest: /path/file.tar.bz2
    exclude_path:
    - /path/to/foo/ba*
    format: bz2

- name: Use gzip to compress a single archive (i.e don't archive it first with tar)
  archive:
    path: /path/to/foo/single.file
    dest: /path/file.gz
    format: gz

- name: Create a tar.gz archive of a single file.
  archive:
    path: /path/to/foo/single.file
    dest: /path/file.tar.gz
    format: gz
    force_archive: true
```



## 七、unarchive
{{< alert color="secondary" >}}
Ansible unarchive 模块用于解压各种类型的压缩文件，包括：
- gzip (.gz)
- bzip2 (.bz2)
- xz (.xz)
- tar (.tar)
- zip (.zip)
- RAR (.rar)

{{< /alert >}}



#### 1. 选项
| 选项       | 默认值 | 作用                                         |
| :--------- | :----- | :------------------------------------------- |
| src        |        | 下载资源的URL（支持http、https、ftp协议）    |
| dest       |        | 下载的资源在目标主机上的保存路径（绝对路径） |
| owner      |        | 属主                                         |
| group      |        | 属组                                         |
| mode       |        | 权限                                         |
| copy       | yes    | 是否从ansible主机复制到远程主机              |
| remote_src |        | 基于http basic认证的用户名                   |
| extra_opts |        | 下载后计算摘要                               |
| exclude    |        | URL请求的超时时间，单位为s                   |



```viz-dot
digraph "unarchive" {
        rankdir=LR;
node [
        shape = octagon;
        fontsize = "12";
];

    "unarchive" -> "src";
    "unarchive" -> "dest";
    "unarchive" -> "remote_src";
    "unarchive" -> "copy";
    "unarchive" -> "owner";
    "unarchive" -> "group";
    "unarchive" -> "mode";
    "unarchive" -> "extra_opts";
    "unarchive" -> "creates";
    "unarchive" -> "checksum";
    "unarchive" -> "include";
    "unarchive" -> "exclude";
}
```

#### 2. 示例


```yaml
---
# Extract
- name: "Extract {{ install_tmpdir }}/prometheus-{{ prometheus_version }}"
  become: yes
  unarchive:
    src: "{{ install_tmpdir }}/prometheus-{{ prometheus_version }}.linux-amd64.tar.gz"
    dest: "{{ prometheus_bin_dir }}/"
    remote_src: true
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: 0755
    extra_opts:
      - --strip-components=1
    exclude:
      - "*.md"
      - "Document*"
  when: ansible_architecture == 'x86_64'

- name: "Extract {{ install_tmpdir }}/prometheus-{{ prometheus_version }}"
  become: yes
  unarchive:
    src: "{{ install_tmpdir }}/prometheus-{{ prometheus_version }}.linux-arm64.tar.gz"
    dest: "{{ prometheus_bin_dir }}/"
    remote_src: true
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    extra_opts:
      - --strip-components=1
    exclude:
      - "*.md"
      - "Document*"
  when: ansible_architecture == 'aarch64'


- name: "解压压缩文件: {{ install_tmpdir }}/node_exporter-{{ node_exporter_version }}"
  become: yes
  unarchive:
    src: "{{ install_tmpdir }}/node_exporter-{{ node_exporter_version }}.linux-arm64.tar.gz"
    dest: "{{ node_exporter_base_dir }}/"
    remote_src: true
    copy: no
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: 0755
  when: ansible_architecture == "aarch64"

- name: "Extract foo.tgz into /var/lib/foo"
  ansible.builtin.unarchive:
    src: foo.tgz
    dest: /var/lib/foo

- name: "Unarchive a file that is already on the remote machine"
  ansible.builtin.unarchive:
    src: /tmp/foo.zip
    dest: /usr/local/bin
    remote_src: yes

- name: "Unarchive a file that needs to be downloaded (added in 2.0)"
  ansible.builtin.unarchive:
    src: https://example.com/example.zip
    dest: /usr/local/bin
    remote_src: yes

- name: "Unarchive a file with extra options"
  ansible.builtin.unarchive:
    src: /tmp/foo.zip
    dest: /usr/local/bin
    extra_opts:
    - --transform
    - s/^xxx/yyy/
```

- src: 压缩文件的路径，可以是本地路径或远程路径。
- dest: 远程主机上的目标绝对路径
- remote_src: 如果为 True，则将压缩文件从本地机器复制到目标机器后再解压。默认值为 False。
- copy: 默认为yes，拷贝的文件从ansible主机复制到远程主机，no在远程主机上寻找src源文件解压
- checksum: 压缩文件的校验和，用于验证文件完整性。
- owner: 解压后文件的拥有者。
- group: 解压后文件的所属组。
- mode: 解压后文件的权限。
- include:
- exclude: 列出需要排除的目录和文件
- creates ：一个文件名，当它已经存在时，这个步骤将不会被运行
- extra_opts:--strip-components=1 去掉外层目录



## 八、lineinfile

#### 1. 选项
| 选项         | 默认值  | 作用                                         |
| :----------- | :------ | :------------------------------------------- |
| path         |         | 指定要操作的文件                             |
| regexp       |         | 下载资源的URL（支持http、https、ftp协议）    |
| line         |         | 要插入/替换到文件中的行                      |
| insertbefore |         | BOF                                          |
| insertafter  | EOF     | EOF                                          |
| firstmatch   |         | 下载的资源在目标主机上的保存路径（绝对路径） |
| owner        |         | 属主                                         |
| group        |         | 属组                                         |
| mode         |         | 权限                                         |
| backup       | false   | 是否从ansible主机复制到远程主机              |
| create       | false   | true则文件不存在时创建文件，false则          |
| state        | present | absent、present                              |
| backrefs     |         | URL请求的超时时间，单位为s                   |

```bash
ansible-doc -s lineinfile
```


#### backup
```yaml
- name: remove LANG from /root/profile
  become: yes
  lineinfile:
    path: /root/profile
    regexp: 'LANG'
    state: absent
    backup: yes

- name: Add LANG to /root/profile
  become: yes
  lineinfile:
    path: /root/profile
    line: 'export LANG="en_US.UTF-8"'
    insertafter: EOF
    state: present
```
输出：
```bash
2024-05-13T19:58:09 [root@node76 /root] $ ll
total 16
-rw-r--r--. 1 root root    0 Apr 22 14:33 my.sql
-rw-r--r--. 1 root root 1888 May 13 19:59 profile
-rw-r--r--. 1 root root 1888 May 13 19:57 profile.14529.2024-05-13@19:59:35~
-rw-r--r--. 1 root root 1862 May 13 19:59 profile.14625.2024-05-13@19:59:36~
-rw-r--r--. 1 root root 1058 Sep 19  2022 README.txt
2024-05-13T19:59:45 [root@node76 /root] $

```


#### 示例

```yaml
# NOTE: Before 2.3, option 'dest', 'destfile' or 'name' was used instead of 'path'
- name: Ensure SELinux is set to enforcing mode
  ansible.builtin.lineinfile:
    path: /etc/selinux/config
    regexp: '^SELINUX='
    line: SELINUX=enforcing

- name: Make sure group wheel is not in the sudoers configuration
  ansible.builtin.lineinfile:
    path: /etc/sudoers
    state: absent
    regexp: '^%wheel'

- name: Replace a localhost entry with our own
  ansible.builtin.lineinfile:
    path: /etc/hosts
    regexp: '^127\.0\.0\.1'
    line: 127.0.0.1 localhost
    owner: root
    group: root
    mode: '0644'

- name: Replace a localhost entry searching for a literal string to avoid escaping
  ansible.builtin.lineinfile:
    path: /etc/hosts
    search_string: '127.0.0.1'
    line: 127.0.0.1 localhost
    owner: root
    group: root
    mode: '0644'

- name: Ensure the default Apache port is 8080
  ansible.builtin.lineinfile:
    path: /etc/httpd/conf/httpd.conf
    regexp: '^Listen '
    insertafter: '^#Listen '
    line: Listen 8080

- name: Ensure php extension matches new pattern
  ansible.builtin.lineinfile:
    path: /etc/httpd/conf/httpd.conf
    search_string: '<FilesMatch ".php[45]?$">'
    insertafter: '^\t<Location \/>\n'
    line: '        <FilesMatch ".php[34]?$">'

- name: Ensure we have our own comment added to /etc/services
  ansible.builtin.lineinfile:
    path: /etc/services
    regexp: '^# port for http'
    insertbefore: '^www.*80/tcp'
    line: '# port for http by default'

- name: Add a line to a file if the file does not exist, without passing regexp
  ansible.builtin.lineinfile:
    path: /tmp/testfile
    line: 192.168.1.99 foo.lab.net foo
    create: yes

# NOTE: Yaml requires escaping backslashes in double quotes but not in single quotes
- name: Ensure the JBoss memory settings are exactly as needed
  ansible.builtin.lineinfile:
    path: /opt/jboss-as/bin/standalone.conf
    regexp: '^(.*)Xms(\d+)m(.*)$'
    line: '\1Xms${xms}m\3'
    backrefs: yes

# NOTE: Fully quoted because of the ': ' on the line. See the Gotchas in the YAML docs.
- name: Validate the sudoers file before saving
  ansible.builtin.lineinfile:
    path: /etc/sudoers
    state: present
    regexp: '^%ADMIN ALL='
    line: '%ADMIN ALL=(ALL) NOPASSWD: ALL'
    validate: /usr/sbin/visudo -cf %s

# See https://docs.python.org/3/library/re.html for further details on syntax
- name: Use backrefs with alternative group syntax to avoid conflicts with variable values
  ansible.builtin.lineinfile:
    path: /tmp/config
    regexp: ^(host=).*
    line: \g<1>{{ hostname }}
    backrefs: yes
```


## 九、blockinfile


## 十、replace


## 十一、find



## 十二、raw


```yaml
- name: Bootstrap a host without python2 installed
  ansible.builtin.raw: dnf install -y python2 python2-dnf libselinux-python

- name: Run a command that uses non-posix shell-isms (in this example /bin/sh doesn't handle redirection and wildcards together but bash does)
  ansible.builtin.raw: cat < /tmp/*txt
  args:
    executable: /bin/bash

- name: Safely use templated variables. Always use quote filter to avoid injection issues.
  ansible.builtin.raw: "{{ package_mgr|quote }} {{ pkg_flags|quote }} install {{ python|quote }}"

- name: List user accounts on a Windows system
  ansible.builtin.raw: Get-WmiObject -Class Win32_UserAccount
```



## 十三、synchronize


```yaml
- name: Check rsync command exists
  command: "which rsync"
  ignore_errors: yes
  register: rsync

- debug: msg="复制集群依赖中.....过程较长请耐心等待, 若中断,  请重新执行安装"

- name: copy repo server package
  copy:
    src: "{{ role_path }}/../../files/{{ web_repo_package_basename }}"
    dest: "{{ web_repo_host_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes
  when: rsync.stdout == ''

- name: synchronize repo server package
  synchronize:
    src: "{{ role_path }}/../../files/{{ web_repo_package_basename }}"
    dest: "{{ web_repo_host_path }}"
  become: yes
  when: rsync.stdout != ''

```







