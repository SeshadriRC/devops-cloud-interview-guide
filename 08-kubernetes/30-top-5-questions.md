Hiring for DevOps and Cloud roles is not about checking tool names on a resume; it is about understanding how candidates think about systems, reliability, and Kubernetes internals. To help you prepare, I have put together the five core questions I ask to reveal whether someone truly understands cloud-native infrastructure.

# Can You Explain the Different Types of Kubernetes Deployments?
This question tests your understanding of availability, risk, and release strategies. I expect you to know when to use each, not just their definitions.

Recreate: All existing pods are terminated before new ones are created, causing downtime.

Rolling Update (Default): Pods are updated gradually to ensure zero or minimal downtime.

Blue-Green: Two identical environments exist; traffic switches instantly between them for instant rollbacks.

Canary: The new version is released to a small subset of users to monitor performance before a full rollout.

---
# Control Plane vs Worker Plane: How Does the Architecture Work?
This separates users from engineers. You must explain how these components interact.

The Control Plane (The Brain): Includes the kube-apiserver (the entry point), etcd (the key-value store for cluster state), the scheduler (decides pod placement), and the controller manager (reconciles desired vs actual state).

The Worker Plane (The Muscle): Consists of the kubelet (the agent on every node) and kube-proxy (which manages network rules and traffic).

---
# Explain Kubernetes Services: ClusterIP, NodePort, and LoadBalancer
This checks your networking fundamentals and real-world usage patterns.

ClusterIP: The default, internal-only service used for backend communication.

NodePort: Exposes the service on a static port across every node; generally not production-friendly.

LoadBalancer: Provisions a cloud-provider load balancer (like AWS ELB) to expose the service publicly.

---
# What Is Pod & Node Affinity / Anti-Affinity?
This tests your scheduling intelligence and how you handle high availability.

Node Affinity: Controls which specific nodes a pod can run on (e.g., ensuring GPU workloads land on GPU-capable nodes).

Pod Anti-Affinity: Used to run pods apart, such as spreading replicas across different availability zones to improve fault tolerance.

---

# What Is the Difference Between Helm and Argo CD?
This is a personal favourite because it highlights your approach to GitOps.

<img width="1024" height="717" alt="image" src="https://github.com/user-attachments/assets/af09d0ed-807f-4974-a88b-1260cfc463d6" />


If you can confidently explain these five areas, you are showing that you think in terms of reliability and scale. As we look toward the technology shifts of 2026, focusing on these internals is very necessary.
