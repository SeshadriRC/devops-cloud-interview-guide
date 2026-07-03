This is an excellent **senior Kubernetes/SRE interview question**. The interviewer wants to check whether you understand the difference between **CPU usage** and **CPU throttling**.

A good answer should explain that **100% CPU throttling does not necessarily mean the pod is consuming all its CPU limit**. It indicates that the Linux kernel (CFS - Completely Fair Scheduler) is throttling the container because it has exhausted its CPU quota during one or more scheduling periods.

---

# Interview Answer

> "If Grafana shows 100% CPU throttling while the pod is only using 2–3 cores out of a 4-core limit, I wouldn't assume the application is CPU-bound. I'd investigate whether the throttling is caused by short CPU bursts, incorrect CPU limits, CFS quota settings, multiple containers sharing the limit, or misleading metrics. I'd verify the throttling metrics, inspect the pod's resource configuration, and correlate them with application behavior."

---

# Step 1: Verify Resource Requests and Limits

Check the pod configuration.

```bash
kubectl describe pod <pod-name> -n <namespace>
```

or

```bash
kubectl get pod <pod-name> -o yaml
```

Example:

```yaml
resources:
  requests:
    cpu: "500m"
  limits:
    cpu: "4"
```

Verify:

* CPU request
* CPU limit
* Number of containers

---

# Step 2: Check Actual CPU Usage

Don't rely only on Grafana.

Use:

```bash
kubectl top pod <pod-name> -n <namespace>
```

Example

```
CPU(cores)

2500m
```

which means

```
2.5 cores
```

If usage is indeed around **2–3 cores**, the throttling needs further investigation.

---

# Step 3: Check CPU Throttling Metrics

Grafana typically displays metrics such as:

```
container_cpu_cfs_throttled_seconds_total
```

or

```
container_cpu_cfs_throttled_periods_total
```

Compare with:

```
container_cpu_cfs_periods_total
```

If almost every CFS period is being throttled, you'll see a high throttling percentage even if average CPU usage looks low.

---

# Step 4: Check for CPU Bursts

Average CPU usage may be **2.5 cores**, but the application may briefly spike above **4 cores**.

Example

```
0–90 ms      CPU = 2 cores

90–100 ms    CPU = 8 cores
```

During that burst, Linux throttles the container.

Grafana averages over time, hiding these short spikes.

This is one of the most common reasons.

---

# Step 5: Check Number of Containers

If the pod contains multiple containers:

```
Application

Sidecar

Log Collector

Istio Proxy
```

the CPU limit applies to each container individually if configured, or throttling may occur in one container while you're observing aggregate pod usage.

Use:

```bash
kubectl top pod --containers
```

---

# Step 6: Verify Node CPU Pressure

Check whether the node is under CPU pressure.

```bash
kubectl top node
```

Also inspect the node:

```bash
kubectl describe node <node-name>
```

Look for:

```
CPUPressure=True
```

If the node is oversubscribed, throttling may increase.

---

# Step 7: Check CFS Quota Configuration

Inside the container:

```bash
cat /sys/fs/cgroup/cpu.max
```

or on older cgroup versions:

```bash
cat /sys/fs/cgroup/cpu/cpu.cfs_quota_us
cat /sys/fs/cgroup/cpu/cpu.cfs_period_us
```

Example:

```
quota = 400000

period = 100000
```

Meaning:

```
4 CPUs
```

Verify that the quota matches the configured limit.

---

# Step 8: Check Application Behavior

Some workloads generate short CPU bursts.

Examples:

* Java garbage collection
* JIT compilation
* Compression
* Encryption
* JSON serialization
* Thread pool spikes

These bursts can trigger throttling even when the average CPU usage remains well below the limit.

---

# Step 9: Check Kubernetes Events

```bash
kubectl describe pod
```

Look for

```
OOMKilled

Evicted

Resource pressure
```

Although CPU throttling doesn't generate events directly, related resource issues may appear.

---

# Step 10: Check Kernel and Runtime

Sometimes throttling is caused by:

* Kernel bugs
* Older container runtimes
* Older Kubernetes versions
* Incorrect cgroup configuration

Check:

```bash
kubectl get node -o wide
```

and inspect the node's runtime and kernel versions.

---

# Possible Root Causes

| Possible Cause                 | How to Verify                                    |
| ------------------------------ | ------------------------------------------------ |
| Short CPU bursts               | Compare throttling metrics with CPU usage graphs |
| CPU limit too low              | `kubectl describe pod`                           |
| Incorrect CFS quota            | Inspect cgroup files (`cpu.max` or `cpu.cfs_*`)  |
| High throttled periods         | Review Prometheus/Grafana metrics                |
| Multiple containers            | `kubectl top pod --containers`                   |
| Node CPU contention            | `kubectl top node`, `kubectl describe node`      |
| JVM GC or application spikes   | Application logs and profiling                   |
| Misleading Grafana aggregation | Compare with raw Prometheus metrics              |

---

# Why can 100% throttling occur with only 2–3 cores of usage?

Imagine a pod has a **4-core limit**.

```
Time (100 ms CFS period)

0–95 ms    Uses 2 cores

95–100 ms  Needs 10 cores
```

The application exceeds its quota during the last few milliseconds, so the kernel throttles it for the remainder of that scheduling period. Over many periods, this can result in **100% throttled periods**, even though the **average CPU usage** is only **2–3 cores**.

This is why **CPU throttling and average CPU utilization are different metrics**.

---

## Interview Answer (2 minutes)

> "I would first verify the pod's CPU requests and limits using `kubectl describe pod`, then confirm the actual CPU consumption with `kubectl top pod` instead of relying solely on Grafana. Next, I'd compare `container_cpu_cfs_throttled_periods_total` with `container_cpu_cfs_periods_total` to understand how often throttling is occurring. A common reason is short CPU bursts that exceed the 4-core limit within individual CFS scheduling periods, even though the average usage remains around 2–3 cores. I'd also check if the pod has multiple containers, inspect node CPU pressure, verify the CFS quota configuration from the cgroup files, and review application behavior such as JVM garbage collection or bursty workloads. Finally, I'd validate that the Grafana dashboard is showing throttling percentages correctly and correlate them with raw Prometheus metrics before deciding whether to increase CPU limits or optimize the application."
