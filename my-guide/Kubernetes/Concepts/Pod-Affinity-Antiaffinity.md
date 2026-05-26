
# 1. Pod Affinity & Anti-Affinity

These control:

* Which Pods should run together
* Which Pods should NOT run together

---

# Pod Affinity

Means:

> "Schedule my Pod near another Pod"

Example:

* Web app should run near cache Pod

---

## Affinity YAML

```yaml id="f1j4qr"
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - backend
        topologyKey: "kubernetes.io/hostname"

  containers:
  - name: nginx
    image: nginx
```

### Meaning

Scheduler places `frontend` Pod on node where:

* Pod with label `app=backend` already exists

---

# Pod Anti-Affinity

Means:

> "Do NOT place my Pod near another Pod"

Used for:

* High availability
* Avoiding same node failure

---

## Anti-Affinity YAML

```yaml id="fjc5k5"
apiVersion: v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp

  template:
    metadata:
      labels:
        app: myapp

    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - myapp
            topologyKey: "kubernetes.io/hostname"

      containers:
      - name: nginx
        image: nginx
```

### Meaning

Scheduler tries:

* NOT to place multiple `myapp` Pods on same node

So replicas spread across nodes.

---

# Very Simple Real-Time Example

## Taints/Tolerations

* Production nodes should accept only production Pods

## Affinity

* App Pod should run near DB Pod for low latency

## Anti-Affinity

* Multiple replicas should run on different nodes for HA

---
