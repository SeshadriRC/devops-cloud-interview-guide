# DevOps Hands-on Practical Interview Challenges

This folder contains real-world hands-on coding, scripting, and infrastructure scenarios given during live technical screens and take-home DevOps assignments.

---

## 📑 Practical Scenarios Index

| # | Practical Challenge | Tech Stack / Tools | Skills Evaluated |
| :-: | :--- | :--- | :--- |
| **01** | [App Deployment with Helm & Shell Script](01-App-deployment-helm-shell-script.md) | Bash, Docker, Helm, Kubernetes | Shell loops, environment variables, containerization, Helm charts |
| **02** | [Deploy Containerized Flask App & Terraform EC2](02-Deploy-containerize-Flask-app.md) | Python, Docker, Kubernetes, Terraform | End-to-end deployment, Dockerfile, k8s Deployment & Service, Terraform EC2 |
| **03** | [Node.js CI/CD to AWS Elastic Beanstalk](03-deploy-node-app-elasticbeanstalk.md) | GitHub Actions, Node.js, AWS CLI, Elastic Beanstalk | GitHub Actions workflow, unit testing, AWS credentials & deployment |
| **04** | [AWS EKS Deployment with Helm](04-EKS-deployment-Helm.md) | Kubernetes, AWS EKS, Helm | Helm chart authoring, parameterized values, microservice deployment |
| **05** | [Datadog Custom Metric Monitoring Script](05-Datadog-monitoring-script-metrics.md) | Node.js, Datadog API, AWS EKS | REST API integration, metric instrumentation, error handling |
| **06** | [Automate Grafana CloudWatch Dashboards](06-grafana-dashboard-cloudwatch-python.md) | Python, Grafana API, AWS CloudWatch | Python `requests`, JSON dashboard definitions, CloudWatch data source |
| **07** | [AWS CloudWatch Alarm with CloudFormation](07-AWS-CloudWatch-Alarm-Configuration.md) | AWS CloudFormation, CloudWatch, SNS | Infrastructure-as-code, EC2 CPU alarms, SNS topic notifications |
| **08** | [Python Web Server Log File Processing](08-python-file-processing-error.md) | Python, HTTP `requests` | Downloading remote logs, string splitting, counting blocked IPs |

---

## 🎯 Tips for Acing Hands-On Live Coding Interviews

1. **Clarify Requirements First**:
   - Ask about environment constraints (Python version, CLI versions, allowed libraries).
   - Check error handling expectations (should the script fail fast or log warnings?).

2. **Structure Clean Code**:
   - For Dockerfiles: Use multi-stage builds and non-root users where appropriate.
   - For Kubernetes: Always specify resource requests and limits.
   - For Shell Scripts: Use `set -euo pipefail` and quote variables.
   - For Python: Structure with functions, `try...except`, and type hints.

3. **Validate & Test**:
   - Run linters (`helm lint`, `terraform validate`, `shellcheck`, `flake8`).
   - Test edge cases (missing environment variables, network timeouts, invalid inputs).
