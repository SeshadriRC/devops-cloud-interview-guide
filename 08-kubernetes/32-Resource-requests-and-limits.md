````md id="kw9d6s"
# What Are Kubernetes Requests and Limits?

This is a very important Kubernetes concept.

Even outside interviews:
- If you are working as a DevOps engineer
- Managing Kubernetes clusters in production

You must clearly understand:
- Resource Requests
- Resource Limits

---

# Understanding Through a Real Example

Imagine you are managing a Kubernetes cluster with 3 worker nodes.

## Cluster Setup

| Node | CPU | Memory |
|---|---|---|
| Node 1 | 16 CPU | 16 GB RAM |
| Node 2 | 2 CPU | 2 GB RAM |
| Node 3 | 32 CPU | 32 GB RAM |

---

# Scenario

A developer approaches you and says:

> “I want to deploy a Java application.  
> It is resource intensive and needs at least:
> - 4 CPU
> - 4 GB RAM”

---

# Problem Without Requests

Suppose:
- You create Deployment YAML
- But you do NOT define resource requests

Kubernetes Scheduler may:
- Schedule the pod on Node 2

Even though Node 2 only has:
- 2 CPU
- 2 GB RAM

Result:
- Application may crash
- Out Of Memory (OOM) issues may occur
- Performance issues happen

---

# Resource Requests

To avoid this:
- Kubernetes provides `requests`

Requests define:

```text
Minimum guaranteed resources required by the pod
````

Example:

```yaml id="v0l5fd"
resources:
  requests:
    cpu: "4"
    memory: "4Gi"
```

Now Scheduler understands:

```text id="hjlwm3"
This pod needs at least:
- 4 CPU
- 4 GB RAM
```

So Scheduler will:

* Avoid Node 2
* Schedule only on suitable nodes

Example:

* Node 1
* Node 3

---

# What Are Limits?

Requests solve scheduling.

But what if application starts consuming unlimited resources?

---

# Example Scenario

Suppose:

* Pod gets scheduled on Node 1
* Node already uses:

  * 10 CPU
  * 10 GB RAM

Initially:

* Application works fine

But as traffic increases:

* Pod starts consuming more memory and CPU

Example:

* 6 CPU
* 6 GB RAM
* Then even more...

Eventually:

* Single pod may consume entire node resources

This creates problems:

* Other pods cannot run
* Node instability
* Resource starvation

---

# Resource Limits

To avoid this:

* Kubernetes provides `limits`

Limits define:

```text
Maximum resources a pod/container can consume
```

Example:

```yaml id="f3npxx"
resources:
  requests:
    cpu: "4"
    memory: "4Gi"

  limits:
    cpu: "8"
    memory: "8Gi"
```

Meaning:

* Minimum required:

  * 4 CPU
  * 4 GB RAM

* Maximum allowed:

  * 8 CPU
  * 8 GB RAM

If pod exceeds limits:

* Kubernetes may terminate/evict the pod
* Prevents node resource exhaustion

---

# Why Limits Are Important

Especially for:

* Java applications
* Memory-intensive applications

Because:

* Memory leaks are common
* Applications may consume unlimited memory

Without limits:

* One pod can consume entire node resources

---

# Simple Understanding

## Requests

```text id="s6lrlf"
Minimum resources required for scheduling
```

Used by:

* Kubernetes Scheduler

---

## Limits

```text id="r1tf5k"
Maximum resources allowed during runtime
```

Enforced by:

* Kubelet

---

# Example Pod YAML

```yaml id="9i2i9g"
apiVersion: v1
kind: Pod

metadata:
  name: resource-demo

spec:
  containers:
  - name: nginx
    image: nginx

    resources:
      requests:
        memory: "256Mi"
        cpu: "500m"

      limits:
        memory: "512Mi"
        cpu: "1"
```

---

# What Happens Here?

## Requests

```text id="jzd4qz"
memory: 256Mi
cpu: 500m
```

Scheduler guarantees:

* 256 MB RAM
* 0.5 CPU

---

## Limits

```text id="f3a7sk"
memory: 512Mi
cpu: 1
```

Container cannot exceed:

* 512 MB RAM
* 1 CPU

If exceeded:

* Pod may get terminated/evicted

---

# Easy Interview Definition

## Resource Requests

> “Resource Requests are the minimum guaranteed resources required by a pod for scheduling.”

## Resource Limits

> “Resource Limits define the maximum resources a pod/container can consume during runtime. These are enforced by Kubelet.”

---

# Easy Interview Answer

“Kubernetes Requests and Limits are used for efficient resource management.

* Requests define the minimum CPU and memory required by the pod. Scheduler uses this information to place the pod on suitable nodes.
* Limits define the maximum CPU and memory the pod can consume during runtime. If limits are exceeded, Kubernetes may throttle or terminate the container.

This prevents a single pod from consuming all node resources and helps maintain cluster stability.”

```
