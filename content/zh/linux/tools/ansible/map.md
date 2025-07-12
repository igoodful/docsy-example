---
title: map
description: map
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



### 嵌套循环: `with_nested`


```yaml
#创建目录并且修改权限
- hosts: all
  become: yes
  tasks:
    - name: 创建文件
      file: path=/opt/{{ item[0] }} owner={{ item[1] }}  state=directory
      with_nested:
        - [ 'test-1', 'test-2' ]
        - [ 'name1', 'name2', ]
#最后文件的所属用户为name2，在test-1与test-2目录上面都会循环2次修改权限的操作
```







在Ansible中，`map`过滤器结合`extract`过滤器的用法通常用于从复杂数据结构中提取嵌套字段。你提供的示例：

```jinja2
{{ groups['all'] | map('extract', hostvars, ['ansible_host']) }}
```

其核心逻辑是：**从所有主机的变量中提取`ansible_host`字段的值**。下面详细解释其含义和用法：

---

### **分步解析**
#### 1. **`groups['all']`**
   - `groups`是Ansible内置变量，表示清单（inventory）中定义的主机组。
   - `groups['all']`返回一个列表，包含清单中所有主机的名称（主机名）。

#### 2. **`map('extract', hostvars, ['ansible_host'])`**
   - `map`过滤器遍历`groups['all']`中的每个主机名。
   - 对每个主机名应用`extract`过滤器，并传递两个参数：`hostvars`和`['ansible_host']`。

   - **`extract`过滤器的作用**：
     - 语法：`extract(字典, 字段路径)`。
     - 从`hostvars`字典中提取某个主机的变量，并进一步提取其`ansible_host`字段的值。
     - 等价于：`hostvars[主机名]['ansible_host']`。

#### 3. **最终结果**
   - 返回一个生成器（需用`list`转换后查看），包含所有主机的`ansible_host`值。

---

### **完整流程**
```jinja2
{{ groups['all'] | map('extract', hostvars, ['ansible_host']) | list }}
```
1. **输入**：`groups['all']` = `['host1', 'host2', 'host3']`（假设清单中有3台主机）。
2. **操作**：
   - 对每个主机名`hostX`，执行 `extract(hostvars, ['ansible_host'])`：
     - `hostvars['host1']['ansible_host']` → 假设值为`10.1.1.1`
     - `hostvars['host2']['ansible_host']` → 假设值为`10.1.1.2`
     - `hostvars['host3']['ansible_host']` → 假设值为`10.1.1.3`
3. **输出**：`['10.1.1.1', '10.1.1.2', '10.1.1.3']`。

---

### **典型应用场景**
#### 1. **动态获取所有主机的IP地址**
```yaml
- name: 打印所有主机的 ansible_host
  debug:
    msg: "{{ groups['all'] | map('extract', hostvars, ['ansible_host']) | list }}"
```

#### 2. **生成主机列表配置文件**
```yaml
- name: 生成服务器列表
  copy:
    content: |
      {% for ip in groups['all'] | map('extract', hostvars, ['ansible_host']) %}
      {{ ip }}
      {% endfor %}
    dest: /tmp/host_ips.txt
```

#### 3. **与其他过滤器结合**
```yaml
# 获取所有主机的 ansible_host，并去重
{{ groups['all'] | map('extract', hostvars, ['ansible_host']) | unique | list }}
```

---

### **注意事项**
1. **依赖`hostvars`结构**：
   - 假设每个主机的`hostvars`中定义了`ansible_host`字段。若字段不存在，会返回`Undefined`或报错。
   - 可通过默认值处理缺失字段：
     ```jinja2
     {{ groups['all'] | map('extract', hostvars, ['ansible_host']) | default('N/A') | list }}
     ```

2. **返回生成器**：
   - `map`返回的是生成器，若需直接查看结果或在某些场景中使用，需通过`list`转换为列表。

3. **性能优化**：
   - 在大型清单中使用时，确保`hostvars`的访问效率（Ansible已优化此操作）。

---

### **等价替代写法**
以下两种写法与原始示例等价：
```jinja2
# 显式使用 attribute 提取
{{ groups['all'] | map('extract', hostvars) | map(attribute='ansible_host') | list }}

# 直接使用 Jinja2 语法（无需 map）
{% set ips = [] %}
{% for host in groups['all'] %}
  {% set _ = ips.append(hostvars[host].ansible_host) %}
{% endfor %}
{{ ips }}
```

---

### **总结**
- **核心目的**：从所有主机的变量中提取`ansible_host`字段。
- **链式操作**：`map` + `extract` 是Ansible中处理嵌套数据结构的常用模式。
- **适用场景**：需要批量操作主机变量时（如生成配置、动态获取IP等）。





Ansible中的`map`过滤器源自Jinja2，用于对列表中的元素进行转换或提取属性。以下是其用法详解及示例：

---

### **基本用法**
`map`过滤器对列表中的每个元素应用操作（如调用方法、提取属性、应用过滤器），返回处理后的新列表。

#### **语法**
```jinja2
{{ 列表变量 | map(操作或属性) | list }}
```
- `操作或属性`：可以是过滤器的名称（字符串形式）、属性名（用`attribute='字段名'`指定），或自定义函数。
- 使用`list`过滤器将结果转换为列表（因`map`返回生成器）。

---

### **常见场景及示例**

#### **1. 提取字典列表中的某个字段**
```yaml
users:
  - { name: 'Alice', age: 30 }
  - { name: 'Bob', age: 25 }

# 提取所有用户的名称
{{ users | map(attribute='name') | list }}
# 结果: ['Alice', 'Bob']
```

#### **2. 对元素应用内置过滤器**
```yaml
names: ['alice', 'bob']

# 转换为大写
{{ names | map('upper') | list }}
# 结果: ['ALICE', 'BOB']

# 转换为整数
{{ ['1', '2', '3'] | map('int') | list }}
# 结果: [1, 2, 3]
```

#### **3. 结合`selectattr`筛选后处理**
```yaml
# 筛选年龄大于28的用户，并提取名称
{{ users | selectattr('age', '>', 28) | map(attribute='name') | list }}
# 结果: ['Alice']
```

#### **4. 处理可能缺失的字段**
```yaml
# 若字段可能缺失，结合默认值
{{ users | map(attribute='name') | map('default', 'Anonymous') | list }}
# 或先筛选有字段的元素
{{ users | selectattr('name', 'defined') | map(attribute='name') | list }}
```

#### **5. 自定义过滤器**
假设存在自定义过滤器`double`（将数值翻倍）：
```yaml
numbers: [1, 2, 3]
{{ numbers | map('double') | list }}
# 结果: [2, 4, 6]
```

#### **6. 传递参数**
```yaml
# 对每个元素调用带参数的函数，如字符串格式化
{{ ['foo', 'bar'] | map('format', 'prefix-%s') | list }}
# 结果: ['prefix-foo', 'prefix-bar']
```

---

### **注意事项**
- **生成器转列表**：`map`返回生成器，通常需用`list`转换为列表，尤其在模板输出或循环中使用时。
- **错误处理**：提取不存在的属性会报错，建议先用`selectattr`筛选或设置默认值。
- **复杂操作**：可链式组合多个过滤器（如`selectattr`、`map`、`join`）实现复杂逻辑。

---

### **示例任务**
```yaml
- name: 创建用户
  ansible.builtin.user:
    name: "{{ item }}"
    state: present
  loop: "{{ users | map(attribute='name') | list }}"
```

此任务会从`users`变量中提取所有`name`值，并依次创建用户。

通过灵活使用`map`，可简化数据转换，提升Playbook的可读性和效率。








