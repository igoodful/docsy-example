---
title: 13. upload
description: upload
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}


{{< /alert >}}




## 一、defaults/main.yaml

```yaml
---
docker_images_package_basename: "docker-images-{{ arch_modifier }}"
docker_images_package_suffix: ".tar.gz"
docker_images_package: "{{ docker_images_package_basename }}{{ docker_images_package_suffix }}"
docker_images_tmp_path: "{{ tmp_dir }}/{{ docker_images_package_basename }}"

pypiserver_host_path: "{{ base_dir }}/pypiserver"
package_path_name: "release"

```


## 二、upload.yml

```yaml
---

# find /data/pypiserver/packages/ -name "greatrds*.whl"
- name: find existing wheels on pypiserver
  find:
    paths:
      - "{{ pypiserver_host_path }}/packages/"
    patterns: greatrds*.whl
  delegate_to: "{{ groups['pypiserver'][0] }}"
  register: old_wheels
  become: true

# rm -rf old_wheels.files
- name: remove old wheels
  file:
    path: "{{ item.path }}"
    state: absent
  loop: "{{ old_wheels.files }}"
  delegate_to: "{{ groups['pypiserver'][0] }}"
  become: true

# find files/release/wheels/
- name: find new wheel
  find:
    paths:
      - "{{ role_path }}/../../files/{{ package_path_name }}/wheels/"
    patterns: "*.whl"
  delegate_to: localhost
  register: new_wheel
  become: true

- name: Get debug info
  debug: var=new_wheel verbosity=0

# cp
- name: upload new wheel
  copy:
    src: "{{ item.path }}"
    dest: "{{ pypiserver_host_path }}/packages/"
  delegate_to: "{{ groups['pypiserver'][0] }}"
  loop: "{{ new_wheel.files }}"
  become: true

# docker restart pypiserver
- name: restart pypyserver
  command: docker restart pypiserver
  become: yes

```

























