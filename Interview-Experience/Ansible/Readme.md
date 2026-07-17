1. What is `-m` in ansible

- It is used to specify which Ansible module to execute on target hosts.

```bash
ansible all -m shell -a "df -h"

-a -> is a argument

```

---

2. Which value will be used for host web01?

```bash
host_vars/web01.yml      → port: 8080
group_vars/webservers.yml → port: 80
playbook.yml (vars)       → port: 9090


group_vars/webservers.yml → 80     (lowest)
host_vars/web01.yml → 8080
playbook vars → 9090     (highest here)


Answer is: 9090
```
---

3. You're designing an automation system with multiple control nodes and wants them to share inventory and roles


- Use dynamic inventory with centralized Git-based role storage
- In enterprise Ansible setups with multiple control nodes, we generally use dynamic inventory for automatic host discovery and maintain roles/playbooks in a centralized Git repository to ensure consistency and scalability.

---
4. Check the OS family using ansible adhoc command

```bash
ansible hosts -m setup -a "filter=ansible_os_family" -u ansible


[WARNING]: Host '192.168.56.12' is using the discovered Python interpreter at '/usr/bin/python3.10', but future installation of another Python interpreter could cause a different interpreter to be discovered. See https://docs.ansible.com/ansible-core/2.20/reference_appendices/interpreter_discovery.html for more information.
192.168.56.12 | SUCCESS => {
    "ansible_facts": {
        "ansible_os_family": "Debian",
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false
}

[WARNING]: Host '192.168.56.11' is using the discovered Python interpreter at '/usr/bin/python3.9', but future installation of another Python interpreter could cause a different interpreter to be discovered. See https://docs.ansible.com/ansible-core/2.20/reference_appendices/interpreter_discovery.html for more information.
192.168.56.11 | SUCCESS => {
    "ansible_facts": {
        "ansible_os_family": "RedHat",
        "discovered_interpreter_python": "/usr/bin/python3.9"
    },
    "changed": false
}

```
---

5. What is Ansible handlers ?

- Handler is one of the task , it will be executed only when another task in the playbook notifies them.

[blog](https://spacelift.io/blog/ansible-handlers#what-are-ansible-handlers)

---
6. What is Ansible roles ?

[link](https://medium.com/@shaiksaidavali223/understanding-ansible-roles-a-comprehensive-guide-a47b160c16b5)


---
7. Ansible limit particular server or particular server in a group

[link](https://github.com/SeshadriRC/devops-cloud-interview-guide/blob/main/Interview-Experience/Ansible/my-preparation/limit.md)

---

8. Ansible secrets [link](https://github.com/SeshadriRC/devops-cloud-interview-guide/blob/main/Interview-Experience/Ansible/my-preparation/secrets.md)

---
