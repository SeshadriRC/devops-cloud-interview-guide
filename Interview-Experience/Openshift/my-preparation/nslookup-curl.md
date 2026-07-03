This is an excellent interview question because it tests your understanding of the Kubernetes networking stack.

### Scenario

From a pod in namespace `ABC`:

```bash
nslookup backend.XYZ.svc.cluster.local
```

fails, and

```bash
curl http://backend.XYZ.svc.cluster.local:8080
```

also fails.

### What does it indicate?

Since **`nslookup` itself is failing**, the issue is **likely at the DNS layer**. `curl` depends on DNS to resolve the service name, so if DNS resolution fails, `curl` will also fail when using the service name.

Possible reasons include:

* CoreDNS is not running or is unhealthy.
* DNS configuration inside the pod is incorrect.
* The service name or namespace is incorrect.
* The service does not exist.
* There is a cluster-wide DNS issue.

---

## How would you proceed?

### Step 1: Verify the Service Exists

```bash
kubectl get svc -n XYZ
```

Ensure the service `backend` exists.

---

### Step 2: Verify the Service Name

Cross-namespace communication should use:

```text
backend.XYZ
```

or

```text
backend.XYZ.svc.cluster.local
```

If the service name or namespace is wrong, DNS resolution will fail.

---

### Step 3: Check CoreDNS

```bash
kubectl get pods -n kube-system
```

Look for the CoreDNS pods.

Example:

```text
coredns-7d8f6b7c9d-abcde   Running
```

If they are in `CrashLoopBackOff` or `Pending`, DNS won't work.

Check logs:

```bash
kubectl logs -n kube-system <coredns-pod>
```

---

### Step 4: Verify DNS Configuration Inside the Pod

```bash
kubectl exec -it <source-pod> -n ABC -- cat /etc/resolv.conf
```

Typical output:

```text
nameserver 10.96.0.10
search ABC.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

Check that:

* A valid `nameserver` (CoreDNS ClusterIP) is present.
* The search domains look correct.

---

### Step 5: Test DNS Directly

```bash
kubectl exec -it <source-pod> -n ABC -- nslookup kubernetes.default
```

If this also fails, it's a **cluster DNS issue**, not just an issue with the target service.

---

### Step 6: Test Using the Service IP

Get the ClusterIP:

```bash
kubectl get svc backend -n XYZ
```

Suppose it is:

```text
10.96.10.50
```

Now test:

```bash
curl http://10.96.10.50:8080
```

**Interpretation:**

* **If this works:** DNS is the problem.
* **If this also fails:** The problem is likely with the Service, endpoints, application, or networking.

---

### Step 7: Verify Endpoints

```bash
kubectl get endpoints backend -n XYZ
```

If you see:

```text
Endpoints: <none>
```

the Service has no healthy backend pods.

---

### Step 8: Check Network Policies

Even if DNS works, `curl` can still fail if traffic is blocked.

```bash
kubectl get networkpolicy -A
```

Inspect policies in both namespaces.

---

## Interview Answer (1-minute)

> "If both `nslookup` and `curl` fail, my first assumption is a DNS issue because `curl` relies on DNS resolution when using the service name. I would first verify that the target Service exists and that I'm using the correct fully qualified service name. Next, I'd check the CoreDNS pods and their logs, inspect `/etc/resolv.conf` inside the source pod, and test DNS resolution for `kubernetes.default` to determine whether it's a cluster-wide DNS issue. After that, I'd try accessing the Service directly using its ClusterIP. If the ClusterIP works, the issue is DNS. If it doesn't, I'd investigate the Service endpoints, application, and any `NetworkPolicy` rules blocking traffic."
