## Difference Between Liveness and Readiness Probes in Kubernetes

### Question  
What is the difference between **liveness** and **readiness** probes in Kubernetes?

### Short explanation of the question  
This checks your understanding of Kubernetes’ health check mechanisms that help manage container lifecycle and service availability.

---

### Answer  
**Liveness probes** check if a container is alive and should be restarted if unresponsive.  
**Readiness probes** check if a container is ready to receive traffic. If not, it's removed from the service endpoints until it's ready.

---

### Detailed explanation of the answer for readers’ understanding

---

### 💡 What is a Probe?

Probes are periodic checks Kubernetes performs to determine the state of a container.  
There are three types: `liveness`, `readiness`, and `startup`.

---

### 🔁 Liveness Probe

- **Purpose:** Detect if a container is stuck or dead.
- **Behavior:** If the liveness probe fails, the container is **restarted**.
- **Common use case:** Detects application lock-ups (e.g., infinite loops, deadlocks).

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

🧠 Think of this as: “Should this container be killed and restarted?”

---

### 🟢 Readiness Probe

- **Purpose:** Check if the app is **ready to accept traffic**.
- **Behavior:** If the readiness probe fails, the pod is **removed from the service endpoint list**, but **not restarted**.
- **Common use case:** Wait for app to fully initialize before receiving requests.

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 3
```

🧠 Think of this as: “Is this container ready to serve traffic?”

---

### 🧪 Real-World Example

> “We had a Java app that took ~40 seconds to load its cache. The readiness probe prevented traffic from hitting it too early, while the liveness probe restarted it if the app crashed during runtime.”

---

### 🔄 Summary Table

| Feature            | Liveness Probe             | Readiness Probe              |
|--------------------|----------------------------|------------------------------|
| Checks if app is   | **Alive**                  | **Ready to serve traffic**   |
| Failure Action     | Restarts the container     | Removes from service routing |
| Restarted on fail? | ✅ Yes                      | ❌ No                         |
| Affects traffic?   | ❌ No                       | ✅ Yes                        |

---

### Key takeaway  

> Use **readiness** probes to ensure your app isn’t hit with traffic too early, and **liveness** probes to auto-recover from hangs or crashes.

---

# Summary

````md id="v8m8a6"
Often people fail to explain what is the difference between liveness probe and readiness probe in Kubernetes.

I’ll first explain the difference between liveness and readiness using examples.
Then I’ll explain how you can answer this in interviews.

# What is Liveness Probe?

In Kubernetes, a liveness probe is used to check whether:
- The container is alive
- The application inside the container is running properly

If the application becomes unhealthy, Kubernetes automatically restarts the container.

## Real-Time Analogy

Think about a Load Balancer.

A Load Balancer:
- Continuously checks whether backend virtual machines are healthy
- Sends traffic only to healthy servers

Similarly, Kubernetes uses liveness probes to check container health.

---

## Why Liveness Probe Is Needed

Containers can fail because of:
- Application crash
- Deadlock
- Memory issues
- Hanging process

In such situations:
- Kubernetes can automatically restart the container
- Manual intervention is avoided

---

## Example

Application developers commonly expose a health endpoint like:

```text
/health


or

```text
/api/health
```

Modern frameworks like Spring Boot automatically provide such endpoints.

Kubernetes periodically calls this endpoint.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  periodSeconds: 5
```

If the endpoint does not return HTTP 200:

* Kubernetes assumes the application is unhealthy
* Container is restarted automatically

If repeated restarts happen:

* Pod may enter `CrashLoopBackOff`

---

# What is Readiness Probe?

Readiness Probe checks whether:

* The application is ready to accept traffic

This is different from liveness.

An application can be:

* Alive
* But not ready to serve requests

---

## Example Scenario

Suppose:

* Backend application is running
* But database connection is not ready

In this case:

* Application process is alive
* But application cannot properly serve user requests

So the pod should not receive traffic yet.

---

## Readiness Endpoint

Developers usually expose endpoints like:

```text
/ready
```

This endpoint may verify:

* Database connectivity
* Dependency availability
* External service availability

Example:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

If readiness probe fails:

* Kubernetes does NOT restart the pod
* Pod is simply removed from Service endpoints
* Traffic is stopped temporarily

Once readiness succeeds again:

* Pod starts receiving traffic

---

# Key Difference

## Liveness Probe

Checks:

* Is the application alive?

If failed:

* Kubernetes restarts the container

Purpose:

* Recover from crashes/deadlocks

---

## Readiness Probe

Checks:

* Is the application ready to serve traffic?

If failed:

* Pod is removed from service traffic
* No restart happens

Purpose:

* Prevent traffic to unready applications



# Quick Comparison

| Feature        | Liveness Probe        | Readiness Probe       |
| -------------- | --------------------- | --------------------- |
| Purpose        | Check if app is alive | Check if app is ready |
| Failure Action | Restart container     | Remove from traffic   |
| Used For       | Crash recovery        | Traffic management    |
| Restart Pod?   | Yes                   | No                    |

````

# Easy Interview Answer

“Liveness Probe and Readiness Probe are health checks configured inside a Kubernetes Pod definition.

* Liveness Probe checks whether the application/container is alive. If it fails, Kubernetes restarts the container.
* Readiness Probe checks whether the application is ready to serve traffic. If it fails, Kubernetes removes the pod from Service endpoints but does not restart it.”



