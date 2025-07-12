---
title: hostvars
description: hostvars
date: 2025-04-22
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}





在Ansible中，`hostvars` 是一个**内置的特殊变量**，它包含了当前Playbook执行时所有主机的变量和收集的Facts信息。其数据结构是一个**字典**，键为**主机名**（或主机别名），值为该主机的**所有变量**（包括自定义变量、系统Facts、清单中定义的变量等）。

---

### **核心数据结构**
```python
hostvars = {
    "主机名1": {
        "ansible_facts": { ... },  # 系统收集的Facts
        "自定义变量1": "值",
        "自定义变量2": "值",
        # ... 其他变量
    },
    "主机名2": { ... },
    # ...
}
```

---

### **关键特性**
#### 1. **键（Key）**
   - 键是主机的名称（在Ansible清单中定义的主机名或别名）。
   - 示例：`hostvars['web-server-1']`。

#### 2. **值（Value）**
   - 值是一个字典，包含以下内容：
     - **`ansible_facts`**：通过`gather_facts: yes`收集的系统信息（如IP地址、操作系统等）。
     - **自定义变量**：在清单文件（`inventory`）、`group_vars`、`host_vars`或Playbook中定义的变量。
     - **其他内置变量**：如`ansible_host`、`ansible_port`等。

#### 3. **作用域**
   - `hostvars` **仅在Playbook执行过程中可用**，且在任务执行时动态填充。
   - 若未启用`gather_facts`，则`ansible_facts`字段可能为空。

---

### **常见内容示例**
假设清单中有一个主机 `web1`，其 `hostvars['web1']` 可能包含如下数据：
```yaml
{
    "ansible_facts": {
        "ansible_host": "192.168.1.10",
        "ansible_distribution": "Ubuntu",
        "ansible_eth0": {
            "ipv4": {"address": "192.168.1.10"},
            "macaddress": "00:11:22:33:44:55"
        },
        # ... 其他Facts
    },
    "http_port": 8080,           # 自定义变量（定义在 host_vars/web1 中）
    "app_version": "2.0",        # 自定义变量
    "ansible_connection": "ssh", # 内置变量
}
```

---

### **访问方式**
#### 1. **直接访问某个主机的变量**
```jinja2
# 获取主机 web1 的 ansible_host
{{ hostvars['web1']['ansible_host'] }}

# 获取主机 web1 的自定义变量
{{ hostvars['web1']['http_port'] }}
```

#### 2. **访问嵌套字段**
```jinja2
# 获取主机 web1 的 eth0 接口的 IPv4 地址
{{ hostvars['web1']['ansible_facts']['ansible_eth0']['ipv4']['address'] }}
```

#### 3. **动态遍历所有主机**
```jinja2
# 获取所有主机的 ansible_host
{{ groups['all'] | map('extract', hostvars, ['ansible_host']) | list }}
```

---

### **重要注意事项**
#### 1. **Facts收集依赖**
   - `ansible_facts` 字段仅在 `gather_facts: yes`（默认启用）时存在。
   - 若禁用Facts收集，需通过其他方式获取系统信息（如手动定义变量）。

#### 2. **主机的访问顺序**
   - 如果某个主机尚未被Ansible访问过（例如未在Play中执行任务），其 `hostvars` 可能不包含该主机的Facts。
   - 解决方法：在Play中提前对所有主机执行一次`setup`模块：
     ```yaml
     - name: 强制收集所有主机的Facts
       setup:
       delegate_to: "{{ item }}"
       loop: "{{ groups['all'] }}"
     ```

#### 3. **变量覆盖规则**
   - 变量优先级：`host_vars > group_vars > inventory变量`。
   - 冲突时，优先级高的变量会覆盖低优先级的变量。

#### 4. **调试方法**
   - 使用 `debug` 模块查看完整结构：
     ```yaml
     - name: 打印 hostvars 内容
       debug:
         var: hostvars
     ```
   - 查看特定主机的变量：
     ```yaml
     - name: 打印 web1 的变量
       debug:
         var: hostvars['web1']
     ```

---

### **典型应用场景**
#### 1. **跨主机信息引用**
```yaml
- name: 配置负载均衡器
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  vars:
    backend_servers: "{{ groups['web'] | map('extract', hostvars, ['ansible_host']) | list }}"
```
模板 `nginx.conf.j2` 中可使用 `{{ backend_servers }}` 动态生成后端服务器列表。

#### 2. **动态生成配置文件**
```jinja2
# 为所有数据库主机生成连接字符串
{% for host in groups['db'] %}
jdbc:mysql://{{ hostvars[host]['ansible_host'] }}:3306
{% endfor %}
```

#### 3. **条件判断**
```yaml
- name: 仅在Debian系统上执行任务
  apt:
    name: nginx
    state: latest
  when: "'Debian' in hostvars[inventory_hostname]['ansible_distribution']"
```

---

### **常见问题**
#### **Q1: `hostvars` 中找不到某个主机的变量**
   - 可能原因：
     - 主机未在清单中定义。
     - 未启用 `gather_facts`，且未手动定义变量。
     - 主机尚未被Ansible访问过（需提前收集Facts）。

#### **Q2: 如何访问主机的IP地址？**
   - 推荐使用 `ansible_host`（直接访问）或通过Facts接口：
     ```jinja2
     # 直接访问（需在清单中定义 ansible_host）
     {{ hostvars['web1']['ansible_host'] }}

     # 通过Facts获取主IP
     {{ hostvars['web1']['ansible_facts']['ansible_default_ipv4']['address'] }}
     ```

#### **Q3: 如何处理嵌套字典的KeyError？**
   - 使用 `default` 过滤器或条件判断：
     ```jinja2
     # 安全访问嵌套字段
     {{ hostvars['web1']['ansible_facts']['ansible_eth0']['ipv4']['address'] | default('N/A') }}

     # 条件判断
     {% if hostvars['web1']['ansible_facts']['ansible_eth0'] is defined %}
     {{ hostvars['web1']['ansible_facts']['ansible_eth0']['ipv4']['address'] }}
     {% endif %}
     ```

---

### **总结**
- **`hostvars` 是Ansible中跨主机操作的核心变量**，存储了所有主机的变量和Facts。
- 通过合理利用 `hostvars`，可实现动态配置、跨主机依赖管理等高级功能。
- 注意Facts收集和变量作用域的限制，结合调试工具（如`debug`模块）可快速定位问题。




