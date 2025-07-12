---
title: 21. upgrade.yml
description: upgrade.yml
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. upload
2. api
3. conductor
4. monitor
5. auth
6. nginx
7. exporter
8. check


{{< /alert >}}


## 一、upgrade.yml

```yaml
---
- import_playbook: facts.yml

- name: Upload new_wheel package
  hosts: pypiserver
  gather_facts: false
  roles:
    - {
        role: upload,
        tags: upload,
      }

- name: Apply role api
  hosts: api
  gather_facts: false
  roles:
    - {
        role: api,
        tags: api,
      }

- name: Apply role conductor
  hosts: conductor
  gather_facts: false
  roles:
    - {
        role: conductor,
        tags: conductor,
      }

- name: Apply role monitor
  hosts: monitor
  gather_facts: false
  roles:
    - {
        role: monitor,
        tags: monitor,
      }

- name: Apply role auth
  hosts: auth
  gather_facts: false
  roles:
    - {
        role: auth,
        tags: auth,
      }

- name: Apply role nginx
  hosts: nginx
  gather_facts: false
  roles:
    - {
        role: nginx,
        tags: nginx,
      }

- name: Apply role exporter
  hosts: exporter
  gather_facts: false
  roles:
    - {
        role: exporter,
        tags: exporter,
      }

- name: Checking the Update Result And Cleaning the battlefield
  hosts: all
  gather_facts: false
  vars:
    pre_check: "no"
  roles:
    - {
        role: check,
        tags: check,
      }

```


