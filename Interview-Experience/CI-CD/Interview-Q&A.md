This is a common **DevOps interview** question. They usually expect **real troubleshooting scenarios + prevention methods**, not just definitions.

**Question:** *What are the common failure points in a CI/CD pipeline and how do you address them?*

You can answer like this:

---

### 1. Source Code / Git Issues

**Failure points:**

* Wrong branch selected
* Merge conflicts
* Missing code changes
* Permission issues

**How to address:**

* Branch protection rules
* Pull request approvals
* Git hooks
* Verify commit/branch before trigger

Example:

```text
Jenkins triggered from wrong branch → fixed webhook and branch configuration
```

---

### 2. Build Failures

**Failure points:**

* Dependency missing
* Package version mismatch
* Syntax errors
* Maven/NPM/Python build failures

**How to address:**

* Check build logs
* Lock dependency versions
* Use artifact repositories
* Validate locally before commit

Commands:

```bash
mvn clean install
npm install
pip install -r requirements.txt
```

---

### 3. CI Tool Failures (Jenkins / GitLab)

**Failure points:**

* Agent unavailable
* Executor full
* Pipeline timeout
* Plugin failures

**How to address:**

* Monitor worker nodes
* Scale agents
* Configure retry logic
* Upgrade plugins

Example:

```text
Jenkins build stuck → agent offline → restart agent
```

---

### 4. Infrastructure Provisioning Failures

**Failure points:**

* Terraform/Ansible errors
* Missing permissions
* Resource quota exceeded

**How to address:**

* Validate IaC before apply
* Use dry-run

Commands:

```bash
terraform plan
ansible-playbook --check
```

---

### 5. Container Build Failures

**Failure points:**

* Dockerfile syntax issues
* Image pull errors
* Registry authentication issues

**How to address:**

* Validate Dockerfile
* Verify registry credentials
* Check image tags

Commands:

```bash
docker build .
docker login
```

---

### 6. Kubernetes / OpenShift Deployment Failures

(Useful for your profile)

**Failure points:**

* Pod crash
* ImagePullBackOff
* Wrong values.yaml
* Resource limits exceeded
* Failed rollout

**How to address:**

Commands:

```bash
oc get pods
oc describe pod <pod>

kubectl rollout status deployment/app

helm status
helm history
```

Example:

```text
Helm upgrade failed due to incorrect values → rollback and redeploy
```

---

### 7. Secret / Configuration Issues

**Failure points:**

* Expired secrets
* Wrong environment variables
* Vault secret rotation issues

**How to address:**

* Validate secret injection
* Use secret management
* Automate credential rotation

Example:

```text
Application failed after secret rotation because old credentials were cached
```

---

### 8. Database Migration Failures

**Failure points:**

* Schema mismatch
* Connection issues
* Migration script failure

**How to address:**

* Run migration separately
* Backup before deployment
* Add rollback plan

---

### 9. Network / Firewall Issues

**Failure points:**

* DNS failure
* Firewall blocks
* Service unreachable

**How to address:**

Commands:

```bash
nslookup
curl
telnet
nc
```

---

### 10. Monitoring & Rollback Issues

**Failure points:**

* Deployment succeeds but application unhealthy

**How to address:**

* Health checks
* Alerts
* Automated rollback

Commands:

```bash
kubectl rollout undo
helm rollback
```

---

### Interview answer (2–3 minutes):

> “Common CI/CD failure points include source code conflicts, build failures, Jenkins agent issues, container image problems, deployment failures in OpenShift/Kubernetes, secret misconfiguration, database migration failures, and infrastructure provisioning issues. My approach is to isolate the failed stage, analyze logs, validate dependencies, verify credentials and environment variables, and use rollback mechanisms such as Helm rollback or deployment rollback to restore service quickly.”
