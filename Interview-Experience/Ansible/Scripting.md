## Question 47 – Ansible Scripting

### Requirement

Your organization needs to deploy a monitoring agent on all Linux servers using Ansible.

Write an Ansible playbook that:

* Install package **net-tools**
* Create user **monitoruser**
* Create directory **/etc/monitoring**
* Copy **monitor.conf** to **/etc/monitoring/monitor.conf**
* Ensure:

  * owner = **monitoruser**
  * permission = **0644**
* Create a **systemd service** for monitoring agent
* Start and enable the service
* Restart the service **only if configuration file changes**

### Constraints

* Use **handlers** wherever appropriate
* Use **Ansible modules** wherever possible
* Make the playbook **idempotent**
* Assume target nodes are **Linux servers**


### Complete Ansible Playbook

```yaml
---
- name: Deploy Monitoring Agent
  hosts: all
  become: true

  tasks:

    - name: Install net-tools package
      package:
        name: net-tools
        state: present

    - name: Create monitor user
      user:
        name: monitoruser
        state: present

    - name: Create monitoring directory
      file:
        path: /etc/monitoring
        state: directory
        owner: monitoruser
        group: monitoruser
        mode: '0755'

    - name: Copy monitoring configuration file
      copy:
        src: monitor.conf
        dest: /etc/monitoring/monitor.conf
        owner: monitoruser
        group: monitoruser
        mode: '0644'
      notify:
        - Restart monitoring service

    - name: Create systemd service file
      copy:
        dest: /etc/systemd/system/monitoring.service
        mode: '0644'
        content: |
          [Unit]
          Description=Monitoring Agent
          After=network.target

          [Service]
          User=monitoruser
          ExecStart=/usr/bin/sleep infinity
          Restart=always

          [Install]
          WantedBy=multi-user.target

    - name: Reload systemd daemon
      systemd:
        daemon_reload: true

    - name: Enable and start monitoring service
      systemd:
        name: monitoring
        state: started
        enabled: true

  handlers:

    - name: Restart monitoring service
      systemd:
        name: monitoring
        state: restarted
```

### Key points for interview

* `package` → installs **net-tools**
* `user` → creates **monitoruser**
* `file` → creates **directory**
* `copy` → copies **monitor.conf**
* `notify + handler` → restart **only if config changes**
* `systemd` → start & enable service
* Idempotent → safe to run multiple times

---
