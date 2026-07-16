In **GitHub Actions**, there isn't a `input` step like Jenkins' `input` for manual approval. The recommended way is to use **Environments with Required Reviewers**.

## Method 1 (Recommended): GitHub Environment Approval

### Step 1: Create an Environment

Go to your repository:

```
Settings
   ↓
Environments
   ↓
New Environment
```

Example:

```
Environment Name

production
```

---

### Step 2: Add Required Reviewers

Inside the environment:

```
Protection Rules

✔ Required reviewers

Add yourself or your DevOps Team
```

For example:

```
Required Reviewers

SeshadriRC
```

---

### Step 3: Update your workflow

Instead of

```yaml
updatek8s:
  runs-on: ubuntu-latest
```

write

```yaml
updatek8s:
  runs-on: ubuntu-latest

  environment:
    name: production

  needs:
    - build
    - unit-tests
    - code-quality
    - docker
```

That's all.

---

When the workflow reaches this job, GitHub pauses it.

Pipeline flow becomes

```
Build
   │
Unit Test
   │
Code Quality
   │
Docker Build
   │
──────────────
Waiting for Approval
──────────────
   │
Approve
   │
Update deploy.yaml
   │
Git Push
```

The UI will show:

```
Waiting for review...

Approve and Deploy
```

Only after someone approves will the `updatek8s` job execute.

---

## Method 2: Separate Deploy Workflow (Common in GitOps)

Instead of deploying automatically, split the process into two workflows:

### Workflow 1

```
CI

Build
↓

Test

↓

Docker Push

↓

Update Manifest PR
```

### Workflow 2

Triggered only after the manifest PR is merged:

```
Deploy

↓

ArgoCD Sync

↓

Deployment
```

The PR approval itself acts as the manual approval.

This is the preferred approach in many GitOps environments.

---

## Method 3: workflow_dispatch (Manual Trigger)

Another option is to trigger deployment manually.

```yaml
on:
  workflow_dispatch:
```

Then click:

```
Actions

↓

Deploy

↓

Run Workflow
```

This requires someone to manually start the deployment.

---

# How it looks in a real company

For a typical DEV → SIT → UAT → PROD flow:

```
Developer
    │
    ▼
Git Push
    │
    ▼
PR Pipeline
(Build + Test + Sonar)
    │
    ▼
Merge
    │
    ▼
Main Branch
    │
    ▼
Docker Build
    │
    ▼
Push Image
    │
    ▼
Update Helm/Manifest
    │
    ▼
DEV Deployment (Automatic)
    │
    ▼
SIT Deployment (Automatic)
    │
    ▼
───────────────
UAT Approval
───────────────
    │
    ▼
UAT Deployment
    │
    ▼
───────────────
Production Approval
───────────────
    │
    ▼
Production Deployment
```

For interview purposes, if asked **"How do you implement manual approval in GitHub Actions?"**, a strong answer is:

> "GitHub Actions doesn't have a built-in approval step like Jenkins. The standard approach is to use GitHub Environments with Required Reviewers. I assign the deployment job to an environment such as `production`. When the workflow reaches that job, it pauses until an authorized reviewer approves it. This provides a secure approval gate before deploying to higher environments."
