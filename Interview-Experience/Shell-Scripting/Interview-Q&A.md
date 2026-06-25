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
