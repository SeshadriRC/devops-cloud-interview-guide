# What is Kubernetes QoS (Quality of Service)?

This is a very common Kubernetes interview question.

QoS stands for **Quality of Service**.

Instead of starting with textbook definitions, let’s understand it using a real-world scenario.

---

# Real-Time Scenario

Imagine you have a Kubernetes cluster with 3 worker nodes.

* Node 1 → 10 Pods
* Node 2 → 20 Pods
* Node 3 → 30 Pods

Assume one of the nodes has:

* 4 CPU
* 4 GB RAM

Now suppose the node starts running out of memory.

Maybe:

* Some Pods suddenly consume more resources
* The node is nearing an **Out Of Memory (OOM)** condition

At this point, Kubernetes must decide:

> “Which Pods should be evicted first to keep the node healthy?”

Kubernetes cannot randomly remove Pods.

It needs a mechanism to prioritize Pods.

That mechanism is called **QoS (Quality of Service)**.

---

# What Does QoS Do?

QoS assigns a **QoS Class** to every Pod in the cluster.

Based on this class, Kubernetes decides:

* Which Pods are most important
* Which Pods can be evicted first during resource pressure

Especially during:

* Memory pressure
* OOM situations
* Node resource exhaustion

---

# QoS Classes in Kubernetes

There are 3 QoS classes:

1. **Guaranteed**
2. **Burstable**
3. **BestEffort**

---

# 1. Guaranteed QoS

This is the **highest priority** class.

A Pod gets **Guaranteed** QoS when:

* Every container has both:

  * `requests`
  * `limits`
* And usually requests = limits

Example:

```yaml id="okx2dg"
resources:
  requests:
    memory: "512Mi"
    cpu: "500m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

### Behavior

* These Pods are least likely to be evicted.
* Kubernetes tries to keep them running even during memory pressure.

---

# 2. Burstable QoS

This is the **medium priority** class.

A Pod gets **Burstable** QoS when:

* Requests are defined
* But limits are missing OR different

Example:

```yaml id="3gzv4m"
resources:
  requests:
    memory: "256Mi"
    cpu: "200m"
```

### Behavior

* These Pods can use additional resources if available.
* They are evicted after BestEffort Pods.

---

# 3. BestEffort QoS

This is the **lowest priority** class.

A Pod gets **BestEffort** QoS when:

* No requests
* No limits

Example:

```yaml id="m6y2xj"
resources: {}
```

or no resources section at all.

### Behavior

* These Pods are evicted first during resource pressure.

---

# Eviction Priority During Memory Issues

When a node experiences memory pressure, Kubernetes evicts Pods in this order:

1. **BestEffort** → Evicted first
2. **Burstable** → Evicted second
3. **Guaranteed** → Evicted last

So:

* Guaranteed Pods have the highest protection
* BestEffort Pods have the least protection

---

# Practical Example

Create a deployment without requests and limits:

```bash id="we07fs"
kubectl create deployment nginx --image=nginx
```

Check the Pod:

```bash id="o0fwif"
kubectl get pods
```

Describe the Pod:

```bash id="0i9g57"
kubectl describe pod <pod-name>
```

You will see:

```bash id="ym3tf6"
QoS Class: BestEffort
```

Because no resource requests or limits were provided.

---

# Final Interview Summary

You can answer like this in interviews:

> “QoS (Quality of Service) in Kubernetes is a mechanism used to prioritize Pods during resource pressure situations like memory exhaustion. Kubernetes assigns a QoS class to every Pod based on resource requests and limits. The three QoS classes are Guaranteed, Burstable, and BestEffort. During node resource issues, BestEffort Pods are evicted first, followed by Burstable Pods, while Guaranteed Pods receive the highest protection and are evicted last.”
