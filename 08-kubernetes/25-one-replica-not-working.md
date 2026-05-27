## Your Deployment Has `replicas: 3`, but Only 1 Pod Is Running — What Could Be Wrong?

### Short explanation of the question  
This scenario tests your ability to troubleshoot **replica mismatches** in Kubernetes — where the desired state (3 pods) doesn’t match the actual state (1 pod running).

---

### Answer  
There could be several reasons: resource constraints on nodes, scheduling issues, crashlooping pods, or affinity/taint restrictions that prevent pods from starting.

---

### Detailed explanation of the answer for readers’ understanding

---

### 🔍 Troubleshooting Checklist

#### ✅ 1. **Check Pod Statuses**

Run:
```bash
kubectl get pods -l app=my-app
```

You might see:
- 1 Running
- 2 Pending / CrashLoopBackOff / ImagePullBackOff

---

#### ✅ 2. **Describe the Deployment and Pods**

```bash
kubectl describe deployment my-deployment
kubectl describe pod <pod-name>
```

Look for:
- Events at the bottom (e.g., “failed scheduling”)
- Crash loop messages
- Image pull errors
- Volume mounting errors

---

#### ✅ 3. **Check Node Capacity**

Maybe the other pods can’t be scheduled due to insufficient **CPU/memory**.

Run:
```bash
kubectl describe nodes
```

If the nodes are out of resources, new pods won’t start.

---

#### ✅ 4. **Check Affinity Rules and Taints**

If your deployment or namespace has node affinity or tolerations set, it may restrict where pods can land.

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
              operator: In
              values:
                - ssd
```

Pods will only be scheduled on matching nodes.

---

#### ✅ 5. **Check for Pod Crashes**

Run:
```bash
kubectl logs <pod-name>
```

If pods are crashing, Kubernetes may try to restart them, but they'll never stay in the "Running" state.

---

### 🧪 Real-World Example

> “We once set a memory limit of `100Mi` in the deployment, but the app needed 200Mi. Only one pod was running because the others kept OOMKilled. Increasing the memory resolved the issue.”

---

### 🔄 Summary Table

| Check                         | What It Tells You                           |
|------------------------------|---------------------------------------------|
| `kubectl get pods`           | Status of all pods                          |
| `kubectl describe`           | Events and reasons for pending/crashes      |
| Node capacity                | Resource exhaustion                         |
| Affinity/Taints              | Constraints preventing scheduling           |
| Container logs               | Runtime crashes or app issues               |

---

### Key takeaway

> If `replicas: 3` but only 1 pod is running, start by checking pod status, node resource limits, crash logs, and affinity/taint rules. The issue is often with scheduling or container-level crashes.

---

# Summary

<img width="1016" height="413" alt="image" src="https://github.com/user-attachments/assets/8f82fa74-acb6-430e-8e11-7b8350b980f4" />



````md id="x4c90l"
# Scenario-Based Kubernetes Interview Question

## Deployment Has 3 Replicas but Only 1 Pod Is Running — What Could Be Wrong?

This is a real-time Kubernetes troubleshooting interview question.

The tricky part:
- One pod is already running successfully
- Only remaining replicas are failing

This means we can eliminate many common issues.

---

# Issues That Can Be Ignored

Since one pod is already running:
- Image exists correctly
- Image pull secrets are working
- Basic deployment configuration is valid
- Application can start successfully

So issues like:
- `ImagePullBackOff`
- Wrong image name
- Missing image pull secret
- Basic CrashLoopBackOff issues

are less likely.

---

# Most Common Root Cause

The most probable reason is:

```text
Insufficient Cluster Resources
````

Example:

* Deployment requests:

```yaml
resources:
  requests:
    cpu: "4"
    memory: "8Gi"
```

But cluster nodes only have:

* 2 CPU
* 4 GB RAM

Result:

* One pod gets scheduled
* Remaining pods remain pending

---

# Another Possible Reason — Taints

Suppose:

* Nodes with sufficient resources are tainted

Then:

* Pod cannot schedule onto those nodes
* Unless tolerations are added

---

# Troubleshooting Steps

## Step 1 — Check Deployment/Pod Configuration

Inspect:

* CPU requests
* Memory requests
* Node selectors
* Tolerations

Example:

```yaml
resources:
  requests:
    cpu: "2"
    memory: "4Gi"
```

Check whether requested resources are too high.

---

## Step 2 — Describe Deployment or Pod

Run:

```bash
kubectl describe deployment <deployment-name>
```

or

```bash
kubectl describe pod <pod-name>
```

Look for scheduling errors like:

```text id="bczr8u"
0/3 nodes are available
Insufficient cpu
Insufficient memory
```

This clearly indicates resource shortage.

---

# Step 3 — Check Kubernetes Events

Run:

```bash
kubectl get events
```

Events provide historical scheduling failures.

Possible errors:

* Insufficient CPU
* Insufficient memory
* Taint mismatch
* Node affinity issues

---

# Step 4 — Verify Node Resources

Check node capacity:

```bash
kubectl describe node <node-name>
```

or

```bash
kubectl top nodes
```

Verify:

* Available CPU
* Available memory

---

# Step 5 — Check Taints and Tolerations

Check node taints:

```bash
kubectl describe node <node-name>
```

Possible taint:

```text id="b3sv8n"
NoSchedule
```

If nodes are tainted:

* Pod needs matching tolerations

Example:

```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "backend"
  effect: "NoSchedule"
```

---

# Possible Solutions

## Option 1 — Add More Nodes

Scale cluster using:

* Cluster Autoscaler
* Karpenter
* Manual node addition

---

## Option 2 — Reduce Resource Requests

Lower CPU/memory requests if overestimated.

---

## Option 3 — Add Tolerations

If nodes are tainted:

* Add proper tolerations to pod configuration

---

# Easy Interview Answer

“If a deployment has three replicas but only one pod is running, first I check the deployment configuration for CPU/memory requests and tolerations. Since one pod is already running, issues like image pull failure are unlikely. Then I use `kubectl describe` and `kubectl get events` to identify scheduling issues such as insufficient resources or tainted nodes. If resources are insufficient, I can scale the cluster using Cluster Autoscaler or Karpenter. If nodes are tainted, I add appropriate tolerations to the pod.”

