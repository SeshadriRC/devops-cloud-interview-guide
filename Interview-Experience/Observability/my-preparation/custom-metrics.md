This is a common **OpenShift/Platform Engineer + Datadog** interview question. They want to know **how you instrument an application, expose custom metrics, configure Datadog Agent, create dashboards, and set up monitors**.

---

# Scenario

Assume you have a deployment:

```text
Namespace: dev
Deployment: payment-app
Replicas: 3
```

You need to:

1. Create custom metrics from the application.
2. Collect those metrics in Datadog.
3. Visualize them on a dashboard.
4. Create alerts (monitors).

---

# Architecture

```text
                 payment-app Pods
              ------------------------
              |  /metrics endpoint   |
              | requests_total       |
              | order_count          |
              | payment_latency      |
              ------------------------
                        |
                        | Prometheus scrape
                        |
                Datadog Agent (DaemonSet)
                        |
                        |
                Datadog Backend
                  /            \
          Dashboard         Monitor
```

---

# Step 1: Instrument the application

The application should expose metrics.

Example (Spring Boot + Micrometer):

```
/actuator/prometheus
```

Example metrics:

```
payment_success_total

payment_failure_total

order_processed_total

payment_duration_seconds

active_users

cart_items
```

If using Go:

```
/metrics
```

NodeJS:

```
/metrics
```

Python:

```
/metrics
```

---

# Step 2: Expose the metrics endpoint

Verify inside pod

```bash
curl localhost:8080/metrics
```

Output

```
payment_success_total 500

payment_failure_total 5

payment_duration_seconds 0.42
```

---

# Step 3: Configure Datadog Agent

Datadog Agent usually runs as a **DaemonSet** in Kubernetes/OpenShift.

Enable OpenMetrics scraping.

Example annotations on Deployment:

```yaml
metadata:
  annotations:
    ad.datadoghq.com/app.check_names: '["openmetrics"]'

    ad.datadoghq.com/app.init_configs: '[{}]'

    ad.datadoghq.com/app.instances: |
      [
        {
          "openmetrics_endpoint":"http://%%host%%:8080/metrics",
          "namespace":"payment",
          "metrics":["*"]
        }
      ]
```

Now the Datadog Agent automatically scrapes:

```
http://podIP:8080/metrics
```

---

# Step 4: Verify metrics reach Datadog

Go to Datadog

```
Metrics Explorer
```

Search

```
payment.success_total

payment.failure_total

payment.duration_seconds
```

If they appear, ingestion is successful.

---

# Step 5: Create Dashboard

Create widgets like:

```
Payment Success Count

Payment Failure Count

Average Payment Latency

Requests/sec

Error %

CPU

Memory

Pod Restarts
```

Example query

```
sum:payment.success_total{namespace:dev}
```

Latency

```
avg:payment.duration_seconds{namespace:dev}
```

Failure Rate

```
sum:payment.failure_total{*}
/
sum:payment.success_total{*}
```

---

# Step 6: Create Monitor

Example 1

Alert if payment failures > 20

Query

```
sum(last_5m):payment.failure_total{namespace:dev} > 20
```

Notification

```
Slack

Email

PagerDuty

MS Teams
```

---

Example 2

Latency monitor

```
avg(last_5m):payment.duration_seconds > 1
```

Meaning

```
If average latency exceeds 1 second for 5 minutes

Trigger alert
```

---

Example 3

Pod restart monitor

```
sum:kubernetes.containers.restarts{deployment:payment-app}
```

Threshold

```
>3 in 10 minutes
```

---

# Step 7: Verify Alert

Generate traffic

```
ab

curl

JMeter
```

Suppose latency becomes

```
2.3 seconds
```

Datadog Monitor

```
Status

ALERT

Payment latency exceeded threshold
```

Slack receives

```
Payment latency exceeded 1 second
Namespace : dev
Deployment : payment-app
```

---

# Real-world metrics you might create

```
payment_success_total

payment_failure_total

payment_duration_seconds

orders_processed

login_count

active_sessions

cache_hit_ratio

database_query_time

api_response_time

queue_depth

inventory_count
```

---

# If application doesn't expose metrics

You have two options:

* Instrument the application using libraries like **Micrometer** (Java), **Prometheus client libraries** (Go, Python, Node.js), etc.
* Or create metrics from logs using Datadog **Log-Based Metrics**. For example, if every successful payment writes `Payment Successful` to the logs, you can define a log-based metric that counts those events and then build dashboards and monitors on it.

---

# Interview Answer (2–3 minutes)

> "First, I'd instrument the application to expose custom metrics through a `/metrics` or `/actuator/prometheus` endpoint using a Prometheus-compatible library such as Micrometer. Then I'd configure the Datadog Agent running as a DaemonSet to scrape that endpoint using OpenMetrics autodiscovery annotations. Once the metrics are ingested, I'd verify them in Datadog Metrics Explorer. Next, I'd build a dashboard to visualize key metrics like request count, error rate, latency, CPU, and memory. Finally, I'd create Datadog monitors with appropriate thresholds—for example, alert if payment failures exceed 20 in 5 minutes or if average latency is above 1 second for 5 minutes—and configure notifications to Slack, PagerDuty, or email so the operations team is alerted proactively."
