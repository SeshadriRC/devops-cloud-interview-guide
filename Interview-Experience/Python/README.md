# Python for DevOps Interview Preparation Guide

Modern DevOps and Site Reliability Engineering (SRE) roles frequently evaluate Python for automation scripting, API integrations, log analysis, and algorithmic problem solving.

---

## 📑 Core Resources & Problems

### 1. Algorithmic Coding Challenge
* **File**: [Python Scripting Challenge: Bank Locker Passcode](Python-Scripting.md)
* **Problem**: Given an integer $N$, find the smallest integer $P$ whose product of digits equals $N$ and $P \ge N$.
* **Algorithm**: Greedy single-digit factorization.
  - Divide $N$ by single-digit numbers from $9$ down to $2$ as many times as possible.
  - If $N$ reduces to $1$, sort the collected digits in ascending order to form the smallest multi-digit integer.
  - If $N > 1$ after checking all digits up to $9$, it contains prime factors $\ge 11$, meaning no single-digit product can equal $N$ (return -1 or handle edge cases).
* **Time Complexity**: $O(\log N)$ — extremely fast and optimal.

---

## 🛠️ Practical Hands-on Python Scenarios
* 📜 **Log File Processing & Counting**: [08-python-file-processing-error.md](../Practicals/08-python-file-processing-error.md)  
  *Downloading remote server logs via `requests`, parsing timestamped entries, and counting blocked IPs.*
* 📈 **Grafana API Automation**: [06-grafana-dashboard-cloudwatch-python.md](../Practicals/06-grafana-dashboard-cloudwatch-python.md)  
  *Automating monitoring dashboard creation using Python `requests` and the Grafana REST API.*

---

## 💡 Top Python DevOps Interview Questions

### 1. How do you handle AWS automation with Python?
* Use the **`boto3`** library.
* Best practices:
  - Never hardcode AWS credentials; use IAM Roles or environment variables.
  - Use `boto3.client` for low-level 1:1 API mapping or `boto3.resource` for object-oriented interaction.
  - Use **Paginators** (`client.get_paginator('list_objects_v2')`) when dealing with large collections (e.g., thousands of S3 objects or EC2 instances) to prevent incomplete results.

### 2. Error handling and retries in Python scripts
* Use `try...except...finally` blocks with specific exception classes (e.g., `ClientError`, `requests.exceptions.RequestException`) rather than catching generic `Exception`.
* Implement exponential backoff using `tenacity` or `urllib3.util.retry` for transient network/API failures.
