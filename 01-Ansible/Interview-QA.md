**1. How to install ansible role and in which location it got stored***

```bash
## Install ansible role
ansible-galaxy role install bsmeding.docker

## Role path
root@LAPTOP-QMBUJPPJ:~# ansible-config dump | grep ROLES_PATH
DEFAULT_ROLES_PATH(default) = ['/root/.ansible/roles', '/usr/share/ansible/roles', '/etc/ansible/roles']
```

---
**2. How to Call the role**

```yaml
- hosts: localhost
  become: true
  roles:
    - bsmeding.docker
```
