An **Ansible Role** is a **standard way of organizing Ansible automation into reusable components**.

Instead of putting everything inside one large playbook, you divide the automation into folders/files based on purpose.

### Simple example

Suppose you want to install and configure **Nginx**.

Without a role:

```yaml
- hosts: webservers
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Copy nginx config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf

    - name: Start nginx
      service:
        name: nginx
        state: started
```

With a role:

```text
roles/
└── nginx/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    │   └── nginx.conf.j2
    ├── files/
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    └── meta/
        └── main.yml
```

Your playbook becomes much cleaner:

```yaml
- hosts: webservers
  roles:
    - nginx
```

### What each folder does

| Folder | Purpose |
|---|---|
| `tasks/` | Main automation tasks |
| `handlers/` | Actions triggered by `notify`, e.g. restart service |
| `templates/` | Jinja2 configuration templates |
| `files/` | Static files to copy |
| `vars/` | Role variables with higher precedence |
| `defaults/` | Default variables; easy to override |
| `meta/` | Role dependencies/metadata |

### Real DevOps example

You could create:

```text
roles/
├── docker/
├── nginx/
├── jenkins/
├── java/
└── kubernetes/
```

Then reuse them across different environments:

```yaml
- hosts: dev
  roles:
    - java
    - docker

- hosts: prod
  roles:
    - java
    - docker
    - nginx
```

### Interview answer

> **"Ansible role is a reusable and standardized structure for organizing Ansible automation. It separates tasks, variables, templates, files, and handlers into predefined directories, making automation modular, maintainable, and reusable across different environments."**

**Key point:**  
**Playbook = what you want to automate**  
**Role = reusable structure containing the automation logic**
