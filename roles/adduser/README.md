adduser
=========

A role that create users and groups for kubespray 

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-name--of--my--role-blue.svg)](https://galaxy.ansible.com/ui/repo/published/kubernetes_sigs/kubespray/content/role/adduser/)


Requirements
------------

None

Role Variables
--------------

| Variable | Required | Default | Choices | Comments |
|---|---|---|---|---|
| `user` | yes | `"{% raw %}{{ addusers.kube }}{% endraw %}"` (typically passed from dependency) | dictionary | Input user object consumed by the role. |
| `addusers.etcd` | no | see default object below | dictionary | Predefined etcd system user profile. |
| `addusers.kube` | no | see default object below | dictionary | Predefined Kubernetes system user profile. |
| `kube_cert_group` | no | `kube-cert` | string | Used by `addusers.kube.group`. |
| `etcd_data_dir` | no | `/var/lib/etcd` | path string | Used as etcd user home dir. |

Default user objects:

```yaml
addusers:
  etcd:
    name: etcd
    comment: "Etcd user"
    create_home: false
    system: true
    shell: /sbin/nologin

  kube:
    name: kube
    comment: "Kubernetes user"
    create_home: false
    system: true
    shell: /sbin/nologin
    group: "{{ kube_cert_group }}"
```



Dependencies
------------

None

Example Playbook
----------------
- name: Create kubernetes service user
  hosts: all
  become: true
  gather_facts: true
  roles:
    - role: adduser
      user: "{{ addusers.kube }}"


License
-------

See license.md
