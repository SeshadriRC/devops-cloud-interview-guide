# Linux Administration & Troubleshooting Interview Guide

Linux is the foundational operating system for cloud, DevOps, and container engineering. This guide categorizes the key system scenarios and diagnostic questions asked in senior technical interviews.

---

## 📑 Core Interview Topics

* **Complete Q&A Document**: [Linux Interview Q&A](Interview-Q&A.md)

### 1. System Calls & Kernel Internals
* **File System Operations**: What is `readdir()`, how directory entries, inode numbers, and record lengths work.
* **Kernel Signals**: `kill -15` (SIGTERM - graceful shutdown) vs. `kill -9` (SIGKILL - immediate un-catchable termination).

### 2. Privileges, Sudo & User Management
* **Privilege Escalation**: Difference between `sudo su - ansible` (uses caller's sudo privileges to become root, then switches to ansible) and `su - ansible` (requires knowing the ansible user's password directly).
* **Login Environments**: What the hyphen (`-`) does in `su -` (clears existing environment and loads target user's `.bash_profile`, `.bashrc`, and `PATH`).

### 3. Process, CPU & Memory Troubleshooting
* **Load Average**: Understanding the 1, 5, and 15-minute load averages relative to available CPU cores. High load with low CPU usage typically indicates high I/O wait (`wa` in `top`).
* **Zombie vs. Orphan Processes**:
  - *Zombie*: Terminated process whose exit status has not yet been read by its parent via `wait()`. Consumes no CPU/memory, only a process table slot (PID). Kill the parent process to remove zombies.
  - *Orphan*: Parent process died before child; automatically adopted by `systemd` / `init` (PID 1).
* **Memory Leaks & Buffer/Cache**: Understanding `free -m` (available memory vs cached/buffers) and Linux OOM Killer heuristics.

### 4. Storage & Filesystem Diagnostics
* **Inodes vs. Disk Space**: Running out of inodes (`df -i`) while `df -h` shows plenty of space (caused by millions of zero-byte or tiny files).
* **Deleted Files Holding Space**: When disk space is full but files cannot be found (`lsof | grep deleted` identifies open file descriptors holding disk blocks).

---

## 🛠️ Essential Diagnostic Command Toolkit

```bash
# Process and CPU monitoring
top -c
htop
vmstat 1 5

# Disk and I/O bottlenecks
df -h
df -i
iostat -xz 1 5

# Open files, sockets and network listeners
lsof -i :8080
ss -tulpn
netstat -plant

# Tracing system calls of a stuck process
strace -p <PID> -s 1024
```
