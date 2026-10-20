# Ansible DevOps Interview Preparation Guide

This guide covers core Ansible concepts, variable precedence rules, ad-hoc commands, enterprise architectures, playbook scripting, and secret management.

---

## 📑 Table of Contents
1. [Core Concepts & Ad-Hoc Commands](#1-core-concepts--ad-hoc-commands)
   - [The `-m` Flag in Ad-Hoc Commands](#the--m-flag-in-ad-hoc-commands)
   - [Checking OS Family via Setup Module](#checking-os-family-via-setup-module)
   - [Idempotency Explained](#idempotency-explained)
2. [Variable Precedence Rules](#2-variable-precedence-rules)
3. [Enterprise Multi-Control Node Architecture](#3-enterprise-multi-control-node-architecture)
4. [Playbooks vs. Roles](#4-playbooks-vs-roles)
5. [Deep Dive Study Guides](#5-deep-dive-study-guides)
   - [Targeting Specific Nodes with `--limit`](#targeting-specific-nodes-with---limit)
   - [Managing Secrets with Ansible Vault](#managing-secrets-with-ansible-vault)
   - [Practical Playbook Scripting Challenge](#practical-playbook-scripting-challenge)

---

## 1. Core Concepts & Ad-Hoc Commands

### The `-m` Flag in Ad-Hoc Commands
* **Question**: What is `-m` in Ansible?
* **Answer**: The `-m` option specifies the **module** to execute on target hosts. By default, Ansible uses the `command` module if none is specified.
* **Example**:
  ```bash
  # Execute shell command on all nodes
  ansible all -m shell -a "df -h"

  # Install a package using the apt module
  ansible webservers -m apt -a "name=nginx state=present" --become
  ```
  Here:
  - `-m`: Target module (`shell`, `apt`, `yum`, `setup`, `copy`, etc.)
  - `-a`: Module arguments passed to that module.

### Checking OS Family via Setup Module
* **Question**: How do you check the OS family using an Ansible ad-hoc command?
* **Answer**: Use the `setup` module with a filter parameter:
  ```bash
  ansible hosts -m setup -a "filter=ansible_os_family" -u ansible
  ```
  **Sample Output**:
  ```json
  192.168.56.12 | SUCCESS => {
      "ansible_facts": {
          "ansible_os_family": "Debian",
          "discovered_interpreter_python": "/usr/bin/python3.10"
      },
      "changed": false
  }
  ```

### Idempotency Explained
* **Question**: What is idempotency in Ansible?
* **Answer**: Idempotency means executing a playbook multiple times produces the exact same end state without making unnecessary changes.
  - If a package is already installed, Ansible reports `ok` (status: unchanged).
  - If a package is missing, Ansible installs it and reports `changed`.
  - Modules like `apt`, `file`, `template`, and `service` are idempotent; raw `shell` or `command` modules require `creates` or `removes` clauses to maintain idempotency.

---

## 2. Variable Precedence Rules

### Interview Scenario
Which value will be assigned to host `web01` given the following files?

```yaml
# host_vars/web01.yml
port: 8080

# group_vars/webservers.yml
port: 80

# playbook.yml (vars block)
vars:
  port: 9090
```

### Precedence Evaluation
In Ansible's 22-level precedence hierarchy:
1. `group_vars/webservers.yml` → 80 (Lowest among these three)
2. `host_vars/web01.yml` → 8080
3. `playbook.yml` vars block → 9090 (Highest among these three)
4. *Note: Extra vars passed via CLI (`-e "port=8000"`) would override all of them!*

**Answer**: `9090`

---

## 3. Enterprise Multi-Control Node Architecture

### Interview Scenario
> *"You are designing an automation system with multiple control nodes and want them to share inventories, roles, and playbooks consistently. How do you design it?"*

### Solution
1. **Dynamic Inventory**: Instead of static inventory files on each node, use dynamic inventory plugins (e.g., AWS EC2, Azure, VMware, or ServiceNow plugins) so control nodes discover live target hosts automatically using tags.
2. **Centralized Git Repository**: Maintain all playbooks, roles, and configuration files in a single Git repository. Control nodes pull configurations via CI/CD webhooks or scheduled syncs.
3. **Ansible Automation Controller (AWX / Tower)**: Use AWX/Automation Controller as a cluster with an external PostgreSQL database, providing shared project syncs, credential management, role-based access control (RBAC), and centralized job scheduling.

---

## 4. Playbooks vs. Roles

| Feature | Playbook | Role |
| :--- | :--- | :--- |
| **Structure** | Single YAML file with tasks and plays | Modular directory structure (`tasks/`, `handlers/`, `templates/`, `vars/`, `defaults/`, `meta/`) |
| **Reusability**| Harder to share across projects | Highly reusable, publishable to Ansible Galaxy |
| **Organization**| Good for simple, one-off automation | Standard for enterprise multi-tier configurations |

---

## 5. Deep Dive Study Guides

* 🎯 **Node Filtering**: [Targeting Specific Nodes with `--limit`](my-preparation/limit.md)  
  *How to run playbooks against specific hosts, groups, failed hosts (`--limit @playbook.retry`), and patterns.*

* 🔐 **Secrets Management**: [Ansible Vault & Secret Security](my-preparation/secrets.md)  
  *Encrypting sensitive strings, entire files, multi-environment passwords, and automated CI/CD decryption.*

* 💻 **Hands-On Scripting**: [Practical Monitoring Agent Playbook](Scripting.md)  
  *Comprehensive production playbook deploying monitoring agents, handlers, systemd services, users, and permissions.*
