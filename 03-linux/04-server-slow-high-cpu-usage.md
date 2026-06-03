## Question  
Linux Server is slow due to high CPU utilization. How will you fix it?

### 📝 Short Explanation  
This question assesses your ability to diagnose performance issues, identify root causes, and take targeted actions to reduce CPU load on a production server.

## ✅ Answer  
I would begin by identifying which processes are consuming the most CPU using tools like `top`, `htop`, or `pidstat`, then analyze whether it's due to a misbehaving application, runaway process, or scheduled job. Based on the findings, I’d take corrective action — either by killing the process, adjusting resource limits, or scaling the workload.

### 📘 Detailed Explanation  

---

### ✅ Step 1: Check Load Average  
```bash
uptime
```
Example output:
```
14:02:03 up  3 days,  4:55,  2 users,  load average: 6.02, 4.33, 2.89
```
A load average consistently higher than the number of CPU cores indicates overutilization.

---

### ✅ Step 2: Identify CPU-Heavy Processes  
```bash
top -o %CPU
```
or more interactively:
```bash
htop
```

This shows which processes are consuming the most CPU.

---

### ✅ Step 3: Drill Down with `ps` or `pidstat`  
```bash
ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head
```

or:
```bash
pidstat -u 1 5
```

These give detailed insight into CPU consumption over time.

---

### ✅ Step 4: Investigate the Cause  
Based on what you see, ask:
- Is it a specific app (e.g., Java, Python, Node.js)?
- Is there a cron job or batch script running?
- Is a service misconfigured and looping?
- Is it caused by a known bug (e.g., zombie processes)?

---

### ✅ Step 5: Take Corrective Action  
- Kill or restart runaway process:
  ```bash
  kill -9 <pid>
  systemctl restart <service>
  ```
- Scale the application or move workloads
- Limit resource usage using `nice`, `cpulimit`, or cgroups
- Tune app performance (e.g., DB queries, memory leaks)

---

### ✅ Step 6: Check Logs  
```bash
journalctl -xe
tail -f /var/log/syslog
```
Logs may reveal:
- App crashes
- High retry loops
- Configuration issues

---

### ✅ Step 7: Implement Preventive Measures  
- Set CPU/memory limits in containerized apps
- Use monitoring tools like `Prometheus + Grafana`
- Configure alerts for high CPU (e.g., above 80% for 5 mins)
- Refactor long-running or expensive tasks

---

### 🧠 Real-Life Examples:
- A cron script looping due to a bad condition
- A Java app stuck in infinite recursion
- Docker containers running unbounded scraping jobs
- Antivirus or audit daemon consuming CPU after log floods

> Summary:  
> Use `top`, `htop`, `ps`, and `pidstat` to identify heavy processes. Fix the root cause and add monitoring to avoid similar issues in the future.

---

# Summarize


# Scenario: High CPU Utilization Causing Slowness on a Production Server

### Interview Question

**"A production Linux/EC2 server is experiencing slowness due to very high CPU utilization. What would you do?"**

This is a critical production issue because if the server is slow, the applications running on it will also be impacted.

In a production environment, your first priority is to **restore service stability quickly**. Detailed root-cause analysis can happen afterward.


## Step 1: Log in and Identify the CPU-Consuming Process

Immediately log in to the server and check which process is consuming CPU.

Use:

```bash
top
```

or

```bash
htop
```

(`top` is available by default on almost every Linux system.)

These commands show the running processes and their CPU consumption in real time.


## Step 2: Identify the Process ID (PID)

From the output, identify:

* The process name
* The Process ID (PID)
* The CPU utilization percentage

Example:

```bash
PID     USER     %CPU     COMMAND
31251   root     99.5     java
```

Now you know which process is causing the issue.


## Step 3: Decide Whether to Kill or Deprioritize the Process

The next action depends on the importance of the process.

### Case 1: Non-Critical Process

If the process is not important for production operations, terminate it.

Example:

```bash
kill -9 <PID>
```

This immediately frees CPU resources.

Examples of potentially non-critical processes:

* Test jobs
* Background scripts
* Accidental processes
* Build jobs running on the server


### Case 2: Critical Production Process

If the process is a critical service such as:

* Nginx
* Apache
* Java application
* Database process

you should not kill it immediately.

Instead, reduce its CPU scheduling priority.

Use:

```bash
renice 10 -p <PID>
```

or start processes with:

```bash
nice
```

Increasing the nice value lowers the process priority, allowing other processes to receive more CPU time.


## Why Use `renice`?

When a process is deprioritized:

* The CPU scheduler allocates less CPU time to it.
* Other critical processes receive more CPU resources.
* Overall system responsiveness improves.

This can provide immediate relief while keeping the application running.


## Production vs Non-Production Approach

### Production Environment

Focus on restoring service quickly:

1. Identify the process.
2. Kill non-critical processes if necessary.
3. Deprioritize critical processes.
4. Stabilize the server.

### UAT / Staging / Development Environment

You can spend more time investigating:

1. Capture logs.
2. Collect thread dumps or application dumps.
3. Share findings with developers.
4. Perform root-cause analysis.
5. Apply a permanent fix.


## Useful Commands

Check CPU utilization:

```bash
top
```

Find top CPU consumers:

```bash
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head
```

Kill a process:

```bash
kill -9 <PID>
```

Reduce process priority:

```bash
renice 10 -p <PID>
```

Monitor CPU continuously:

```bash
top
```



## Interview Answer

> If a production server is experiencing slowness due to high CPU utilization, my first action is to log in and identify the CPU-consuming process using `top` or `htop`. After identifying the PID, I determine whether the process is critical. If it is a non-critical process, I terminate it to quickly free CPU resources. If it is a critical production service, I reduce its scheduling priority using `renice` rather than killing it. Once the server is stabilized, I collect logs, process details, and other diagnostics for root-cause analysis with the development team. In a production outage, restoring service availability is the immediate priority, while detailed investigation comes afterward.

---
