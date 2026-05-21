```yaml

---
# Playbook name
- name: Install Apache HTTP Server

  # Target hosts from inventory
  hosts: webservers

  # Run tasks with sudo/root privileges
  become: yes

  # List of tasks to execute
  tasks:

    # Task name shown during execution
    - name: Install httpd package

      # yum module installs packages in RHEL-based systems
      yum:

        # Package name
        name: httpd

        # Ensure package is installed
        state: present

    # Start and enable Apache service
    - name: Start and enable httpd service

      # service module manages services
      service:

        # Service name
        name: httpd

        # Start the service
        state: started

        # Enable service during boot
        enabled: yes

```