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


