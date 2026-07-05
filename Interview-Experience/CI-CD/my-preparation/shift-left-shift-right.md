**Shift Left** and **Shift Right** are software development and DevOps approaches that describe **when** certain activities happen during the SDLC (Software Development Life Cycle).

Think of the SDLC timeline like this:

```text
Planning → Coding → Build → Test → Deploy → Production → Monitoring
              ↑ Shift Left                     ↑ Shift Right
```

---

# Shift Left

Shift Left means **moving testing, security, and quality checks earlier** in the CI/CD pipeline, before the application reaches production.

The goal is:

> **Find problems as early as possible because fixing them is cheaper and easier.**

## Traditional approach

```text
Developer writes code
        ↓
Build
        ↓
Deploy
        ↓
Tester finds bug
```

The developer has already forgotten the code, making fixes slower.

---

## Shift Left approach

```text
Developer writes code
        ↓
Unit Test
        ↓
Static Code Analysis
        ↓
Security Scan
        ↓
Build
        ↓
Deploy
```

Problems are detected immediately.

---

## Example CI/CD Pipeline

```text
Git Push
    ↓
Jenkins
    ↓
Compile
    ↓
Run Unit Tests
    ↓
SonarQube Scan
    ↓
Check Code Coverage
    ↓
Dependency Scan
    ↓
Docker Build
    ↓
Deploy
```

Everything before deployment is Shift Left.

---

## Activities included in Shift Left

* Unit Testing
* Code Reviews
* Static Code Analysis (SonarQube)
* Security Scanning
* Dependency Scanning
* Secret Scanning
* Infrastructure as Code Validation
* Linting
* Code Formatting
* Integration Testing

---

## Real-time example

Suppose your Java application has this code:

```java
int result = 10 / 0;
```

Without Shift Left:

```
Developer commits
        ↓
Application deployed
        ↓
Production crashes
```

With Shift Left:

```
Developer commits
        ↓
Unit tests fail
        ↓
Build fails
        ↓
Developer fixes immediately
```

---

# Shift Right

Shift Right means **performing validation, monitoring, testing, and optimization after deployment**, especially in production or production-like environments.

The goal is:

> **Observe how the application behaves with real users and real workloads.**

---

## Example

```text
Deploy Application
        ↓
Real Users
        ↓
Logs
        ↓
Metrics
        ↓
Tracing
        ↓
Alerts
        ↓
Performance Monitoring
```

---

## Activities included in Shift Right

* Application Monitoring
* Log Analysis
* Distributed Tracing
* APM (Application Performance Monitoring)
* Chaos Engineering
* Load Testing in Production
* Canary Deployments
* Blue-Green Deployments
* Feature Flags
* Auto Scaling
* Incident Response

---

# Real-time example

Imagine an e-commerce website.

You deploy Version 2.

Instead of sending all users to the new version:

```
10000 Users

↓

5% → New Version
95% → Old Version
```

You monitor:

* CPU
* Memory
* Error Rate
* Response Time
* Database Connections

If everything is healthy:

```
25%
↓

50%
↓

100%
```

This is Shift Right because you're validating the application after deployment.

---

# CI/CD Pipeline Example

```text
Developer Pushes Code
          │
          ▼
GitHub
          │
          ▼
Jenkins Pipeline
          │
          ▼
Build
          │
          ▼
Unit Tests
          │
          ▼
SonarQube Scan
          │
          ▼
Dependency Scan
          │
          ▼
Docker Image Build
          │
          ▼
Container Scan
          │
          ▼
Deploy to Dev
          │
          ▼
Integration Tests
          │
          ▼
Deploy to UAT
          │
          ▼
Deploy to Production
          │
          ▼
Prometheus Monitoring
          │
          ▼
Grafana Dashboards
          │
          ▼
Alertmanager
          │
          ▼
Incident Response
```

**Shift Left** includes:

* Build
* Unit Tests
* SonarQube
* Security Scans
* Integration Tests

**Shift Right** includes:

* Monitoring
* Logging
* Metrics
* Tracing
* Alerts
* Performance Analysis

---

# Interview Scenario

Suppose your team manages a banking application.

### Shift Left

A developer commits code to Git.

The Jenkins pipeline automatically:

1. Compiles the application.
2. Runs unit tests.
3. Executes SonarQube quality checks.
4. Scans dependencies for known vulnerabilities.
5. Builds the Docker image only if all checks pass.

This prevents defective or vulnerable code from reaching production.

### Shift Right

After deployment:

* Prometheus collects CPU, memory, and application metrics.
* Grafana dashboards display service health.
* Logs are centralized for troubleshooting.
* Alerts notify the on-call team if error rates or latency exceed thresholds.
* A canary deployment exposes the new version to 10% of users first; if metrics remain healthy, traffic is gradually increased to 100%.

---

# Easy way to remember

| Shift Left        | Shift Right                         |
| ----------------- | ----------------------------------- |
| Before deployment | After deployment                    |
| Prevent defects   | Detect issues in real usage         |
| Focus on quality  | Focus on reliability and operations |
| Unit testing      | Monitoring                          |
| Security scanning | Alerting                            |
| SonarQube         | Prometheus/Grafana                  |
| Code reviews      | Log analysis                        |
| CI pipeline       | CD/Production operations            |

### One-line interview answer

> **Shift Left** means moving testing, security, and quality checks earlier in the CI/CD pipeline so defects are caught before deployment. **Shift Right** means validating and improving the application after deployment through monitoring, logging, observability, canary releases, and real-world performance analysis to ensure reliability in production.

