1. Your script receives a filename that may include spaces and wildcard characters. Which approach is best ?

- Always quote variables when handling filenames.

```bash
file="my data*.txt"

cat "$file"
```

---

2. Which command safely counts the number of matching lines without printing them?

```bash
Ans:
grep -c "ERROR" app.log

sesha@LAPTOP-QMBUJPPJ:~$ grep "ERROR" error.log
ERROR Connection failed
ERROR Timeout
sesha@LAPTOP-QMBUJPPJ:~$ grep -c "ERROR" error.log
2
```
---

3. Which command creates a uniquely named temporary file safely in Bash scripts?

- mktemp creates a unique temporary file (or directory) securely and avoids naming conflicts.

```bash
temp_file=$(mktemp)

echo "hello" > "$temp_file"
```

or

```bash
sesha@LAPTOP-QMBUJPPJ:/tmp$ echo "hello temp" > /tmp/tmp.T7RjgLaxtN

sesha@LAPTOP-QMBUJPPJ:/tmp$ cat /tmp/tmp.T7RjgLaxtN
hello temp
```

---

4. Assume there is a log file(ora.logs), which has ORA errors. i need to print only the ORA errors

```bash
grep -o "ORA-00019" ora.logs
```

---

5. Write a shell scripting where you need to print Today's date with your name , it should run interval of 5 seconds, name need to pass as an env variable.

export name="Welcome"

```bash
#!/bin/bash

while true
do
    echo "$(date) - $name"
    sleep 5
done
```

---

6. write a shell script where it should delete all docker images and shedule it in a cronjob , assume its running on wednesday.

```bash
#!/bin/bash

# Log file
LOGFILE="/var/log/docker_image_cleanup.log"

echo "==========================================" >> $LOGFILE
echo "Docker Image Cleanup Started: $(date)" >> $LOGFILE

# Remove all Docker images
docker rmi -f $(docker images -q) >> $LOGFILE 2>&1

echo "Docker Image Cleanup Completed: $(date)" >> $LOGFILE
echo "==========================================" >> $LOGFILE
```

**crontab**

```bash
0 2 * * 3 /home/ec2-user/cleanup_docker_images.sh
```

| Field        | Value | Meaning      |
| ------------ | ----- | ------------ |
| Minute       | 0     | At 0 minutes |
| Hour         | 2     | 2 AM         |
| Day of Month | *     | Every day    |
| Month        | *     | Every month  |
| Day of Week  | 3     | Wednesday    |


---

