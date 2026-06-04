<img width="1424" height="817" alt="image" src="https://github.com/user-attachments/assets/b49f8431-3c30-4881-972b-ae08747d38f7" />


## What kind of metrics do you work with in your current project?

This is a common interview question. Instead of simply saying *"I monitor CPU, memory, and disk usage,"* it's better to classify the metrics into categories and explain them. This demonstrates a deeper understanding of monitoring and observability.

### Sample Interview Answer

In my current organization, we use Prometheus as our primary monitoring tool. We collect and monitor metrics from multiple layers of the infrastructure and applications.

I generally classify the metrics into three categories:

1. Infrastructure Metrics
2. Kubernetes/OpenShift Metrics
3. Application Metrics

---

## 1. Infrastructure Metrics

We use Prometheus Node Exporter to collect infrastructure-level metrics from Linux servers and worker nodes.

Some of the key metrics we monitor are:

* CPU utilization (`node_cpu_seconds_total`)
* Available memory (`node_memory_MemAvailable_bytes`)
* Disk usage and I/O metrics
* Network traffic metrics
* Filesystem utilization
* Load average

These metrics help us identify resource bottlenecks and capacity issues on the underlying servers.

---

## 2. Kubernetes/OpenShift Metrics

Since our applications run on Kubernetes/OpenShift, we also monitor cluster and container-related metrics.

Some important metrics include:

### Container Metrics

* Container CPU utilization
* Container memory utilization
* Container restart count
* Pod status and health
* Resource requests and limits

These metrics are typically collected through Kubelet and container runtime integrations.

### Cluster Component Metrics

We also monitor critical Kubernetes components such as:

* API Server health
* Scheduler health
* Controller Manager status
* ETCD performance and latency

For this, we use:

kube-state-metrics

which exposes Kubernetes object and cluster-state information.

---

## 3. Application Metrics

Application metrics are the most important because they directly impact end users.

Some examples include:

### HTTP Metrics

* Total HTTP requests
* Request rate (RPS)
* HTTP error rate (4xx/5xx)
* Request latency
* Response time percentiles

### Database Metrics

* Query execution time
* Database connection count
* Slow queries
* Database availability and health

### Business Metrics

We also monitor business-specific metrics such as:

* Number of successful payments
* Number of failed payments
* Orders processed
* User registrations

For example, if the application processes online payments, developers expose a Prometheus Counter metric to track payment failures over time. This helps us create alerts and dashboards around critical business operations.

---

## Why This Approach Is Better

Instead of saying:

> "I monitor CPU, memory, and disk."

A stronger answer is:

> "I monitor infrastructure metrics using Node Exporter, Kubernetes metrics using Kubelet and kube-state-metrics, and application/business metrics such as request latency, database performance, and payment failures using Prometheus."

This shows the interviewer that you understand monitoring across the entire stack—from infrastructure to Kubernetes to business applications.

---

## Interview-Friendly Answer (Short Version)

In my current project, we use Prometheus for monitoring. I primarily work with three categories of metrics:

* **Infrastructure Metrics:** CPU, memory, disk I/O, filesystem usage, and network traffic collected through Node Exporter.
* **Kubernetes/OpenShift Metrics:** Pod health, container CPU and memory usage, restart counts, API server health, and ETCD metrics collected through Kubelet and kube-state-metrics.
* **Application Metrics:** HTTP request count, latency, error rates, database performance metrics, and business-specific metrics such as payment failures and transaction counts.

This layered monitoring approach helps us ensure the health of the infrastructure, Kubernetes platform, and the applications running on it.

