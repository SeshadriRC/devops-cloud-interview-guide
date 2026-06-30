1. what is `oc adm policy reconcile-sccs`

- Restore or reconcile Security Context Constraints (SCCs) to their default OpenShift configuration.

---

2. Which command will switch current CLI context to context name "flyers"

```bash
oc config use-context flyers
```

---

3. What happens to pods in worker nodes if master node is down in k8s.What are the impacts

- If the master node in Kubernetes goes down, the worker nodes will continue to operate as usual. However, there will be some impact on the pods running on the worker nodes, especially if the pods rely on services or resources provided by the Kubernetes control plane, which runs on the master node.

Here are some of the impacts that pods on worker nodes may experience if the master node is down:

**No API Access:** The Kubernetes API server runs on the master node and is responsible for managing the entire cluster. If the master node goes down, the API server becomes unavailable, and no new requests can be made to the API server. Pods that rely on the Kubernetes API server to perform operations such as scaling or updating their configurations will be affected.

**No Scheduling or Rescheduling:** The Kubernetes scheduler is responsible for placing pods on available worker nodes. If the master node goes down, the scheduler becomes unavailable, and no new pods can be scheduled or rescheduled to run on the worker nodes.

**No Service Discovery:** Kubernetes uses a built-in DNS service called kube-dns to provide service discovery within the cluster. If the master node goes down, kube-dns becomes unavailable, and pods will not be able to discover or communicate with other services running within the cluster.

**No Cluster-Level Resource Management:** The Kubernetes control plane, which runs on the master node, is responsible for managing resources such as storage, networking, and security across the entire cluster. If the master node goes down, pods running on worker nodes may not be able to access or manage these resources.

In summary, if the master node in Kubernetes goes down, the impact on pods running on worker nodes will depend on how the pods are configured and what services or resources they rely on. However, by design, worker nodes are intended to be resilient and continue to operate independently of the master node.

---

4. Resourcequota command in Openshift

```bash
oc get resourcequota
```

---
