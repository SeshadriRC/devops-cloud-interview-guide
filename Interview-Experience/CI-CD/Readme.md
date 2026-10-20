# CI/CD & Jenkins Interview Preparation Guide

A comprehensive, production-tested guide to CI/CD pipelines, Jenkins architecture, release strategies, approval gates, and pipeline troubleshooting.

---

## 📑 Categorized Preparation Roadmap

### 1. Pipeline Architecture & Syntax
* 🚀 **Declarative Pipeline Design**: [Declarative Jenkins Pipeline Complete Flow](my-preparation/declarative-jenkins-pipeline.md)  
  *How CI/CD works end-to-end: checkout, test, SonarQube, Docker build, ECR push, and Helm deploy to Kubernetes/OpenShift.*
* ⚙️ **Reference Jenkinsfile**: [Sample Production Declarative Jenkinsfile](https://github.com/SeshadriRC/Jenkins-Zero-To-Hero/blob/main/java-maven-sonar-argocd-helm-k8s/spring-boot-app/JenkinsFile)

### 2. Quality Gates, Testing & Methodology
* 🛡️ **SonarQube Quality Gates**: [Steps Involved in SonarQube Quality Gates](my-preparation/sonarqube-quality.md)  
  *Code coverage, bugs, vulnerabilities, code smells, and pipeline breaking conditions.*
* ⏪ **Shift Left vs. Shift Right**: [Shift Left & Shift Right in DevOps](my-preparation/shift-left-shift-right.md)  
  *Testing early (SAST, unit tests, linting) vs. testing in production (canary, chaos engineering, APM monitoring).*
* 🧪 **User Acceptance Testing (UAT)**: [What is UAT and its Role in Release Cycles](my-preparation/UAT.md)  
  *Promotion criteria from Dev/QA to UAT and finally Production.*
* 🚪 **Quality Gates**: [Cold Gate vs. Soft Gate](my-preparation/cold-gate-soft-gate.md)  
  *Hard blocking gates vs. non-blocking warning gates.*

### 3. Release & Deployment Strategy
* 🏷️ **RC Tags Management**: [Deciding Which RC Tag Gets Deployed](my-preparation/RC-tags.md)  
  *Resolving conflicting developer Release Candidate tags and hotfixes.*
* 🚢 **Production Deployment Flow**: [Complete End-to-End Production Deployment Flow](my-preparation/RC-deployment-flow.md)  
  *CAB approval, change windows, blue/green rollout, smoke tests, and rollback procedures.*

### 4. Enterprise Approvals & Governance
* ✋ **Jenkins Manual Approvals**: [Configuring Approvals in Jenkins Pipelines](my-preparation/approval-process-jenkins.md)  
  *Using the `input` step with timeout and role-based approver restrictions.*
* 🐙 **GitHub Actions Approvals**: [Configuring Environment Protection in GitHub Actions](my-preparation/approval-process-git-actions.md)  
  *Required reviewers, environment secrets, and deployment branches.*

### 5. Security & Secrets Management
* 🔐 **Environment-Specific Vault Secrets**: [Configuring HashiCorp Vault per Jenkins Environment](my-preparation/environment-secrets.md)  
  *Dynamic secrets injection for DEV, QA, and PROD using HashiCorp Vault.*
* 🙈 **Preventing Secret Leaks**: [Masking Sensitive Information in Pipeline Logs](my-preparation/Sensitive-information.md)  
  *Handling credentials securely, using Jenkins credentials binding, and cleaning leaked secrets.*

### 6. Troubleshooting CI/CD Failures
* 🩺 **Pipeline Failure Troubleshooting**: [Sudden Jenkins Pipeline Failures Troubleshooting Guide](my-preparation/pipeline-not-working.md)  
  *Step-by-step resolution: Jenkins master/agent disk space, webhooks, plugin upgrades, Docker daemon, git credentials.*
* 📋 **End-to-End Failure Points Guide**: [Common CI/CD Failure Points and Fixes](Interview-Q&A.md)  
  *Comprehensive breakdown covering Git, Build, Test, Deploy, Security, and Rollback errors.*
