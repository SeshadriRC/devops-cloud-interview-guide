This is a **real-world DevOps release management** interview question. The interviewer wants to know whether you understand **release governance**, not just Jenkins or Git.

The key point is:

> **Developers do not decide what goes to production. The approved Release Candidate (RC) is selected through the release management process.**

---

# Interview Answer

> "If multiple developers create different RC tags, we don't deploy them arbitrarily. The Release Manager or Product Owner identifies the approved RC based on successful testing and business approval. Only that approved tag is deployed to production."

---

## Example

Suppose there are multiple RC tags:

```text
v2.5.0-rc1
v2.5.0-rc2
v2.5.0-rc3
v2.5.0-rc4
```

* `rc1` – Failed integration testing.
* `rc2` – Critical bug found during UAT.
* `rc3` – Passed testing but missing a required feature.
* `rc4` – Passed all tests and received business approval.

The production deployment uses:

```text
v2.5.0-rc4
```

or it may be promoted to the final release tag:

```text
v2.5.0
```

---

# How is the RC selected?

Typical approval flow:

```text
Developer
      │
      ▼
Create RC Tag
      │
      ▼
CI Pipeline
      │
      ▼
Build & Unit Tests
      │
      ▼
QA Testing
      │
      ▼
Integration Testing
      │
      ▼
UAT
      │
      ▼
Business Approval
      │
      ▼
Release Manager Approves RC
      │
      ▼
Production Deployment
```

---

# How is this handled in Jenkins?

The production pipeline usually **does not automatically deploy the latest RC tag**.

Instead, it accepts a parameter.

Example:

```groovy
parameters {
    string(name: 'RELEASE_TAG',
           defaultValue: 'v2.5.0-rc4',
           description: 'Release tag to deploy')
}
```

During deployment:

```bash
git checkout ${RELEASE_TAG}
```

or

```bash
docker pull registry/app:${RELEASE_TAG}
```

This ensures only the approved RC is deployed.

---

# In a GitOps Environment

If using **Argo CD**, the process is:

1. Jenkins builds the image.
2. Image is tagged:

```text
app:v2.5.0-rc4
```

3. The GitOps repository is updated:

```yaml
image:
  tag: v2.5.0-rc4
```

4. Argo CD detects the change.
5. Argo CD deploys that specific image.

Again, only the approved RC tag is committed to the GitOps repository.

---

# Best Practices

* Never deploy the "latest" tag to production.
* Use immutable version tags (for example, `v2.5.0-rc4`).
* Require QA/UAT and business approval before production.
* Keep production deployments parameterized or GitOps-controlled.
* Maintain rollback capability by preserving previous release tags.

---

# Interview Answer (2 minutes)

> "When multiple RC tags exist, the latest tag isn't necessarily deployed. Each RC goes through automated testing, integration testing, QA, and UAT. The Release Manager or Product Owner identifies the RC that has passed all required validations and is approved for production. In Jenkins, the production pipeline is parameterized to deploy a specific release tag, or, in a GitOps setup, the approved image tag is committed to the GitOps repository and Argo CD deploys that version. This ensures production receives only the tested and approved release, while also making rollbacks straightforward because every deployment uses immutable version tags."
