````md id="t4jv9v"
# Difference Between Horizontal Scaling and Vertical Scaling

This is a very commonly asked interview question in DevOps and Kubernetes interviews.

---

# Simple Definition

| Scaling Type | Meaning |
|---|---|
| Horizontal Scaling | Adding more instances/nodes |
| Vertical Scaling | Increasing resources of existing instance/node |

---

# Example 1 — Kubernetes Cluster Scaling

Imagine:
- You have a Kubernetes cluster
- Cluster contains 3 worker nodes

Initially:
- Cluster works perfectly fine

After some time:
- More applications are deployed
- More users start using the applications

Now:
- Worker nodes are using 90% CPU and Memory

---

# How Can You Solve This?

You have two options:

---

# Horizontal Scaling (Cluster Level)

Add a new worker node to the cluster.

Example:

```text id="e1oqv0"
3 Worker Nodes  →  4 Worker Nodes
````

This increases:

* Overall cluster capacity

This is called:

```text id="9evr3s"
Horizontal Scaling
```

Because:

* You are scaling outward
* Adding more machines/nodes

---

# Vertical Scaling (Cluster Level)

Instead of adding nodes:

* Increase CPU and RAM of existing worker nodes

Example:

```text id="3j9t17"
2 CPU, 4 GB RAM
        ↓
8 CPU, 16 GB RAM
```

This is called:

```text id="p4apjo"
Vertical Scaling
```

Because:

* You are increasing power of existing machine

---

# Example 2 — Application Scaling

Suppose:

* Application is deployed using Kubernetes Deployment
* Replica count is 1

Initially:

* Application works fine

Later:

* Users increase
* Application becomes slow
* Developers observe:

  * High CPU usage
  * High Memory usage

---

# Option 1 — Vertical Scaling

Increase pod/container resources.

Example:

```yaml id="xj0c5n"
resources:
  limits:
    cpu: "4"
    memory: "4Gi"
```

Previously:

```text id="vx4k9q"
2 CPU → 4 CPU
2 GB RAM → 4 GB RAM
```

This is:

```text id="yl2hmt"
Vertical Scaling
```

Because:

* Existing pod gets more resources

---

# Option 2 — Horizontal Scaling

Increase replicas.

Example:

```yaml id="r1aq87"
replicas: 3
```

Previously:

```text id="2zff3o"
1 Pod → 3 Pods
```

Traffic gets distributed across:

* Multiple pod replicas

This is:

```text id="r6mh35"
Horizontal Scaling
```

Because:

* More instances are added

---

# Kubernetes Example

## Vertical Scaling

```text id="msmk8h"
Increase CPU/RAM of existing pod or node
```

Examples:

* 2 CPU → 4 CPU
* 4 GB RAM → 8 GB RAM

---

## Horizontal Scaling

```text id="82yn9v"
Increase number of pods or nodes
```

Examples:

* 1 Pod → 3 Pods
* 3 Nodes → 5 Nodes

---

# Simple Real-World Analogy

## Vertical Scaling

```text id="m6tqj4"
One worker works faster with better machine
```

---

## Horizontal Scaling

```text id="xjlwm9"
Hire more workers
```

---

# Easy Interview Answer

“Horizontal Scaling means increasing capacity by adding more instances, such as additional pods or worker nodes.

Vertical Scaling means increasing the resources like CPU and memory of existing pods or nodes.

For example:

* Increasing replicas from 1 to 3 is Horizontal Scaling.
* Increasing pod memory from 2 GB to 4 GB is Vertical Scaling.”

```
