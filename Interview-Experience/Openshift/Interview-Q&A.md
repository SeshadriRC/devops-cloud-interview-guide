1. what is `oc adm policy reconcile-sccs`

- Restore or reconcile Security Context Constraints (SCCs) to their default OpenShift configuration.

---

2. Which command will switch current CLI context to context name "flyers"

```bash
oc config use-context flyers
```

---

# 3. Blue greedn deployment in Kubernetes


**Blue-Green Deployment** in Kubernetes is a deployment strategy used to **release a new application version with near-zero downtime and quick rollback**.

Idea:

* **Blue = Current production version**
* **Green = New version**
* Traffic goes to only one environment at a time.
* Once Green is verified → switch traffic from Blue to Green.

---

## Normal Deployment Problem

Suppose current app:

```text id="2oc7jf"
v1 → Running in production
```

You deploy v2 directly.

If deployment fails:

```text id="4hq2ko"
Users affected ❌
```

Blue-Green avoids this.

---

## Blue-Green Flow

### Step 1 — Blue is live

```text id="ekxqg8"
Users
  ↓
Service
  ↓
Blue Pods (v1)
```

Example:

```yaml
deployment-blue
app: myapp
version: blue
```

---

### Step 2 — Deploy Green

Create new deployment:

```text id="3pnz4p"
Users
  ↓
Service
  ↓
Blue (v1)

Green (v2)
(Not receiving traffic)
```

Green pods start in parallel.

Example:

```yaml
deployment-green
app: myapp
version: green
```

Validate:

* Pod status
* Logs
* Health checks

Commands:

```bash
kubectl get pods
kubectl logs
kubectl describe pod
```

---

### Step 3 — Switch Traffic

Service initially:

```yaml
selector:
  app: myapp
  version: blue
```

Change selector:

```yaml
selector:
  app: myapp
  version: green
```

Traffic now:

```text id="y3mz4q"
Users
  ↓
Service
  ↓
Green Pods (v2)
```

No pod restart required.

Command:

```bash
kubectl apply -f service.yaml
```

---

### Step 4 — Rollback (if issue)

If Green fails:

Change service back:

```yaml
selector:
  version: blue
```

Traffic immediately returns.

---

## Kubernetes Example

Blue Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
spec:
  replicas: 3
```

Green Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
spec:
  replicas: 3
```

Service:

```yaml
selector:
  app: myapp
  version: green
```

---

## Visual

Before:

```text
Service
  ↓
Blue v1
```

During:

```text
Service
 ↓
Blue v1

Green v2
```

After switch:

```text
Service
 ↓
Green v2
```

---

## Blue-Green vs Rolling Update

| Feature                | Blue-Green              | Rolling Update |
| ---------------------- | ----------------------- | -------------- |
| Downtime               | Very low                | Very low       |
| Rollback               | Immediate               | Slower         |
| Resources              | Higher (2 environments) | Lower          |
| Testing before release | Yes                     | Limited        |
| Complexity             | Medium                  | Lower          |

---

## OpenShift / Helm scenario (close to your environment)

Typical flow:

```text id="smyrjv"
Jenkins
 ↓
helm upgrade
 ↓
Green deployment created
 ↓
Route/Service switched
 ↓
Validate
 ↓
Delete Blue
```

---

### Interview answer

> “In Kubernetes Blue-Green deployment, two identical environments are maintained. Blue serves production traffic while Green runs the new version. After validating Green, traffic is switched using Service selectors or routes. If issues occur, rollback is done by redirecting traffic back to Blue, providing minimal downtime and quick recovery.”

Correct — **applying `service.yaml` again does not restart pods**.

In Kubernetes, **Service and Pods are separate resources**.

When you update a Service:

```bash
kubectl apply -f service.yaml
```

Kubernetes updates only the **Service object** (mainly selector/routing rules).

Pods continue running.

---

### Example

Current state:

```text
Service
 selector: version=blue

Pods:
app-blue-1
app-blue-2
app-green-1
app-green-2
```

Traffic:

```text
Users
 ↓
Service
 ↓
Blue Pods
```

Now update Service:

```yaml
spec:
  selector:
    app: myapp
    version: green
```

Run:

```bash
kubectl apply -f service.yaml
```

Result:

```text
Users
 ↓
Service
 ↓
Green Pods
```

Blue pods are still alive.

No restart.

---

You can verify:

Before:

```bash
kubectl get pods
```

Output:

```text
app-blue-1   Running
app-green-1  Running
```

Apply service.

Run again:

```bash
kubectl get pods
```

Same output.

Only routing changed.

---

You can observe service endpoints:

```bash
kubectl get endpoints
```

Before:

```text
service → blue pod IPs
```

After:

```text
service → green pod IPs
```

(or)

```bash
kubectl describe svc my-service
```

You will see endpoint change.

---

Pods restart only if something changes in the **Deployment/ReplicaSet/Pod spec**, for example:

```bash
kubectl rollout restart deployment app
```

or

```yaml
spec:
  template:
    spec:
      containers:
```

Changing pod template triggers rollout.

But changing:

```yaml
kind: Service
```

→ **No pod restart.**

This separation of **Service (network)** and **Deployment (pods)** is what makes Blue-Green switching fast.

---
