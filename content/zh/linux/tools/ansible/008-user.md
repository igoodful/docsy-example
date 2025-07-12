---
title: 08. 用户管理
description: 用户管理
date: 2023-10-12
weight: 800


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}

{{< alert color="primary" >}}

ansible的输出的颜色提示说明:

黄色：对远程节点进行相应修改

绿色：对远程节点不进行相应修改，或者只是对远程节点信息进行查看

红色：操作命令有异常

紫色：表示对命令执行发出警告信息会给一下建议

{{< /alert >}}


```viz-dot
digraph "user" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"用户管理"->"user";
"用户管理"->"group";
"用户管理"->"fetch";
"用户管理"->"lineinfile";
"用户管理"->"blockinfile";
}
```





## 一、user

| 参数               | 作用                                             | 默认值  |
|:-------------------|:-------------------------------------------------|:--------|
| name               | 用户名                                           |         |
| passwd             | 用户密码，但不能明文密码                          |         |
| uid                | 用户的UID                                        |         |
| non_unique         | 允许改变UID为非唯一值                            |         |
| group              | 用户主组                                         |         |
| groups             | 用户属组                                         |         |
| state              | present/absent                                   | present |
| remove             |                                                  | false   |
| force              |                                                  | false   |
| home               | 用户home目录                                     |         |
| create_home        | 在用户创建时或home目录不存在时为用户创建home目录 | true    |
| move_home          | 临时迁移用户家目录到特定目录                     |         |
| shell              | 用户默认shell                                    |         |
| system             | 是否系统用户                                     |         |
| expires            | 过期时间                                         |         |
| password_lock      | 是否锁定                                         |         |
| update_password    | on_create/always                                 | always  |
| generate_ssh_key   | 是否生成ssh密钥对                                | false   |
| ssh_key_bits       | ssh key加密位数                                  |         |
| ssh_key_file       | ssh私钥的路径及名称                              |         |
| ssh_key_passphrase |                                                  |         |
| ssh_key_type       | 密钥对的类型                                     | rsa     |
| ssh_key_comment    |                                                  |         |
| comment            |                                                  |         |



获取加密的密码

`ansible all -i localhost, -m debug -a "msg={{ 'igoodful' | password_hash('sha512', 'apple') }}"`
```json
localhost | SUCCESS => {
    "msg": "$6$apple$YLfeDKEQ77RTtooOrmgx.oHQCMZSDF.TIWHaqtuD.OHwK.8C3L7pzwDwFiAx0A/avg8Sj5qd8gXqhdr2wa3ag/"
}

```





- 创建用户，生成ssh密钥对
```yaml
 - name: "Add the user: igoodful"
   become: yes
   ansible.builtin.user:
     name: igoodful
     password: "$6$apple$YLfeDKEQ77RTtooOrmgx.oHQCMZSDF.TIWHaqtuD.OHwK.8C3L7pzwDwFiAx0A/avg8Sj5qd8gXqhdr2wa3ag/"
     comment: igoodful@qq.com
     generate_ssh_key: yes
     ssh_key_type: rsa
     ssh_key_file: .ssh/id_rsa
     ssh_key_bits: 2048
     ssh_key_comment: igoodful@qq.com
```


```yaml
 - name: "Add the user: igoodful"
   become: yes
   ansible.builtin.user:
     name: igoodful
     password: "$6$apple$YLfeDKEQ77RTtooOrmgx.oHQCMZSDF.TIWHaqtuD.OHwK.8C3L7pzwDwFiAx0A/avg8Sj5qd8gXqhdr2wa3ag/"
     comment: igoodful@qq.com
     uid: 10086
     group: igoodful
     home: /home/igoodful
```


- system user
```yaml
 - name: "Add the user: igoodful"
   become: yes
   ansible.builtin.user:
     name: igoodful
     password: "$6$apple$YLfeDKEQ77RTtooOrmgx.oHQCMZSDF.TIWHaqtuD.OHwK.8C3L7pzwDwFiAx0A/avg8Sj5qd8gXqhdr2wa3ag/"
     comment: igoodful@qq.com
     system: yes
     shell: /sbin/nologin
```


```yaml
 - name: "Add the user: igoodful"
   become: yes
   ansible.builtin.user:
     name: igoodful
     password: "$6$apple$YLfeDKEQ77RTtooOrmgx.oHQCMZSDF.TIWHaqtuD.OHwK.8C3L7pzwDwFiAx0A/avg8Sj5qd8gXqhdr2wa3ag/"
     comment: igoodful@qq.com
     system: no
     shell: /bin/shell
     home: /home/igoodful
     move_home: yes
```



- 删除用户
```yaml
 - name: "remove the user: igoodful"
   become: yes
   ansible.builtin.user:
     name: igoodful
     state: absent
     remove: yes

```




## 二、group

#### 1. 参数
```viz-dot
digraph "group" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"group"->"name";
"group"->"gid";
"group"->"state";
"group"->"system";
"group"->"local" [label="lgroupadd"];
"group"->"non_unique" [label="Not supported on macOS or BusyBox distributions"];

}
```


#### 2. 新增组
```yaml
# add group
 - name: Ensure group "igoodful"
   become: yes
   ansible.builtin.group:
     name: igoodful
     state: present

# add group with system
 - name: Ensure group "igoodful"
   become: yes
   ansible.builtin.group:
     name: igoodful
     state: present
     system: true

# add group with gid
- name: Ensure group "docker" exists with correct gid
  become: yes
  ansible.builtin.group:
    name: docker
    gid: 2000
    state: present


# add group with system and gid
- name: Ensure group "docker" exists with correct gid
  become: yes
  ansible.builtin.group:
    name: docker
    gid: 500
    state: present
    system: true


```



#### 3. 删除组
```yaml
# remove group
 - name: remove group "igoodful"
   become: yes
   ansible.builtin.group:
     name: igoodful
     state: absent
```











