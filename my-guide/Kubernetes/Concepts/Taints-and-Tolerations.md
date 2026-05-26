# Taints and Tolerations

Taints are used to **block Pods** from running on certain nodes.

Think like:

> "Do not schedule Pods here unless they explicitly tolerate this node."

---

## Node Taint Example

Apply taint on node:

```bash id="cn3l5e"
kubectl taint nodes worker-1 env=prod:NoSchedule
```

Meaning:

* Node `worker-1` rejects all Pods
* Unless Pod has matching toleration

---

## Pod Without Toleration

```yaml id="mjpw6s"
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

This Pod CANNOT schedule on tainted node.

---

## Pod With Toleration

```yaml id="4gtxfx"
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  tolerations:
  - key: "env"
    operator: "Equal"
    value: "prod"
    effect: "NoSchedule"

  containers:
  - name: nginx
    image: nginx
```

Now scheduler allows it.

---

## Easy Understanding

| Concept    | Meaning                |
| ---------- | ---------------------- |
| Taint      | Node says "keep away"  |
| Toleration | Pod says "I can enter" |

---
