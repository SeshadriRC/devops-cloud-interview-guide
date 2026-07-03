This is one of the most common **DevOps interview scenario questions**. The interviewer wants to evaluate your **systematic troubleshooting approach**, not just Jenkins knowledge.

A good answer is to troubleshoot layer by layer: **Jenkins → SCM → Build → Dependencies → Infrastructure → Deployment**.

---

# Interview Answer

> "If a Jenkins pipeline has been working for a long time and suddenly starts failing, I would first identify what changed. Since it was working previously, I would check recent code changes, infrastructure changes, plugin updates, credentials, and external dependencies before assuming the pipeline itself is faulty."

---

# Step 1: Identify the Failure Stage

Check which stage failed.

Example Pipeline:

```text
Checkout
    ↓
Build
    ↓
Unit Test
    ↓
Docker Build
    ↓
Push Image
    ↓
Deploy
```

Determine the exact stage where the failure occurs.

Example:

```text
✓ Checkout

✓ Build

✗ Unit Test Failed
```

or

```text
✓ Checkout

✗ Docker Build Failed
```

This narrows down the investigation.

---

# Step 2: Check Jenkins Console Output

The first place to look is the **Console Output**.

Look for errors like:

```text
Permission denied
```

```text
Git authentication failed
```

```text
Disk space exceeded
```

```text
OutOfMemoryError
```

```text
Docker daemon not running
```

```text
Maven dependency download failed
```

The logs usually provide the root cause.

---

# Step 3: Compare with the Last Successful Build

Compare:

* Jenkinsfile
* Build parameters
* Git commit
* Environment variables

Questions to ask:

* Did someone modify the Jenkinsfile?
* Was a new branch merged?
* Were new credentials introduced?

---

# Step 4: Check Source Code Repository

Verify:

* Latest commits
* Branch changes
* Merge conflicts

Example:

```bash
git log --oneline
```

If the pipeline started failing immediately after a merge, review that commit.

---

# Step 5: Verify Jenkins Agent

Ensure the build agent is healthy.

```text
Manage Jenkins
    ↓
Nodes
```

Check:

* Agent is online
* No disk issues
* No memory issues
* Correct labels

If using Kubernetes agents:

```bash
kubectl get pods -n jenkins
```

Verify the agent pod is running.

---

# Step 6: Check Disk Space

A common production issue.

Linux:

```bash
df -h
```

Also check:

```bash
du -sh /var/lib/jenkins/*
```

If the disk is full, builds may fail due to inability to write workspaces or logs.

---

# Step 7: Check Credentials

Verify:

* Git credentials
* Docker registry credentials
* Cloud credentials
* Kubernetes credentials

Example errors:

```text
Authentication failed
```

```text
Access denied
```

```text
401 Unauthorized
```

Check whether credentials have expired or were modified.

---

# Step 8: Verify External Dependencies

Pipelines often depend on external services.

Examples:

* GitHub/GitLab
* Docker Registry
* Maven Repository
* Nexus
* SonarQube
* Kubernetes Cluster

Example:

```text
Connection timed out
```

or

```text
503 Service Unavailable
```

Check connectivity:

```bash
curl
ping
nslookup
```

---

# Step 9: Check Plugin Updates

Ask:

* Was Jenkins upgraded?
* Were plugins upgraded recently?

Some plugin updates introduce compatibility issues.

Check:

```text
Manage Jenkins
↓

Plugin Manager
```

---

# Step 10: Verify Environment Variables

A missing variable can break the build.

Example:

```text
JAVA_HOME

MAVEN_HOME

DOCKER_HOST

KUBECONFIG
```

Verify:

```bash
printenv
```

or in Jenkins:

```groovy
echo env.JAVA_HOME
```

---

# Step 11: Check Tool Availability

Ensure required tools exist on the agent.

Example:

```bash
java -version

mvn -version

docker version

kubectl version --client
```

Sometimes the agent image changes and required tools are missing.

---

# Step 12: Check Infrastructure

If the deployment stage fails:

Verify

```bash
kubectl get nodes

kubectl get pods
```

If deploying to AWS:

Check

* IAM permissions
* Cluster health
* Network connectivity

---

# Step 13: Check Recent Infrastructure Changes

Ask questions like:

* Was Jenkins upgraded?
* Was the agent image changed?
* Was the Kubernetes cluster upgraded?
* Were firewall rules modified?
* Were certificates renewed?

Infrastructure changes often affect previously stable pipelines.

---

# Step 14: Reproduce the Issue

Run the failing command manually on the Jenkins agent.

Example:

```bash
mvn clean install
```

or

```bash
docker build .
```

This helps determine whether the issue is with Jenkins or the underlying command.

---

# Common Root Causes

| Issue                             | How to Verify                                     |
| --------------------------------- | ------------------------------------------------- |
| Code changes                      | Compare with last successful build                |
| Jenkinsfile changes               | Review SCM history                                |
| Git authentication failure        | Check credentials and console logs                |
| Expired credentials               | Verify Jenkins credentials                        |
| Disk full                         | `df -h`                                           |
| Agent offline                     | Jenkins Nodes / Kubernetes pods                   |
| Missing tools                     | `java -version`, `mvn -version`, `docker version` |
| Plugin incompatibility            | Plugin Manager                                    |
| Dependency repository unavailable | Test connectivity to Nexus/Maven                  |
| Kubernetes deployment failure     | `kubectl get events`, `kubectl describe pod`      |
| Docker daemon issue               | `docker ps` / `docker version`                    |
| Network or firewall issue         | `ping`, `curl`, `nslookup`                        |

---

# Real-Time Example

Suppose the pipeline fails during the Docker build stage.

Console output:

```text
Cannot connect to the Docker daemon at unix:///var/run/docker.sock
```

Investigation:

* Check whether Docker is running:

```bash
systemctl status docker
```

* Verify Jenkins user permissions:

```bash
groups jenkins
```

* Ensure the Docker socket exists:

```bash
ls -l /var/run/docker.sock
```

Root cause: Docker service stopped on the build agent.

---

# Interview Answer (2–3 minutes)

> "My first step would be to identify the stage where the pipeline fails and examine the Jenkins console logs for the exact error. Since the pipeline worked previously, I'd compare it with the last successful build to identify any recent code, Jenkinsfile, or configuration changes. Next, I'd verify the health of the Jenkins agent, including disk space, CPU, memory, and tool availability. I'd also check credentials for Git, Docker registries, cloud providers, or Kubernetes clusters to ensure they haven't expired. If the pipeline depends on external services like Git, Nexus, SonarQube, or Kubernetes, I'd verify their availability and connectivity. Finally, I'd review any recent Jenkins or plugin upgrades and, if necessary, reproduce the failing command directly on the build agent to determine whether the issue is with Jenkins or the underlying infrastructure. This systematic approach helps isolate the root cause quickly."


---

