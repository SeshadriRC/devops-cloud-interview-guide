## Question  
Your application generates large logs in `/var/log/myapp/` and there's no log rotation setup.

**Task:**  
Write a shell script that compresses logs older than 7 days and deletes logs older than 30 days. Also, run it daily via cron.

### 📝 Short Explanation  
This question tests your ability to manage disk space with log compression and retention — a common task in DevOps. You're expected to automate it safely and consistently using a cron job.

## ✅ Answer  

### 🖥️ Shell Script: `log_cleanup.sh`

```bash
#!/bin/bash

# Directory where logs are stored
LOG_DIR="/var/log/myapp"
LOG_FILE="/var/log/myapp/log_rotation.log"

# Ensure the log directory exists
if [ ! -d "$LOG_DIR" ]; then
    echo "[$(date)] ERROR: Log directory $LOG_DIR does not exist!" >> "$LOG_FILE"
    exit 1                   # means stop the script and return an error status.
fi

# Compress logs older than 7 days (but newer than 30)
find "$LOG_DIR" -type f -name "*.log" -mtime +7 -mtime -30 ! -name "*.gz" -exec gzip {} \; -exec echo "[$(date)] Compressed: {}" >> "$LOG_FILE" \;

# Delete compressed logs older than 30 days
find "$LOG_DIR" -type f -name "*.gz" -mtime +30 -exec rm -f {} \; -exec echo "[$(date)] Deleted: {}" >> "$LOG_FILE" \;

# Optional: Delete uncompressed logs older than 30 days
find "$LOG_DIR" -type f -name "*.log" -mtime +30 -exec rm -f {} \; -exec echo "[$(date)] Deleted (uncompressed): {}" >> "$LOG_FILE" \;

# Done
echo "[$(date)] Log rotation completed successfully." >> "$LOG_FILE"

```


## Cronjob


To run this script as a cron job in Linux, follow these steps.

### 1. Save the Script

Create a file, for example:

```bash
sudo vi /usr/local/bin/log_rotate.sh
```

Paste the script and save it.

---

### 2. Make It Executable

```bash
sudo chmod +x /usr/local/bin/log_rotate.sh
```

Verify:

```bash
ls -l /usr/local/bin/log_rotate.sh
```

You should see:

```bash
-rwxr-xr-x
```

---

### 3. Test the Script Manually

Before scheduling it, run it manually:

```bash
sudo /usr/local/bin/log_rotate.sh
```

Check:

```bash
cat /var/log/myapp/log_rotation.log
```

for any errors.

---

### 4. Add the Cron Job

Edit the root user's crontab:

```bash
sudo crontab -e
```

#### Run Daily at 1:00 AM

```cron
0 1 * * * /usr/local/bin/log_rotate.sh >/dev/null 2>&1
```

#### Run Every Day at Midnight

```cron
0 0 * * * /usr/local/bin/log_rotate.sh >/dev/null 2>&1
```

#### Run Every Sunday at 2:00 AM

```cron
0 2 * * 0 /usr/local/bin/log_rotate.sh >/dev/null 2>&1
```

---

### 5. Verify Cron Entry

```bash
sudo crontab -l
```

Example output:

```cron
0 1 * * * /usr/local/bin/log_rotate.sh >/dev/null 2>&1
```

---

### 6. Check Cron Logs

On RHEL/CentOS/Amazon Linux:

```bash
grep CRON /var/log/cron
```

On Ubuntu:

```bash
grep CRON /var/log/syslog
```

---

### Production Recommendation

Instead of suppressing all output:

```cron
0 1 * * * /usr/local/bin/log_rotate.sh >/dev/null 2>&1
```

redirect cron output to a separate log:

```cron
0 1 * * * /usr/local/bin/log_rotate.sh >> /var/log/myapp/cron.log 2>&1
```

This makes troubleshooting easier if the script fails.

### Interview Point

If asked how you automate log cleanup in Linux:

> "I create a shell script to compress logs older than 7 days and delete logs older than 30 days. I place the script in `/usr/local/bin`, make it executable, and schedule it using `crontab` to run daily during non-business hours. The script writes execution details to a dedicated log file for auditing and troubleshooting."

---
