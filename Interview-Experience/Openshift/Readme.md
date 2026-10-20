# OpenShift & Kubernetes Interview Preparation Guide

This guide covers real-world enterprise OpenShift and Kubernetes interview questions, cluster troubleshooting scenarios, network debugging, resource throttling, and security architectures.

---

## 📑 Categorized Preparation Roadmap

### 1. Networking, CNI & Service Discovery
* 🌐 **Cross-Namespace Communication**: [Service in Namespace ABC Cannot Reach Namespace XYZ](my-preparation/service-service-communication.md)  
  *Network policies, multitenancy, DNS resolution format (`svc.namespace.svc.cluster.local`).*
* 🔄 **Pod-to-Pod Communication**: [Both Services Running But Communication Fails](my-preparation/pod-to-pod-communication.md)  
  *Service selectors, port mapping discrepancies, CNI overlay network issues.*
* 🔍 **DNS & Network Diagnostics**: [Troubleshooting When Both nslookup and curl Fail](my-preparation/nslookup-curl.md)  
  *CoreDNS/OpenShift DNS debugging, IP routing, iptables/kube-proxy rules, and network traces.*
* 🗺️ **Architecture Flow**: [End-to-End Service Discovery and Communication Architecture](my-preparation/service-discovery-kubernetes.md)  
  *Step-by-step path: Client pod → CoreDNS → Service IP (kube-proxy / OVN) → Endpoints / EndpointSlices → Target Pod.*
* 🔌 **Container Network Interface (CNI)**: [What is CNI and How OpenShift Uses OVN-Kubernetes](my-preparation/CNI.md)  
  *CNI responsibilities, IPAM, OpenShift OVN-Kubernetes architecture.*

### 2. Performance, CPU Throttling & Memory Issues
* ⚡ **Grafana CPU Throttling**: [Pod Shows 100% CPU Throttling While Node Has Idle Cores](my-preparation/cpu-throttled-grafana.md)  
  *CFS quota enforcement, period/quota metrics (`container_cpu_cfs_throttled_periods_total`), multi-threading bursts.*
* 🎯 **Kernel CFS Enforcement**: [Why Containers Throttle Even with Low Node CPU](my-preparation/pod-throttled-container.md)  
  *Differentiating node-level CPU utilization from container cgroup limits.*
* 🧠 **Memory Leaks & OOMKilled**: [Investigating and Mitigating Container Memory Leaks](my-preparation/memory-leak.md)  
  *Exit Code 137, OOMKilled events, JVM heap sizing, heap dumps, memory profiling, and garbage collection.*

### 3. Cluster Administration & Deployment Strategies
* 🛑 **Control Plane Outages**: [Impact on Worker Nodes and Pods When Master Node is Down](Interview-Q&A.md#3-what-happens-to-pods-in-worker-nodes-if-master-node-is-down-in-k8s)  
  *API server unavailability, no scheduling, existing pods keep running, impact on ingress and statefulsets.*
* 🟢 **Zero-Downtime Rollouts**: [Blue-Green Deployment Strategy in Kubernetes](my-preparation/blue-green.md)  
  *Service selector switching, traffic verification, instant rollback mechanisms.*
* 🧩 **Extending Kubernetes**: [Custom Resource Definitions (CRDs) and Operators](my-preparation/CRD.md)  
  *Defining custom APIs, reconciling controllers, and deploying database operators.*

### 4. Security, Secrets & Workload Identity
* 🔐 **Secret Security**: [Kubernetes Secrets vs. HashiCorp Vault](my-preparation/kubernetes-secret-hashicorp-difference.md)  
  *Base64 encoding risks vs. enterprise encryption-at-rest, dynamic secrets, rotation, and audit logs.*
* ☁️ **AWS Cloud Integration**: [K8s Pod Accessing AWS S3 via ServiceAccount & IAM OIDC](my-preparation/serviceaccount-oidc.md)  
  *IAM Roles for Service Accounts (IRSA), OIDC provider trust, assuming roles without static AWS access keys.*
* 🛡️ **OpenShift Security Constraints**: [Reconciling Security Context Constraints (SCCs)](Interview-Q&A.md#1-what-is-oc-adm-policy-reconcile-sccs)  
  *Using `oc adm policy reconcile-sccs` to repair cluster security policies.*

---

## 🛠️ Key OpenShift Commands Cheat Sheet

```bash
# Switch CLI context
oc config use-context <context-name>

# Check node status and taints
oc get nodes -o wide
oc describe node <node-name>

# Troubleshoot DNS in OpenShift
oc exec -it <pod-name> -n <namespace> -- nslookup kubernetes.default.svc.cluster.local

# Check OVN-Kubernetes CNI pods
oc get pods -n openshift-ovn-kubernetes

# Reconcile default SCCs
oc adm policy reconcile-sccs --confirm
```
