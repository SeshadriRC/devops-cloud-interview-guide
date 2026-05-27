`maxSurge` is used during a **RollingUpdate deployment strategy** in Kubernetes.

It defines:

> **How many extra pods can be created temporarily during an update.**

---

Your example:

```yaml id="6e44x9"
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 1
```

means:

* Kubernetes can create **1 extra new pod**
* Kubernetes can make **1 old pod unavailable** during the update

---

## Example

Assume Deployment has:

```text id="ub08gn"
Replicas = 4
```

During update:

### `maxSurge: 1`

Kubernetes can temporarily run:

```text id="0qvxvf"
4 existing pods + 1 new pod = 5 pods
```

This helps achieve:

* Zero/minimal downtime
* Smooth rollout

---

### `maxUnavailable: 1`

At most:

* 1 old pod can be down at a time

So minimum available pods will be:

```text id="3n8q24"
4 - 1 = 3 available pods
```

---

## Rolling Update Flow

Kubernetes does:

```text id="xx3x0q"
Create 1 new pod
↓
Terminate 1 old pod
↓
Repeat until update completes
```

---

## Easy Interview Definition

> “maxSurge defines how many extra pods Kubernetes can create temporarily during a rolling update. It helps ensure application availability while new pod versions are being deployed.”
