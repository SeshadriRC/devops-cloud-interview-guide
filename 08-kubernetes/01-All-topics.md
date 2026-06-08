## Architecture

- [Read-doc-1](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/Architecture/Kubernetes-explained.md)
- [Read-doc-2](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/Architecture/Architecture.md)

---
## Pod

**Pod**

[read-here](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/Pod/guide.md)

---

**Pod Affinity and Anti-affinity**

[refer](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/PodAffinity/Affinity.md)

---

**ReplicaSets**

[refer](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/Replicasets/Replicasets.md)

---
**Login to the running pod**

```bash
kubectl exec -it <pod-name> -- sh    # for sh based container
kubectl exec -it <pod-name> -- bash  # for bash based container

kubectl exec -it java-app-deployment-6ffb7dd46-bc84f -- sh
```

---
## Probes

[Probe-notes-refer](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/Probes/guide.md)

---
## Deployment

[deployment-notes](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/Deployments)


---
## ConfigMap-Secrets-Env variables

[notes](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/ConfigMap-Secrets-Env)

---
# Resource

**What is Resource and Requests**

[Refer](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/resource-limit-and-request)

---
## Daemonset

[notes](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/DaemonSet/guide.md)

---

## Service

**Service**

[Service-and-its-types](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/Service)

---

**Kubectl port forward, how to do**

```bash
root@controlplane:~$ kubectl get svc -n monitoring
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                     ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP   12m
prometheus-grafana                        ClusterIP   10.96.206.179    <none>        80/TCP                       12m

kubectl port-forward --address 0.0.0.0 -n monitoring svc/prometheus-grafana 3000:80
```

- This command forwards local port 3000 on the machine running kubectl to port 80 of the Kubernetes Service prometheus-grafana in the monitoring namespace. The --address 0.0.0.0 option allows connections from any network interface, making the application accessible remotely rather than only from localhost.

```bash
# is used to check whether port 3000 is in use and which process is using it.
root@controlplane:~$ ss -tulnp | grep 3000
tcp   LISTEN 0      4096                              0.0.0.0:3000       0.0.0.0:*    users:(("kubectl",pid=8025,fd=8))                     
root@controlplane:~$ 
```

| Part                     | Meaning                                                                  |
| ------------------------ | ------------------------------------------------------------------------ |
| `kubectl`                | Kubernetes command-line tool                                             |
| `port-forward`           | Creates a tunnel from your local machine to a Pod/Service in the cluster |
| `--address 0.0.0.0`      | Listen on all network interfaces (not just localhost)                    |
| `-n monitoring`          | Service is in the `monitoring` namespace                                 |
| `svc/prometheus-grafana` | Target Kubernetes Service named `prometheus-grafana`                     |
| `3000:80`                | Forward local port **3000** to Service port **80**                       |

**Traffic flow**

```
Browser
   |
   | http://<node-ip>:3000
   |
Control Plane Node
(Local Port 3000)
   |
kubectl port-forward
   |
Service: prometheus-grafana (Port 80)
   |
Grafana Pod(s)
```

---

## Statefulset

[read](https://github.com/SeshadriRC/documentation/blob/main/kubernetes/Statefulset/guide-statefulset.md)

---

## PV / PVC / Storageclass / Provisioner

[read](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/PV-PVC-Storage-class)

---

## Ingress Controller

[read](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/Ingress-Ingress-controller)

---

## RBAC

[read](https://github.com/SeshadriRC/documentation/tree/main/kubernetes/RBAC)

---
