## Question  
`/var` is almost 90% full. What will be your next steps?

### 📝 Short Explanation  
This question checks your troubleshooting and disk management skills. The `/var` directory is commonly used for logs, spools, caches, and runtime data — so issues here can break system processes or fill up disks silently.

## ✅ Answer  
My first step is to identify what’s consuming the space inside `/var`. Then I would clean up unnecessary files like rotated logs, caches, or orphaned packages — and put alerts or log rotation in place to avoid recurrence.

### 📘 Detailed Explanation  

---

### ✅ Step 1: Inspect Disk Usage Under `/var`

```bash
sudo du -sh /var/* | sort -hr | head -10
```
This will show which directories inside `/var` are consuming the most space — usually it’s `/var/log`, `/var/cache`, or `/var/lib/docker`.

---

### ✅ Step 2: Clean Log Files  
If `/var/log` is the culprit:

```bash
sudo journalctl --vacuum-size=200M
sudo rm -rf /var/log/*.gz /var/log/*.[0-9]
```

Or truncate large log files:
```bash
sudo truncate -s 0 /var/log/syslog
```

---

### ✅ Step 3: Clear Package Cache  
If using `apt` or `yum`, clear the package manager cache:

```bash
sudo apt clean         # Debian/Ubuntu
sudo yum clean all     # RHEL/CentOS
```

---

### ✅ Step 4: Check Docker Artifacts  
If the server runs containers:

```bash
docker system df        # See what’s taking space
docker system prune -a  # Remove unused containers/images
```

**⚠️ Warning:** Prune removes *unused* images and volumes — be cautious on production systems.

---

### ✅ Step 5: Consider Moving or Archiving Data  
If data in `/var` is needed but rarely accessed:
- Archive old logs to `/home` or S3
- Use `logrotate` to compress and limit logs:
  ```bash
  sudo nano /etc/logrotate.conf
  ```

---

### ✅ Step 6: Set Up Alerts and Monitoring  
- Install `ncdu`, `duf`, or setup Prometheus/Grafana alerts for disk usage thresholds.
- Automate cleanup with cron or systemd timers if appropriate.

---

### 🧠 Why `/var` Fills Up:
- Verbose logging (e.g., failed cron jobs, app debug logs)
- Docker images/layers
- Orphaned cache files
- Email spools or crash dumps

> Summary:  
> Quickly inspect, clean, and automate monitoring. Ensure critical services like journald, docker, and package managers are not starved of space.

---

# Summarize


## Scenario: `/var` Partition is 90–95% Full on a Linux/EC2 Instance. What Will You Do?

This is a common scenario-based interview question.

### Scenario

The interviewer tells you:

> "One of your EC2 instances or Linux servers has a `/var` partition that is 90–95% utilized. What will you do?"

This question tests your practical Linux administration experience because disk space issues are very common in production environments.

If `/var` reaches 100% utilization, services may stop functioning correctly because many applications write logs and temporary files under `/var`.

For example:

* Apache logs
* Nginx logs
* Application logs
* Package manager caches
* Temporary files

All of these can consume significant disk space over time.



## Step 1: Check Disk Usage

First, log in to the server and verify filesystem utilization:

```bash
df -h
```

Example output:

```bash
Filesystem      Size  Used Avail Use%
/dev/xvda1       20G   19G   1G   95%
```

This confirms that the partition is nearly full.


## Step 2: Identify Which Directory Is Consuming Space

Navigate to `/var` and check directory sizes:

```bash
cd /var
sudo du -sh *
```

Example output:

```bash
4.0K    backups
1.2G    cache
5.8G    log
300M    tmp
```

This helps identify the directory consuming the most space.


## Step 3: Investigate `/var/log`

In many cases, log files are the primary reason for high utilization.

```bash
cd /var/log
sudo du -sh *
```

If log files are consuming significant space, consider the following actions:

### Delete Old Logs

```bash
sudo rm old-log-file.log
```

Only delete logs after confirming they are no longer needed.

### Compress Old Logs

Instead of deleting them, archive them:

```bash
gzip application.log
```

or

```bash
tar -czvf logs.tar.gz *.log
```

Compressed logs consume much less disk space.

### Configure Log Rotation

Linux provides `logrotate` to automatically rotate, compress, and remove old logs.

Example:

```bash
sudo logrotate -f /etc/logrotate.conf
```

This is the preferred long-term solution.


## Step 4: Check Package Manager Cache

Package managers store downloaded packages in cache directories under `/var`.

### Ubuntu/Debian

```bash
sudo apt clean
```

This removes cached package files.

### RHEL/CentOS/Amazon Linux

```bash
sudo yum clean all
```

or

```bash
sudo dnf clean all
```

This can free a significant amount of disk space on older systems.


## Step 5: Check Temporary Files

Investigate temporary directories:

```bash
cd /var/tmp
ls -ltr
```

and

```bash
cd /tmp
ls -ltr
```

Remove unnecessary temporary files if they are no longer being used.


## Step 6: Investigate Other Large Directories

If logs are not the culprit, continue investigating:

```bash
sudo du -sh /var/* | sort -hr
```

Look for directories such as:

* `/var/cache`
* `/var/lib`
* `/var/tmp`

Be careful with `/var/lib` because it contains application data, package information, databases, and system files. Avoid deleting anything unless you fully understand its purpose.


## Long-Term Solutions

If the issue occurs frequently:

1. Implement proper log rotation.
2. Archive logs to external storage such as S3.
3. Monitor disk utilization using monitoring tools.
4. Increase the filesystem size if business requirements justify it.
5. Set alerts when utilization exceeds 80–85%.


## Interview Answer

> If I notice that the `/var` partition is 90–95% utilized, my first step is to log in to the server and verify disk usage using `df -h`. Then I will identify the directories consuming the most space using `du -sh /var/*`. In most cases, log files under `/var/log` are responsible, so I will either delete unnecessary old logs, compress them, or configure log rotation. I will also check package manager caches and temporary files under `/var/cache`, `/var/tmp`, and `/tmp`. If required, I will clean those safely. Finally, I will implement preventive measures such as log rotation, monitoring alerts, and filesystem expansion if needed. This ensures the partition does not reach 100% utilization and impact application availability.

---
