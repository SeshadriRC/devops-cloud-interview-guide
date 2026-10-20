# Helm Interview Preparation Guide

Helm is the standard package manager for Kubernetes. This guide provides structured resources on chart architecture, rendering sequence, and common interview questions.

---

## 📑 Core Topics & Study Guides

### 1. [Helm Chart Architecture & Structure](my-preparation/structure-helm.md)
* Anatomy of a Helm chart (`Chart.yaml`, `values.yaml`, `templates/`, `_helpers.tpl`).
* Go template syntax, built-in objects (`.Values`, `.Release`, `.Chart`), and functions (`toYaml`, `nindent`, `include`).
* Multi-environment production patterns (`values-dev.yaml`, `values-prod.yaml`).
* Essential CLI commands (`lint`, `template`, `upgrade --install`, `rollback`, `history`).

### 2. [Helm Resource Installation Order](my-preparation/helm-apply-order.md)
* Does Helm apply resources in a specific order? (Yes, built-in dependency sorting).
* Installation order: Namespaces, CRDs, ServiceAccounts, Secrets, ConfigMaps, PVCs, Deployments, Services, Ingress.
* Using Helm Hooks (`pre-install`, `post-install`, `pre-upgrade`, `post-upgrade`, `test`) to customize execution flow.

---

## 🛠️ Practical Hands-on Scenarios
* [Deploy Web App to AWS EKS with Helm](../Practicals/04-EKS-deployment-Helm.md)
* [Application Deployment with Helm & Shell Scripting](../Practicals/01-App-deployment-helm-shell-script.md)
