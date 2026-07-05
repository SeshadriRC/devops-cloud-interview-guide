In CI/CD, **Cold Gate** and **Soft Gate** are terms used to describe **quality gates** that determine whether a pipeline can continue. They are not official standards like "Shift Left" or "Blue-Green Deployment," but many organizations use them in their DevOps processes.

---

# Cold Gate (Hard Gate)

A **Cold Gate** (also called a **Hard Gate**) is a **mandatory checkpoint**.

If the gate fails:

* ❌ Pipeline stops immediately.
* ❌ Deployment is blocked.
* ❌ Developers must fix the issue before proceeding.

Think of it as:

> **"No Pass = No Deployment."**

### Example CI/CD Pipeline

```text
Git Push
    ↓
Build
    ↓
Unit Tests
    ↓
SonarQube Quality Gate ❌
    ↓
Pipeline Stops
```

The application will **not** be deployed.

### Examples of Cold Gates

* Unit test failures
* Build failures
* Critical security vulnerabilities
* Failed infrastructure validation
* Failed Terraform plan validation
* Docker image scan with Critical CVEs
* Failed compliance checks
* Missing approvals for Production deployment

---

# Soft Gate

A **Soft Gate** reports issues but **does not stop the pipeline**.

The pipeline continues while generating warnings or notifications.

Think of it as:

> **"Deployment is allowed, but you should fix these issues soon."**

### Example

```text
Git Push
    ↓
Build
    ↓
SonarQube Scan
    ↓
Code Coverage = 68%
    ↓
Warning Generated
    ↓
Deploy Continues
```

The deployment succeeds, but the team is informed about the lower code coverage.

### Examples of Soft Gates

* Low code coverage
* Medium or Low security vulnerabilities
* Coding style violations
* Documentation missing
* Technical debt exceeding a threshold
* Performance recommendations
* Best practice violations

---

# Real-Time Banking Project Example

Imagine you are deploying a banking application.

## Cold Gate

Pipeline:

```text
Developer Commit
        ↓
Build
        ↓
Unit Test
        ↓
Security Scan
        ↓
Critical Vulnerability Found
        ↓
Deployment Blocked
```

The security team requires that **no critical vulnerabilities** exist before production, so the deployment is stopped.

---

## Soft Gate

Pipeline:

```text
Developer Commit
        ↓
Build
        ↓
SonarQube
        ↓
Code Smells = 20
        ↓
Warning
        ↓
Deploy
```

The application still deploys because code smells are not considered severe enough to block the release.

---

# Jenkins Example

### Cold Gate

```groovy
stage('Quality Gate') {
    steps {
        waitForQualityGate abortPipeline: true
    }
}
```

If the quality gate fails, Jenkins aborts the pipeline.

---

### Soft Gate

```groovy
stage('Quality Gate') {
    steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
            sh 'run-quality-check.sh'
        }
    }
}
```

The stage becomes **UNSTABLE**, but the pipeline continues.

---

# Terraform Example

### Cold Gate

```text
terraform validate
```

If validation fails:

```text
Error
Pipeline Stops
```

---

### Soft Gate

```text
tflint
```

Output:

```text
Warning:
Unused Variable
Naming Convention
```

Pipeline continues while reporting the warnings.

---

# Docker Example

### Cold Gate

```text
Trivy Scan
```

Result:

```text
Critical CVE Found
```

Pipeline stops.

---

### Soft Gate

```text
Trivy Scan
```

Result:

```text
Low Severity CVEs
```

Pipeline continues, and the findings are reported.

---

# Difference Between Cold Gate and Soft Gate

| Feature           | Cold Gate (Hard Gate)                                          | Soft Gate                                   |
| ----------------- | -------------------------------------------------------------- | ------------------------------------------- |
| Blocks deployment | ✅ Yes                                                          | ❌ No                                        |
| Pipeline status   | Failed                                                         | Success/Unstable                            |
| Developer action  | Must fix immediately                                           | Can fix later                               |
| Suitable for      | Critical issues                                                | Minor issues                                |
| Examples          | Build failure, failed tests, critical security vulnerabilities | Code smells, low coverage, style violations |

---

## Interview Answer

> **A Cold Gate (Hard Gate)** is a mandatory quality check that blocks the CI/CD pipeline if it fails, such as failed unit tests or critical security vulnerabilities. **A Soft Gate** reports issues like low code coverage or code smells but allows the pipeline to continue, usually marking the build as unstable or generating warnings. Organizations typically configure these gates based on their quality and risk requirements.
