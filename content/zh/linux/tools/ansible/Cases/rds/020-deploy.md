---
title: 20. deploy.yml
description: deploy.yml
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. os
2. docker
3. images
4. repo
5. pypiserver
6. registry
7. mysql
8. prometheus
9. rabbitmq
10. nginx
11. redis
12. keepalived
13. upload
14. api
15. conductor
16. monitor
17. auth
18. exporter
19. check


{{< /alert >}}


## 一、deploy.yml

```yaml
---
- import_playbook: facts.yml

- name: Apply role os
  hosts: all
  gather_facts: false
  roles:
    - {
        role: os,
        tags: os,
      }

- name: Apply role docker
  hosts: all
  gather_facts: false
  roles:
    - {
        role: docker,
        tags: docker,
      }


- name: Apply role load images
  hosts: all
  gather_facts: false
  roles:
    - {
        role: images,
        tags: images,
      }

- name: Apply role repo
  hosts: reposerver
  gather_facts: false
  roles:
    - {
        role: repo,
        tags: repo,
      }

- name: Apply role pypiserver
  hosts: pypiserver
  gather_facts: false
  roles:
    - {
        role: pypiserver,
        tags: pypiserver,
      }

- name: Apply role registry
  hosts: registry
  gather_facts: false
  roles:
    - {
        role: registry,
        tags: registry,
      }

- name: Apply role mysql
  hosts: database
  gather_facts: false
  roles:
    - {
        role: mysql,
        tags: mysql,
      }

- name: Apply role prometheus
  hosts: monitor
  gather_facts: false
  roles:
    - {
        role: prometheus,
        tags: prometheus,
      }

- name: Apply role rabbitmq
  hosts: rabbitmq
  gather_facts: false
  roles:
    - {
        role: rabbitmq,
        tags: rabbitmq,
      }

- name: Apply role nginx
  hosts: nginx
  gather_facts: false
  roles:
    - {
        role: nginx,
        tags: nginx,
      }

#- name: Apply role redis
#  hosts: redis
#  gather_facts: false
#  roles:
#    - {
#        role: redis,
#        tags: redis,
#      }

# - name: Apply keepalived
#   hosts: keepalived
#   gather_facts: false
#   roles:
#     - {
#         role: keepalived,
#         tags: keepalived,
#         when: deploy_mode != "all-in-one"
#       }

- name: Upload wheel package
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

- name: Apply role exporter
  hosts: exporter
  gather_facts: false
  roles:
    - {
        role: exporter,
        tags: exporter,
      }

- name: Checking the Installation Result And Cleaning the battlefield
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


