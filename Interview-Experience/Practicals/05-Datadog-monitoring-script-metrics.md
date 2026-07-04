# Datadog Monitoring Script

Create a script in Node.js that integrates with the Datadog API to send custom metrics. The script should send at least two different metrics from an
application running in an AWS EKS cluster.

Follow these requirements:

Authenticate with the Datadog API
Define and send two custom metrics.
Handle any potential API errors gracefully.

Skills Evaluated:

Node.js development
API integration
Monitoring and metrics management

---

This question is testing your knowledge of:

* **Node.js**
* **REST API integration**
* **Datadog Metrics API**
* **Authentication**
* **Error handling**
* **Monitoring in Kubernetes (EKS)**

The interviewer does **not** expect you to memorize the Datadog API. They want to see that you know how to authenticate, send metrics, and handle failures.

---

# Scenario

Suppose your Node.js application is running inside an **AWS EKS** pod.

You want to send two custom metrics to Datadog every minute:

* Application response time
* Number of active users

Architecture:

```text
Node.js App (Pod in EKS)
        │
        │ HTTPS POST
        ▼
Datadog Metrics API
        │
        ▼
Datadog Dashboard
```

---

# Step 1: Install Axios

```bash
npm install axios
```

Axios is used to make HTTP requests.

---

# Step 2: Store Datadog API Key

Never hardcode the API key.

Instead, use an environment variable.

Linux:

```bash
export DATADOG_API_KEY=xxxxxxxxxxxxxxxx
```

Kubernetes Secret:

```yaml
env:
- name: DATADOG_API_KEY
  valueFrom:
    secretKeyRef:
      name: datadog-secret
      key: api-key
```

---

# Step 3: Node.js Script

```javascript
const axios = require("axios");

const API_KEY = process.env.DATADOG_API_KEY;

const url = "https://api.datadoghq.com/api/v1/series";

async function sendMetrics() {

    const payload = {
        series: [
            {
                metric: "myapp.response_time",
                points: [[Math.floor(Date.now() / 1000), 150]],
                type: "gauge",
                tags: ["env:dev", "service:web"]
            },
            {
                metric: "myapp.active_users",
                points: [[Math.floor(Date.now() / 1000), 45]],
                type: "gauge",
                tags: ["env:dev", "service:web"]
            }
        ]
    };

    try {

        const response = await axios.post(url, payload, {
            headers: {
                "DD-API-KEY": API_KEY,
                "Content-Type": "application/json"
            }
        });

        console.log("Metrics sent successfully");
        console.log(response.data);

    } catch (error) {

        if (error.response) {

            console.error("Datadog API Error");
            console.error(error.response.status);
            console.error(error.response.data);

        } else {

            console.error("Network Error");
            console.error(error.message);

        }

    }
}

sendMetrics();
```

---

# What the Script Does

## Authentication

```javascript
headers: {
   "DD-API-KEY": API_KEY
}
```

Authenticates using the Datadog API key.

---

## Metric 1

```javascript
metric: "myapp.response_time"
```

Suppose your application response time is

```text
150 ms
```

This is sent as a **gauge** metric.

---

## Metric 2

```javascript
metric: "myapp.active_users"
```

Suppose

```text
45 active users
```

Again sent as a gauge metric.

---

## Timestamp

```javascript
Math.floor(Date.now()/1000)
```

Datadog expects the timestamp in **Unix epoch seconds**.

---

## Error Handling

If Datadog returns

```text
401 Unauthorized
```

the script prints

```text
Datadog API Error
401
```

If the network is unavailable

```text
Network Error
```

is displayed instead.

This demonstrates graceful error handling.

---

# Running in EKS

The pod might look like:

```yaml
containers:
- name: node-app

  image: myrepo/node-app

  env:

  - name: DATADOG_API_KEY
    valueFrom:
      secretKeyRef:
        name: datadog-secret
        key: api-key
```

Every minute the application can execute

```javascript
sendMetrics();
```

or schedule it using:

```javascript
setInterval(sendMetrics, 60000);
```

---

# Example Metrics in Datadog

Once received, you'll be able to visualize:

```text
myapp.response_time

150
140
160
155
145
```

and

```text
myapp.active_users

42
45
48
50
53
```

These can be displayed on Datadog dashboards or used to trigger alerts.

---

# Interview Explanation (2 Minutes)

> "In my Node.js application, I would integrate with the Datadog Metrics API using an HTTP client such as Axios. The Datadog API key would be stored securely as an environment variable or Kubernetes Secret rather than hardcoded. The script would build a payload containing multiple custom metrics—for example, application response time and active user count—along with timestamps and tags like environment and service name. It would send the metrics to the Datadog Metrics API over HTTPS. I would wrap the API call in a `try...catch` block to handle failures gracefully, logging API errors such as authentication issues or network connectivity problems without crashing the application. This allows operational metrics from applications running in AWS EKS to be monitored through Datadog dashboards and alerts."



---
# how the application is executing every minute ?

- Good question. The Node.js application itself does not automatically execute every minute. There are several ways to achieve this, depending on the use case.
- In many production EKS environments, applications don't call the Datadog API directly for every metric.

```
Node.js Application
        │
        ▼
Datadog Agent (DaemonSet)
        │
        ▼
Datadog Cloud

```
- The application sends metrics to the Datadog Agent running on the cluster, and the agent batches and forwards them efficiently. This reduces API calls and simplifies authentication.

---

# Ok you are sending metric, now i need to visualize in datadog console. how to achieve

Once the metrics are sent successfully to Datadog, they are stored in Datadog's **Metrics** database. You can then create dashboards, graphs, alerts, and monitors based on those metrics.

Let's use the same example.

Your Node.js application sends these metrics every minute:

```javascript
metric: "myapp.response_time"

metric: "myapp.active_users"
```

---

# Step 1: Verify the Metrics Are Received

Log in to the Datadog console.

Navigate to:

```text
Metrics
   ↓
Metrics Explorer
```

In the search box, type:

```text
myapp.response_time
```

If the metric has been received, Datadog will autocomplete it.

Similarly:

```text
myapp.active_users
```

If you don't see them, verify:

* The API key is correct.
* The API request succeeded (HTTP 202/200 depending on the endpoint).
* The metric names are spelled correctly.
* You're looking in the correct Datadog site (US1, EU, etc.).

---

# Step 2: Visualize in Metrics Explorer

Select the metric:

```text
myapp.response_time
```

Choose:

* Visualization: **Timeseries**
* Aggregation: **Average**
* Time: Last 1 hour

You'll see a graph similar to:

```text
Response Time (ms)

170 ┤
160 ┤          ●
150 ┤      ●       ●
140 ┤   ●
130 ┤
    └────────────────────────► Time
```

You can repeat the same process for:

```text
myapp.active_users
```

---

# Step 3: Create a Dashboard

Go to:

```text
Dashboards
      ↓
New Dashboard
```

Click:

```text
Add Widget
```

Choose:

```text
Timeseries
```

Query:

```text
avg:myapp.response_time{*}
```

Add another widget.

Query:

```text
avg:myapp.active_users{*}
```

Now your dashboard contains both graphs.

Example:

```text
-----------------------------------------------------
| Response Time              | Active Users          |
|                            |                       |
|    /\                      |      /\              |
|   /  \                     |     /  \             |
|__/____\_______             |____/____\_____       |
-----------------------------------------------------
```


# Step 4: Filter Using Tags

In your code you added tags:

```javascript
tags: [
   "env:dev",
   "service:web"
]
```

Now you can query:

```text
avg:myapp.response_time{env:dev}
```

or

```text
avg:myapp.response_time{service:web}
```

This is extremely useful when the same application runs in multiple environments like Dev, QA, and Production.


# Step 5: Create an Alert (Monitor)

Go to:

```text
Monitors
     ↓
Create Monitor
```

Choose:

```text
Metric
```

Query:

```text
avg(last_5m):avg:myapp.response_time{*} > 300
```

Meaning:

> If the average response time over the last 5 minutes exceeds **300 ms**, trigger an alert.

You can configure notifications through:

* Email
* Slack
* Microsoft Teams
* PagerDuty
* Opsgenie


# End-to-End Flow

```text
                AWS EKS

+-----------------------------+
|      Node.js Application    |
|                             |
|  sendMetrics()              |
|         │                   |
|         ▼                   |
|  Datadog Metrics API        |
+-----------------------------+
            │
            ▼
      Datadog Cloud
            │
            ├── Metrics Explorer
            ├── Dashboards
            ├── Monitors
            └── Alerts
```


# Real-Time Example

Suppose your application sends:

```javascript
Metric 1

myapp.response_time = 180 ms
```

```javascript
Metric 2

myapp.active_users = 75
```

Every minute, new values are sent:

| Time  | Response Time | Active Users |
| ----- | ------------- | ------------ |
| 10:00 | 180           | 75           |
| 10:01 | 175           | 77           |
| 10:02 | 210           | 80           |
| 10:03 | 190           | 79           |

Datadog stores these data points and displays them as time-series graphs on your dashboard. You can also create monitors to notify your team if response time becomes too high or active users suddenly drop.


## Interview Answer (2 Minutes)

> "After my Node.js application sends custom metrics to the Datadog Metrics API, Datadog stores them as time-series data. I can verify that the metrics are arriving by opening **Metrics Explorer** and searching for the metric names, such as `myapp.response_time` or `myapp.active_users`. To visualize them, I create a dashboard with Timeseries widgets and use queries like `avg:myapp.response_time{*}` or filter them by tags such as `env:dev` or `service:web`. Finally, I can create monitors that trigger alerts when a metric crosses a threshold—for example, if the average response time exceeds 300 ms over the last five minutes."

---
