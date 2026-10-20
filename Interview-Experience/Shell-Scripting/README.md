# Shell Scripting Interview Preparation Guide

Shell scripting is essential for pipeline automation, system provisioning, log processing, and Linux administration. This guide covers scripting patterns and interview questions.

---

## 📑 Core Interview Topics

* **Q&A Guide**: [Shell Scripting Interview Q&A](Interview-Q&A.md)

### Key Questions Covered:
1. **Handling Spaces and Wildcards in Filenames**:
   - Always wrap variables in double quotes (`"$file"`) to prevent word splitting and globbing.
2. **Counting Matching Lines Safely**:
   - Using `grep -c "PATTERN" file.log` instead of `grep "PATTERN" file.log | wc -l` (avoids printing lines to stdout and pipeline overhead).
3. **Safe Temporary File Creation**:
   - Using `mktemp` (`temp_file=$(mktemp)`) to avoid race conditions and predictable file collision exploits in `/tmp`.
4. **Parsing Oracle / Application Errors**:
   - Grepping specific patterns like `ORA-[0-9]+` and extracting error codes.

---

## 🛡️ Production Bash Scripting Best Practices

### The Bash "Unofficial Strict Mode"
Always begin production shell scripts with:
```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'
```
* `-e`: Exit immediately if any command exits with a non-zero status.
* `-u`: Treat unset/uninitialized variables as an error and exit immediately.
* `-o pipefail`: Return the exit status of the first command in the pipeline that fails, rather than the exit status of the last command.
* `IFS`: Internal Field Separator set to newline and tab to prevent spaces in strings from breaking loop iterations.

---

## 🛠️ Related Practical Hands-On Challenges
* 📜 [Application Deployment with Helm & Shell Scripting](../Practicals/01-App-deployment-helm-shell-script.md)
