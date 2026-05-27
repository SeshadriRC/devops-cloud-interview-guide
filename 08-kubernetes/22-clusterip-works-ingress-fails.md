## Your App Works with ClusterIP but Fails with Ingress – How Do You Troubleshoot It?

### Question  
You’re able to access your app using its ClusterIP service internally, but it fails when accessed via Ingress. How do you troubleshoot the issue?

### Short explanation of the question  
This scenario tests your practical knowledge of Kubernetes networking, particularly around Ingress controllers and how Ingress routing works.

---

### Answer  
I would check if the Ingress controller is installed and running, verify the ingress rules, confirm DNS or host header matches, and review logs and service connectivity. Most often, the issue lies in incorrect rules, missing annotations, or DNS misconfiguration.

---

### Detailed explanation of the answer for readers’ understanding

---

### 🔍 Step-by-Step Troubleshooting Process

---

#### ✅ 1. **Check if Ingress Controller is Installed and Running**

Ingress resources only work if there’s a controller (e.g., NGINX, Traefik, AWS ALB Controller) running in the cluster.

```bash
kubectl get pods -n ingress-nginx
```

Make sure it’s in a `Running` state.

---

#### ✅ 2. **Check the Ingress Resource Definition**

```bash
kubectl describe ingress <ingress-name>
```

- Are the rules defined correctly?
- Are you using correct **`host`** or **`path`**?
- Does the `serviceName` and `port` match your ClusterIP service?

---

#### ✅ 3. **Check Annotations and Class**

Ensure the Ingress has the correct controller class:

```yaml
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
```

Or for newer versions:

```yaml
spec:
  ingressClassName: nginx
```

---

#### ✅ 4. **Check DNS or Host Header Configuration**

If you're using a host-based rule like `app.example.com`, make sure:

- DNS points to the ingress controller’s external IP.
- Or you’ve added a line in `/etc/hosts`:

```bash
<ingress-external-ip>  app.example.com
```

If you hit the IP directly but the app uses host-based routing, it may return `404`.

---

#### ✅ 5. **Check Logs of the Ingress Controller**

```bash
kubectl logs -n ingress-nginx <controller-pod-name>
```

Common errors include:

- `default backend - 404`
- `no matching path rule`
- TLS errors

---

#### ✅ 6. **Check Backend Service and Pod Health**

Sometimes, the Ingress forwards correctly, but the backend service is broken:

```bash
kubectl get endpoints <service-name>
```

Ensure there are endpoints (pods) behind the service.

---

#### ✅ 7. **Check TLS/HTTPS Configuration**

If using HTTPS, verify:

- TLS secret is valid.
- Rules include `tls` section.
- Ingress controller supports HTTPS.

---

### 🧪 Real-World Example

> “Our app worked internally via ClusterIP but gave a 404 over Ingress. It turned out we had a missing `ingressClassName` field, so the resource wasn’t being picked up by the NGINX controller at all.”

---

### 🔄 Summary Table

| Check                           | Description                                        |
|----------------------------------|----------------------------------------------------|
| Ingress Controller Running       | Must be deployed for Ingress to work              |
| Ingress Rules & Paths            | Must match service correctly                      |
| Hostname or Path Match           | Ensure DNS or `/etc/hosts` is correctly configured|
| Logs of Ingress Controller       | Debug routing errors                              |
| Backend Service & Endpoints      | Ensure pods are reachable                         |

---

### Key takeaway  

> If your app works via ClusterIP but not Ingress, the issue is often with the **Ingress configuration**, missing annotations, DNS setup, or **controller not handling the resource**.


---

<img width="1831" height="740" alt="image" src="https://github.com/user-attachments/assets/b33b9c14-0a72-4dc2-a5d0-dc2f543b05cf" />



````md id="tnxj0x"
# Scenario-Based Kubernetes Interview Question

## Application Works with ClusterIP but Fails with Ingress — How Do You Troubleshoot?

This is a common Kubernetes troubleshooting interview question.

---

# Scenario Understanding

Suppose:

- Application pod is running properly
- Service of type `ClusterIP` is created
- Internal communication within the cluster works perfectly

Example:

```text
Pod → ClusterIP Service → Backend Pod ✅
````

But when external access is enabled using Ingress:

```text
Client → example.com → Ingress → Service → Pod ❌
```

Traffic fails.

The interviewer asks:
How would you troubleshoot this issue?

---

# Step 1 — Check if Ingress Controller Is Installed

Creating only an Ingress resource is not enough.

Ingress Controller is required because:

* It watches Ingress resources
* Configures load balancer/reverse proxy
* Routes external traffic to services

Examples:

* NGINX Ingress Controller
* Traefik
* HAProxy
* Envoy

Check whether ingress controller pods are running.

Example:

```bash
kubectl get pods -n ingress-nginx
```

---

# Step 2 — Check Ingress Controller Logs

If controller exists:

* Check controller logs

Purpose:

* Verify whether ingress resource is detected
* Identify configuration errors

Example:

```bash
kubectl logs <ingress-controller-pod> -n ingress-nginx
```

Things to verify:

* Is ingress resource being watched?
* Any routing/configuration errors?
* Any backend/service errors?

---

# Step 3 — Verify Ingress Class Name

In clusters with multiple ingress controllers:

* `ingressClassName` decides which controller handles the ingress

Example:

```yaml
ingressClassName: nginx
```

If class name is missing or incorrect:

* Ingress controller ignores the ingress resource

Also ensure:

* Same class exists in ingress controller configuration

---

# Step 4 — Verify Ingress Backend Configuration

Sometimes ingress exists correctly, but backend service mapping is wrong.

Check:

* Service name
* Service port
* Namespace
* Path configuration

Example:

```yaml
backend:
  service:
    name: payment-service
    port:
      number: 80
```

Common mistakes:

* Wrong service name
* Wrong port number
* Incorrect namespace
* Invalid path rules

---

# Step 5 — Verify Service and Endpoints

Even though ClusterIP works:

* Ensure ingress points to correct service

Check:

```bash
kubectl get svc
kubectl get endpoints
```

Verify:

* Endpoints exist
* Pods are attached correctly

---

# Step 6 — Verify DNS and Load Balancer

If using external DNS like:

```text
example.com
```

Verify:

* DNS resolves correctly
* Load balancer IP/domain is correct
* Security groups/firewalls allow traffic

---

# Common Root Causes

Most ingress issues happen because of:

* Missing ingress controller
* Incorrect ingress class
* Wrong backend service mapping
* DNS issues
* Invalid ingress rules

---

# Easy Interview Answer

“If the application works through ClusterIP but fails through Ingress, first I check whether the Ingress Controller is installed and running. Then I inspect ingress controller logs to verify whether the ingress resource is detected correctly. I also verify the `ingressClassName`, backend service name, service port, and ingress rules. Finally, I check DNS resolution, load balancer configuration, and service endpoints to identify the root cause.”



---
