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
# Script explanation

### What This Script Does

The goal of this script is to **manage log files automatically** so that the `/var/log/myapp` directory does not consume excessive disk space.

The logic is:

* Logs newer than 7 days → Keep as-is
* Logs older than 7 days → Compress them
* Logs older than 30 days → Delete them

This is a common DevOps task to prevent disks from filling up.

---

## Step 1: Define Variables

```bash
LOG_DIR="/var/log/myapp"
LOG_FILE="/var/log/myapp/log_rotation.log"
```

### Purpose

* `LOG_DIR` → Location of application logs.
* `LOG_FILE` → Script execution log.

Example:

```text
/var/log/myapp/
├── app.log
├── app.log.1
├── app.log.2
└── log_rotation.log
```

The script records its actions inside `log_rotation.log`.

---

## Step 2: Verify Directory Exists

```bash
if [ ! -d "$LOG_DIR" ]; then
    echo "[$(date)] ERROR: Log directory $LOG_DIR does not exist!" >> "$LOG_FILE"
    exit 1
fi
```

### Purpose

Checks whether `/var/log/myapp` exists.

If the directory is missing:

* Writes an error message.
* Stops execution.

Example:

```text
[Wed Jun 3 10:00:00 UTC 2026] ERROR: Log directory /var/log/myapp does not exist!
```

Without this check, the script may fail unexpectedly.

---

## Step 3: Compress Logs Older Than 7 Days

```bash
find "$LOG_DIR" -type f -name "*.log" -mtime +7 -mtime -30 ! -name "*.gz" \
-exec gzip {} \; \
-exec echo "[$(date)] Compressed: {}" >> "$LOG_FILE" \;
```

### Understanding the Command

```bash
find "$LOG_DIR"
```

Search inside:

```text
/var/log/myapp
```

---

```bash
-type f
```

Only files.

Ignore directories.

---

```bash
-name "*.log"
```

Only log files.

Examples:

```text
app.log
server.log
error.log
```

---

```bash
-mtime +7
```

Files older than 7 days.

---

```bash
-mtime -30
```

Files newer than 30 days.

Combined:

```bash
-mtime +7 -mtime -30
```

Means:

```text
Age > 7 days
AND
Age < 30 days
```

---

```bash
-exec gzip {} \;
```

Compresses each matching file.

Example:

Before:

```text
app.log
```

After:

```text
app.log.gz
```

---

### Example

Before:

```text
app.log      (10 days old)
error.log    (15 days old)
```

After:

```text
app.log.gz
error.log.gz
```

---

## Step 4: Delete Compressed Logs Older Than 30 Days

```bash
find "$LOG_DIR" -type f -name "*.gz" -mtime +30 \
-exec rm -f {} \; \
-exec echo "[$(date)] Deleted: {}" >> "$LOG_FILE" \;
```

### Purpose

Find compressed files:

```text
*.gz
```

that are older than 30 days and remove them.

Example:

Before:

```text
app.log.gz      45 days old
error.log.gz    60 days old
```

After:

```text
Deleted
Deleted
```

---

## Step 5: Delete Uncompressed Logs Older Than 30 Days

```bash
find "$LOG_DIR" -type f -name "*.log" -mtime +30 \
-exec rm -f {} \; \
-exec echo "[$(date)] Deleted (uncompressed): {}" >> "$LOG_FILE" \;
```

### Why?

Suppose somebody manually copied logs into the folder and they never got compressed.

This cleanup catches those files as well.

Example:

```text
old.log     45 days old
```

Deleted immediately.

---

## Step 6: Log Completion

```bash
echo "[$(date)] Log rotation completed successfully." >> "$LOG_FILE"
```

Example output:

```text
[Wed Jun 3 01:00:00 UTC 2026] Log rotation completed successfully.
```

This provides an audit trail.

---

# Cron Job Explanation

Cron automates script execution.

---

## Save Script

```bash
sudo vi /usr/local/bin/log_rotate.sh
```

Store the script in a common executable location.

---

## Make It Executable

```bash
sudo chmod +x /usr/local/bin/log_rotate.sh
```

Without execute permission, cron cannot run it.

---

## Test Manually

```bash
sudo /usr/local/bin/log_rotate.sh
```

Always test before scheduling.

---

## Schedule with Cron

```bash
sudo crontab -e
```

Add:

```cron
0 1 * * * /usr/local/bin/log_rotate.sh
```

---

### Understanding Cron Syntax

```cron
0 1 * * *
│ │ │ │ │
│ │ │ │ └── Day of Week
│ │ │ └──── Month
│ │ └────── Day of Month
│ └──────── Hour
└────────── Minute
```

---

```cron
0 1 * * *
```

Means:

```text
Minute = 0
Hour   = 1 AM
Every day
Every month
Every weekday
```

Result:

```text
Runs daily at 1:00 AM
```

---

# Example Timeline

Assume today is:

```text
June 30
```

Files:

```text
app.log       created June 25 (5 days old)
server.log    created June 20 (10 days old)
error.log.gz  created May 15 (46 days old)
```

### Script Result

```text
app.log
```

5 days old → No action

---

```text
server.log
```

10 days old → Compress

```text
server.log.gz
```

---

```text
error.log.gz
```

46 days old → Delete

---

# Interview Answer

> This script automates log management. It first verifies that the log directory exists. Then it compresses `.log` files that are older than 7 days to save disk space. Compressed logs older than 30 days are deleted based on the retention policy. It also removes any uncompressed logs older than 30 days. The script records all actions in a log file for auditing. Finally, I schedule it using cron to run daily during off-business hours, ensuring that log files do not fill up the filesystem and impact application availability.

---
