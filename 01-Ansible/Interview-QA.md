# Roles

**What is Ansible Roles**

**Ansible Role** is a reusable and organized way to group related automation tasks, variables, templates, handlers, and files into a single unit.

It helps avoid duplication by allowing the same configuration (e.g., Apache installation, user creation, database setup) to be reused across multiple playbooks and environments.

---

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

A standard Ansible role has a predefined directory structure. Not every folder is mandatory, but this is the typical layout:

```text
roles/
└── apache/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    ├── files/
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    ├── meta/
    │   └── main.yml
    ├── tests/
    │   ├── inventory
    │   └── test.yml
    └── README.md
```

### What each directory does

| Directory    | Purpose                                                   |
| ------------ | --------------------------------------------------------- |
| `tasks/`     | Main tasks executed by the role. Required.                |
| `handlers/`  | Tasks triggered by notifications (e.g., restart service). |
| `templates/` | Jinja2 template files (`.j2`).                            |
| `files/`     | Static files to copy to managed nodes.                    |
| `vars/`      | High-precedence variables for the role.                   |
| `defaults/`  | Low-precedence default variables that users can override. |
| `meta/`      | Role metadata and dependencies.                           |
| `tests/`     | Test playbooks and inventories.                           |
| `README.md`  | Documentation for the role.                               |


## Example

### tasks/main.yml

```yaml
---
- name: Install Apache
  yum:
    name: httpd
    state: present

- name: Copy config
  template:
    src: httpd.conf.j2
    dest: /etc/httpd/conf/httpd.conf
  notify: Restart Apache
```

### handlers/main.yml

```yaml
---
- name: Restart Apache
  service:
    name: httpd
    state: restarted
```

### defaults/main.yml

```yaml
---
http_port: 80
```

### templates/httpd.conf.j2

```text
Listen {{ http_port }}
```



### Easy way to remember for interviews

**Tasks → Handlers → Templates → Files → Variables → Defaults → Meta**

* **tasks** = What to do
* **handlers** = Actions triggered after changes
* **templates** = Dynamic configuration files
* **files** = Static files
* **vars/defaults** = Variables
* **meta** = Role information and dependencies

The **only directory that is practically required is `tasks/main.yml`**, because that's where Ansible starts executing the role.


---


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
****************************
# Molecule

**1. What is molecule in Ansible**

Molecule is a testing framework for Ansible roles. It allows you to automatically verify that your role works correctly before deploying it to real servers.

In 2 lines (Interview Answer)

Molecule is used to test Ansible roles by creating temporary environments (Docker containers, VMs, etc.), executing the role, and validating the results. It helps catch configuration issues early and ensures the role behaves as expected.

---
