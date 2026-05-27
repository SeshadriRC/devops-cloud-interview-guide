## What is the Rollback Strategy You Follow in Your Organization?

### Question  
Explain how your team handles rollbacks when a deployment goes wrong. What mechanisms or tools are in place to revert a release safely?

### Short explanation of the question  
This question evaluates your team's preparedness and response to failed deployments — especially how you **recover quickly** while minimizing downtime and user impact.

---

### Answer  
We follow an automated **rollback strategy** integrated with our CI/CD system (GitHub Actions + Argo CD). Rollbacks are triggered either **manually via Git revert** or **automatically** if health checks fail during canary or rolling deployments. The GitOps model makes rollbacks clean and reproducible.

---

### Detailed explanation of the answer for readers’ understanding

---

### 🔁 GitOps Rollback (Primary Method)

We manage Kubernetes deployments through Git using **Argo CD**.

- **Deployments are version-controlled** as YAML in Git.
- If a deployment breaks production, we simply revert the Git commit.
- Argo CD syncs the previous state back to the cluster.

```bash
git revert <bad-commit>
git push origin main
```

Argo CD picks up the change and rolls back automatically.

#### ✅ Pros:
- Fully auditable
- Consistent rollback to known good state
- Git history = deployment history

---

### 🧪 Canary Rollback (for Progressive Deployments)

For services using **Argo Rollouts**:

- If new version causes latency or errors, the rollout is **paused** automatically.
- The deployment is either auto-rolled back or a manual approval can revert it.

```yaml
analysis:
  templates:
    - templateName: success-rate-check
  args:
    - name: success-rate
      value: "95"
```

If the success rate drops below threshold, the rollout fails.

---

### ⚙️ Helm Rollback (Legacy Services)

For services deployed via Helm:

```bash
helm rollback my-app 2
```

- Lists previous releases and rolls back to a known good version.
- Useful during migration or testing phases.

---

### 🔍 Real-world Scenario

> “We pushed a change to our payment API and started seeing increased 500 errors. Since the deployment was managed by Argo CD, we immediately ran `git revert` and pushed the fix. Within 2 minutes, Argo CD synced the rollback, and the service stabilized without manual intervention in the cluster.”

---

### 🔐 Safeguards We Use

| Strategy                      | Purpose                                |
|------------------------------|----------------------------------------|
| Pre-deploy validation         | Prevents pushing broken YAML to Git   |
| Readiness & liveness probes   | Catch bad pods early                   |
| SLO-based auto rollback       | Canary rollouts monitored via metrics |
| Alerts on sync divergence     | Argo CD notifies on drift              |

---

### Key takeaway  

> "Our rollback strategy relies on GitOps principles — reverting Git changes triggers clean, trackable rollbacks. For high-risk deployments, we combine this with automated checks and canary monitoring to catch regressions early."


---

<img width="1345" height="827" alt="image" src="https://github.com/user-attachments/assets/7312f25e-4e8e-4274-9db3-c22ae2f65b91" />
