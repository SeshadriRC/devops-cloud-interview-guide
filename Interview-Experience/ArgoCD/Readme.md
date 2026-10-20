# ArgoCD & GitOps Interview Preparation Guide

ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. This guide provides comprehensive answers to frequently asked ArgoCD architecture and GitOps interview questions.

---

## 📑 Table of Contents
1. [ArgoCD Architecture Components](#1-argocd-architecture-components)
   - [Application Controller](#application-controller)
   - [Dex Controller / Server](#dex-controller--server)
   - [Repo Server](#repo-server)
   - [API Server](#api-server)
   - [Argo CD Image Updater](#argo-cd-image-updater)
2. [How CI/CD Interacts with ArgoCD](#2-how-cicd-interacts-with-argocd)
3. [Argo CD Image Updater Workflow](#3-argo-cd-image-updater-workflow)
4. [Push vs. Pull Deployment Models](#4-push-vs-pull-deployment-models)
5. [Common ArgoCD Interview Questions](#5-common-argocd-interview-questions)

---

## 1. ArgoCD Architecture Components

```text
                  ┌──────────────────────────────────────────────┐
                  │                 User / Web UI                │
                  └──────────────────────┬───────────────────────┘
                                         │
                                         ▼
                 ┌────────────────────────────────────────────────┐
                 │               Argo CD API Server               │
                 │   - Authenticates via Dex (SSO / OIDC)         │
                 │   - Enforces RBAC permissions                  │
                 └───────────────┬────────────────┬───────────────┘
                                 │                │
                                 ▼                ▼
┌─────────────────────────────────┐              ┌────────────────────────────────┐
│       Argo CD Repo Server       │              │  Argo CD Application Controller│
│  - Clones Git repository        │              │  - Watches CRDs (Application)  │
│  - Renders Helm / Kustomize     │              │  - Compares Desired vs Live    │
│  - Generates pure K8s manifests │              │  - Executes Sync / Rollbacks   │
└─────────────────────────────────┘              └───────────────┬────────────────┘
                                                                 │
                                                                 ▼
                                                  ┌───────────────────────────────┐
                                                  │       Kubernetes Cluster      │
                                                  │ (Deployments, Services, Pods) │
                                                  └───────────────────────────────┘
```

### Application Controller
* **Role**: The core brain of ArgoCD.
* Runs as a Kubernetes controller that continuously compares the **desired state** defined in the Git repository with the **live cluster state** in Kubernetes.
* Detects **`OutOfSync`** or **`Degraded`** states.
* Executes sync actions (either automatically or manually triggered) to bring live infrastructure to match Git.

### Dex Controller / Server
* **Role**: Embedded identity provider and authentication broker.
* Enables Single Sign-On (SSO) integration with external enterprise identity providers:
  - OpenID Connect (OIDC) / Okta / Azure AD / Keycloak
  - GitHub / GitLab / Bitbucket
  - SAML 2.0 / LDAP
* Intercepts login requests from the ArgoCD UI/CLI, delegates authentication to the configured identity provider, and maps user groups to ArgoCD RBAC roles (`argocd-rbac-cm`).

### Repo Server
* **Role**: Internal service dedicated to parsing and rendering manifests.
* Maintains a local cache of Git repositories.
* Takes raw source code (Helm charts, Kustomize overlays, Jsonnet, or vanilla YAML files) and renders them into pure Kubernetes manifests.
* Sends the rendered manifests to the Application Controller for diffing and application.

### API Server
* **Role**: Front-facing gRPC and REST API gateway.
* Powers the ArgoCD Web UI, CLI (`argocd`), and third-party webhooks.
* Handles user authentication, token generation, credential verification, and RBAC authorization.

### Argo CD Image Updater
* **Role**: Standalone controller that automates container image updates.
* Tracks container image registries (DockerHub, AWS ECR, Harbor, Quay, etc.).
* When a new container image tag matching a specified constraint (e.g., `semver`, `latest`, `digest`, or `regex`) is published, Image Updater updates the target deployment.
* Can write changes back to Git (commit & push) or update the ArgoCD Application parameters directly.

---

## 2. How CI/CD Interacts with ArgoCD

In a modern GitOps pipeline, CI and CD are strictly separated:

```text
[ Developer ] ──Push Code──► [ App Git Repo ]
                                   │
                                   ▼
                       [ CI: Jenkins / GitHub Actions ]
                         ├── Run Unit & Integration Tests
                         ├── Build Docker Image
                         ├── Scan Image (Trivy / Snyk)
                         └── Push Image to Registry (ECR / Harbor)
                                   │
         ┌─────────────────────────┴─────────────────────────┐
         ▼ Option A (Git Commit)                             ▼ Option B (Image Updater)
[ CI updates Helm values.yaml ]                 [ ArgoCD Image Updater polls ECR ]
         │                                                   │
         ▼                                                   ▼
[ Manifest Git Repo (Config Repo) ] ◄──────────── [ Commits new tag to Git ]
         │
         ▼ (Webhook trigger or 3-minute poll)
[ ArgoCD Application Controller reconciles & deploys to Kubernetes ]
```

---

## 3. Argo CD Image Updater Workflow

### Interview Question
> *"How do you know the CI pipeline has finished and a new image is ready? How does ArgoCD deploy it?"*

### Mechanisms:

#### Approach 1: CI Commits Directly to Config Git Repo (Recommended)
1. CI pipeline completes build and pushes container image: `myrepo/app:v1.2.3`.
2. CI pipeline executes a `git commit` to the Kubernetes configuration Git repository updating `values.yaml` (e.g. `image.tag: v1.2.3`).
3. CI invokes an ArgoCD webhook (`curl https://argocd.example.com/api/webhook`) to instantly notify ArgoCD without waiting for polling.
4. ArgoCD syncs the application immediately.

#### Approach 2: Argo CD Image Updater (Automated Registry Poller)
1. Image Updater runs as a pod inside the cluster.
2. Annotations on the ArgoCD Application define which image to watch and the update strategy:
   ```yaml
   argocd-image-updater.argoproj.io/image-list: my-app=myrepo/app:v*
   argocd-image-updater.argoproj.io/my-app.update-strategy: semver
   argocd-image-updater.argoproj.io/write-back-method: git
   ```
3. When the CI pipeline finishes and pushes `v1.2.4`, Image Updater detects the new image within its polling interval (default 2 minutes).
4. Image Updater writes a Git commit back to the repository (`git write-back`), updating the manifest.
5. ArgoCD reconciles the new commit and updates the Kubernetes pods.

---

## 4. Push vs. Pull Deployment Models

| Comparison Criteria | Push Model (e.g., Jenkins / GitLab CI) | Pull Model (GitOps with ArgoCD) |
| :--- | :--- | :--- |
| **Cluster Access** | CI server requires admin `kubeconfig` and cluster credentials | No credentials stored outside; ArgoCD agent pulls from inside cluster |
| **Security Risk** | High: CI server compromise grants full cluster access | Low: Least privilege; outbound HTTPS to Git/Registry only |
| **Configuration Drift**| Manual cluster modifications are not auto-repaired | Auto-heals: ArgoCD detects and reverts manual cluster tampering |
| **Audit & Rollback** | Depends on CI history | Every cluster change is an explicit Git commit; `git revert` rolls back |

---

## 5. Common ArgoCD Interview Questions

### Q1: What is the difference between ArgoCD Sync and Refresh?
* **Refresh**: Compares the latest Git commit against the current live cluster state and updates the UI status (OutOfSync / Synced). Does NOT modify any cluster resources.
* **Sync**: Actually executes `kubectl apply` / `helm install` to reconcile and change cluster resources to match Git.

### Q2: What are Sync Waves and Resource Hooks?
* **Sync Waves**: Ordered deployment using annotations (`argocd.argoproj.io/sync-wave: "1"`). Waves are applied in ascending order; wave 1 waits for wave 0 resources to become healthy before starting.
* **Resource Hooks**: Execute specific scripts or jobs during synchronization phases:
  - `PreSync`: Run database schema migrations before updating deployments.
  - `Sync`: Main application deployment.
  - `PostSync`: Run integration or smoke tests, send Slack notifications.
  - `SyncFail`: Cleanup or trigger alert if sync fails.

### Q3: What happens if someone modifies a Kubernetes Deployment directly using `kubectl edit`?
* If **Self-Heal (`selfHeal: true`)** is enabled: ArgoCD immediately detects drift and overwrites the manual changes with the state from Git.
* If Self-Heal is disabled: ArgoCD marks the application as **`OutOfSync`** and highlights the diff in the UI until manual sync is approved.
