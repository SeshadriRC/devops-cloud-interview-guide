1. what is readdir()

- readdir() reads entries from an opened directory and returns details like inode number, filename, record length, and file type.

---

2. Difference between `sudo su - ansible` and `su - ansible`

Good question. These two commands look similar but behave differently.

## 1. `sudo su - ansible`

```bash
sudo su - ansible
```

Meaning:

* `sudo` → execute command as **root**
* `su - ansible` → root switches to **ansible user**
* `-` → load ansible user's login environment

Flow:

```text
Current User → root → ansible
```

Example:

```text
ubuntu
 ↓ sudo
root
 ↓ su -
ansible
```

This requires **sudo permission**.

Check:

```bash
whoami
```

Output:

```text
ansible
```

Check original user:

```bash
echo $SUDO_USER
```

Output:

```text
ubuntu
```

## 2. `su - ansible`

```bash
su - ansible
```

Meaning:

* Directly switch to `ansible`
* Requires **ansible user's password**
* No sudo involved

Flow:

```text
Current User → ansible
```

Example:

```text
ubuntu
 ↓ su -
ansible
```

System asks:

```text
Password:
```

You must know `ansible` user's password.


## Real-world server usage

Usually on cloud servers:

```bash
sudo su - ansible
```

is more common because:

* Users login using SSH keys
* Service accounts (ansible, oracle, postgres) often don't have passwords

Example:

```bash
ssh ubuntu@server
sudo su - oracle
```


### Quick comparison

| Command             | Uses sudo? | Requires target user password? | Intermediate user |
| ------------------- | ---------- | ------------------------------ | ----------------- |
| `sudo su - ansible` | Yes        | No                             | root              |
| `su - ansible`      | No         | Yes                            | none              |

For AWS EC2, `sudo su - ansible` is usually what you use.


---

3. I need to find my application is consuming which port, how to do


```bash
ss -tulnp | grep nginx
```

---

4. An application is slow, but CPU and memory are normal. What would you investigate?


This is a common **DevOps/SRE interview question**. The interviewer wants to know whether you can troubleshoot **beyond CPU and memory**.

A structured answer is best.


## Interview Answer

> "If an application is slow but CPU and memory utilization are normal, I would investigate other possible bottlenecks layer by layer rather than assuming it's a compute resource issue."

### 1. Check Application Logs

First, I would check the application logs for:

* Exceptions
* Timeout errors
* Connection failures
* Thread pool exhaustion
* Garbage Collection (for Java applications)

Commands:

```bash
kubectl logs <pod-name>
```

or

```bash
docker logs <container-id>
```


### 2. Check Database Performance

A slow database is one of the most common causes.

I would check:

* Slow-running SQL queries
* Database locks
* Connection pool exhaustion
* High query latency

Example:

```text
Application
      ↓
Waiting for DB response
      ↓
Application appears slow
```

### 3. Check Network Latency

I would verify whether there is network delay between:

* Application and Database
* Application and External APIs
* Microservice to Microservice communication

Commands:

```bash
ping
```

```bash
traceroute
```

```bash
curl -w "%{time_total}"
```


### 4. Check External Dependencies

Many applications depend on:

* REST APIs
* Authentication services
* Payment gateways
* Third-party services

If one dependency is slow, the entire application becomes slow.

### 5. Check Kubernetes/OpenShift

If running in Kubernetes, I'd verify:

```bash
kubectl get pods
```

Check whether:

* Pods are restarting
* Pods are stuck in `Pending`
* Readiness probes are failing

Then inspect events:

```bash
kubectl describe pod <pod-name>
```


### 6. Check Disk I/O

CPU may be normal while disk I/O is saturated.

Commands:

```bash
iostat
```

```bash
iotop
```

```bash
df -h
```

A slow disk can delay:

* Reading files
* Writing logs
* Database operations


### 7. Check Thread or Connection Pools

For Java applications, I would verify:

* Thread pool utilization
* Database connection pool
* HTTP connection pool

If all threads or connections are busy, requests queue up, causing slow responses.


### 8. Check Load Balancer

I would verify:

* Are all backend servers healthy?
* Is traffic evenly distributed?
* Are there unhealthy targets?

For example, if only one backend is receiving traffic, response times can increase.


### 9. Check Monitoring Metrics

Using tools such as:

* Prometheus
* Grafana
* Datadog

I'd look at:

* Request latency
* Error rates (4xx/5xx)
* Response times
* Network traffic
* Disk usage


### 10. Use Distributed Tracing

If OpenTelemetry, Jaeger, or Zipkin is configured, I'd trace a slow request to identify exactly where time is spent.

Example:

```text
Client
   │
   ▼
Application (10 ms)
   │
   ▼
Service A (20 ms)
   │
   ▼
Database (3 seconds)
```

The trace immediately highlights the bottleneck.

---

## Real-time Example

Suppose a user reports:

> "The login page takes 20 seconds."

I would investigate in this order:

1. Check application logs for errors or timeouts.
2. Verify pod health and restarts.
3. Test database response time and look for slow queries.
4. Check if the authentication service is responding slowly.
5. Review Prometheus/Grafana dashboards for latency spikes.
6. Use distributed tracing to identify which service is delaying the request.

---

## Short Interview Answer (2 minutes)

> "If CPU and memory are normal, I wouldn't assume the infrastructure is healthy. I'd check the application logs for errors, verify database performance and slow queries, inspect network latency, review any external API dependencies, and check Kubernetes pod health. I'd also look at disk I/O, thread pools, and load balancer health. Finally, I'd use monitoring dashboards and distributed tracing to identify exactly where the request latency is occurring. This systematic approach helps isolate whether the bottleneck is in the application, database, network, or an external dependency."

---
