# Roles

**How to Create a Role in ansible**

```bash
ansible-galaxy role init apache
```

- This creates the below structure automatically

```
apache/
├── defaults
├── files
├── handlers
├── meta
├── tasks
├── templates
├── tests
└── vars
```
---

**Structure of Ansible role**




**How to install ansible role and in which location it got stored**

```bash
## Install ansible role
ansible-galaxy role install bsmeding.docker

## Role path
root@LAPTOP-QMBUJPPJ:~# ansible-config dump | grep ROLES_PATH
DEFAULT_ROLES_PATH(default) = ['/root/.ansible/roles', '/usr/share/ansible/roles', '/etc/ansible/roles']
```

---
**How to Call the role**

```yaml
- hosts: localhost
  become: true
  roles:
    - bsmeding.docker
```
