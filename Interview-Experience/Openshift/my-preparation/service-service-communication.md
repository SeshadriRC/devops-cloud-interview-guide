This is a very common **L2/L3 Kubernetes interview scenario**. The interviewer wants to evaluate your troubleshooting methodology rather than just commands.

A good answer should follow a systematic approach from DNS → Service → Endpoints → Pods → Network Policies → Application.

---

## Scenario

**Question:**

> A service in namespace `ABC` cannot communicate with a service in namespace `XYZ`. How will you troubleshoot?

---

## Step 1: Verify the Pods are Running

First, verify whether the application pods are healthy.

```bash
kubectl get pods -n ABC
kubectl get pods -n XYZ
```

Check

* Pod is Running
* Ready status is `1/1`
* No CrashLoopBackOff
* No ImagePullBackOff

If pods are not healthy, fix the application first.

---

## Step 2: Verify the Service

Check whether the service exists.

```bash
kubectl get svc -n XYZ
```

Example

```
NAME        TYPE        CLUSTER-IP
backend     ClusterIP   10.96.10.50
```

Describe it.

```bash
kubectl describe svc backend -n XYZ
```

Verify

* Correct selector
* Correct targetPort
* Correct port

---

## Step 3: Verify Endpoints

This is one of the most important checks.

```bash
kubectl get endpoints backend -n XYZ
```

or

```bash
kubectl describe endpoints backend -n XYZ
```

Expected

```
10.244.2.15:8080
10.244.2.16:8080
```

If endpoints are empty

```
Endpoints: <none>
```

Then usually

* Service selector is wrong
* Pods don't have matching labels
* Pods are not Ready

---

## Step 4: Verify Labels

Check pod labels.

```bash
kubectl get pods -n XYZ --show-labels
```

Check service selector.

```bash
kubectl describe svc backend -n XYZ
```

Example

Service selector

```
app=backend
```

Pod labels

```
app=backend
```

They must match exactly.

---

## Step 5: Test DNS Resolution

From a pod in namespace ABC

```bash
kubectl exec -it frontend-pod -n ABC -- sh
```

Run

```bash
nslookup backend.XYZ.svc.cluster.local
```

or

```bash
dig backend.XYZ.svc.cluster.local
```

Expected

```
Name: backend.XYZ.svc.cluster.local
Address: 10.96.10.50
```

If DNS fails

Check CoreDNS

```bash
kubectl get pods -n kube-system
```

```bash
kubectl logs -n kube-system deployment/coredns
```

---

## Step 6: Verify Connectivity

Inside the pod

```bash
curl http://backend.XYZ.svc.cluster.local:8080
```

or

```bash
wget
```

or

```bash
telnet
```

or

```bash
nc
```

Example

```bash
nc -zv backend.XYZ.svc.cluster.local 8080
```

If DNS resolves but connection times out

Likely

* NetworkPolicy
* Firewall
* Application not listening

---

## Step 7: Check Network Policies

Very common interview question.

```bash
kubectl get networkpolicy -A
```

Describe

```bash
kubectl describe networkpolicy -n XYZ
```

Possible issue

Namespace ABC is blocked from reaching namespace XYZ.

Example

Ingress policy only allows

```
namespace=production
```

while your namespace is

```
ABC
```

---

## Step 8: Verify Application Port

Exec into backend pod.

```bash
kubectl exec -it backend-pod -n XYZ -- sh
```

Check listening ports

```bash
netstat -tulnp
```

or

```bash
ss -tulnp
```

Verify application listens on

```
8080
```

Sometimes Service exposes

```
targetPort=8080
```

but application listens on

```
9090
```

---

## Step 9: Check Service Port Mapping

Example

```
Service

Port        80
TargetPort  8080
```

Pod

```
Listening on 8080
```

If targetPort is incorrect, traffic won't reach the application.

---

## Step 10: Verify Pod Logs

```bash
kubectl logs backend-pod -n XYZ
```

Maybe application is failing internally

Example

```
Database connection refused
```

or

```
Application failed to start
```

---

## Step 11: Test Direct Pod Connectivity

Try accessing the backend Pod IP directly.

```bash
kubectl get pods -o wide -n XYZ
```

Example

```
10.244.2.15
```

From ABC

```bash
curl http://10.244.2.15:8080
```

If Pod IP works but Service doesn't

Problem is likely

* Service
* Selector
* Endpoints

---

## Step 12: Check Namespace DNS Name

Sometimes developers call

```
http://backend
```

instead of

```
backend.XYZ.svc.cluster.local
```

or

```
backend.XYZ
```

Across namespaces, the namespace must be specified (or the fully qualified domain name used).

---

## Step 13: Verify CNI Plugin

If multiple pods across namespaces cannot communicate

Check CNI.

```bash
kubectl get pods -n kube-system
```

Verify

* Calico
* Cilium
* Flannel
* OVN-Kubernetes (OpenShift)

Check CNI logs if necessary.

---

## Step 14: Check kube-proxy

If Service IP isn't working

```bash
kubectl get pods -n kube-system
```

Look for

```
kube-proxy
```

Check logs

```bash
kubectl logs kube-proxy-xxxxx -n kube-system
```

---

# Typical Root Causes

| Problem                        | How to identify                  |
| ------------------------------ | -------------------------------- |
| Pod not running                | `kubectl get pods`               |
| Service missing                | `kubectl get svc`                |
| No endpoints                   | `kubectl get endpoints`          |
| Wrong selector                 | `kubectl describe svc`           |
| Labels mismatch                | `kubectl get pods --show-labels` |
| DNS issue                      | `nslookup` / `dig`               |
| NetworkPolicy blocking traffic | `kubectl get networkpolicy`      |
| Wrong targetPort               | `kubectl describe svc`           |
| Application not listening      | `ss -tulnp` / `netstat`          |
| CoreDNS issue                  | CoreDNS logs                     |
| kube-proxy issue               | kube-proxy logs                  |
| CNI issue                      | CNI pod status and logs          |

## Interview Answer (2-minute version)

> "I would troubleshoot layer by layer. First, I'd verify that the pods in both namespaces are running and ready using `kubectl get pods`. Next, I'd check that the service in namespace `XYZ` exists and that its selector matches the pod labels. Then I'd verify the service endpoints with `kubectl get endpoints`; if there are no endpoints, I'd investigate label mismatches or pod readiness. From a pod in namespace `ABC`, I'd test DNS resolution using `nslookup backend.XYZ.svc.cluster.local` and then test connectivity with `curl` or `nc` to the service. If DNS works but traffic is blocked, I'd inspect `NetworkPolicy` resources to see if ingress from namespace `ABC` is allowed. I'd also verify the application's listening port matches the service's `targetPort`, review application logs, and, if necessary, test direct connectivity to the backend Pod IP. Finally, if the issue appears cluster-wide, I'd investigate CoreDNS, the CNI plugin, and kube-proxy."
