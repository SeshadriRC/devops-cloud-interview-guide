# Grafana Dashboard Creation

Write a script in Python using the Grafana API to create a new dashboard. The dashboard should include at least three panels displaying metrics from your AWS CloudWatch data sources. Each panel should represent a different metric. Consider the following steps:

- Set up API access to Grafana. Define the dashboard layout using JSON.
- Use the appropriate API call to create the dashboard.

Skills Evaluated:

· Python programming
· API integration
· Grafana dashboard creation

---

This question tests:

* Python programming
* REST API integration
* Grafana HTTP API
* Dashboard JSON structure
* AWS CloudWatch datasource knowledge

The interviewer expects you to automate dashboard creation instead of creating it manually through the Grafana UI.

---

# Scenario

Suppose your architecture is:

```text
AWS
│
├── EC2
├── RDS
├── ALB
└── CloudWatch
        │
        ▼
Grafana
        │
        ▼
Python Script
        │
        ▼
Grafana REST API
```

CloudWatch is already configured as a **Grafana Data Source**.

Your Python script simply tells Grafana:

> "Create a dashboard with three graphs."

---

# Step 1: Generate a Grafana API Token

In Grafana:

```
Administration
      ↓
Users and Access
      ↓
Service Accounts
      ↓
Create Token
```

Example token:

```text
eyJrIjoiYWJjZGVmMTIzNDU2...
```

We'll use this token for authentication.

---

# Step 2: Install Python Requests Library

```bash
pip install requests
```

---

# Step 3: Python Script

```python
import requests
import json

GRAFANA_URL = "http://grafana.example.com:3000/api/dashboards/db"

API_TOKEN = "YOUR_GRAFANA_API_TOKEN"

headers = {
    "Authorization": f"Bearer {API_TOKEN}",
    "Content-Type": "application/json"
}

dashboard = {
    "dashboard": {
        "id": None,
        "title": "AWS CloudWatch Dashboard",
        "timezone": "browser",
        "schemaVersion": 38,
        "version": 0,
        "panels": [

            {
                "id": 1,
                "title": "EC2 CPU Utilization",
                "type": "timeseries",
                "datasource": {
                    "type": "cloudwatch",
                    "uid": "YOUR_CLOUDWATCH_UID"
                },
                "targets": [
                    {
                        "namespace": "AWS/EC2",
                        "metricName": "CPUUtilization",
                        "region": "ap-south-1"
                    }
                ]
            },

            {
                "id": 2,
                "title": "RDS CPU Utilization",
                "type": "timeseries",
                "datasource": {
                    "type": "cloudwatch",
                    "uid": "YOUR_CLOUDWATCH_UID"
                },
                "targets": [
                    {
                        "namespace": "AWS/RDS",
                        "metricName": "CPUUtilization",
                        "region": "ap-south-1"
                    }
                ]
            },

            {
                "id": 3,
                "title": "Application Load Balancer Requests",
                "type": "timeseries",
                "datasource": {
                    "type": "cloudwatch",
                    "uid": "YOUR_CLOUDWATCH_UID"
                },
                "targets": [
                    {
                        "namespace": "AWS/ApplicationELB",
                        "metricName": "RequestCount",
                        "region": "ap-south-1"
                    }
                ]
            }

        ]
    },

    "overwrite": True
}

response = requests.post(
    GRAFANA_URL,
    headers=headers,
    data=json.dumps(dashboard)
)

if response.status_code == 200:

    print("Dashboard created successfully")

else:

    print("Failed")

    print(response.status_code)

    print(response.text)
```

---

# Understanding the Script

## Authentication

```python
headers = {
    "Authorization": f"Bearer {API_TOKEN}"
}
```

Grafana authenticates the request using the API token.

---

## Dashboard Title

```python
"title": "AWS CloudWatch Dashboard"
```

This will be the dashboard name in Grafana.

---

## Panel 1

```python
"title": "EC2 CPU Utilization"
```

Uses

```
Namespace

AWS/EC2
```

Metric

```
CPUUtilization
```

---

## Panel 2

```
AWS/RDS

CPUUtilization
```

Shows database CPU usage.

---

## Panel 3

```
AWS/ApplicationELB

RequestCount
```

Shows how many requests the load balancer is receiving.

---

## API Call

```python
requests.post()
```

calls

```
POST

/api/dashboards/db
```

Grafana creates the dashboard.

---

# Dashboard Created

You'll see something like:

```
----------------------------------------------------------
| EC2 CPU Utilization                                    |
|                                                        |
|     /\      /\                                         |
|____/  \____/  \________________________                |
----------------------------------------------------------

----------------------------------------------------------
| RDS CPU Utilization                                    |
|                                                        |
|   /\                                                   |
|__/  \_______                                           |
----------------------------------------------------------

----------------------------------------------------------
| ALB Request Count                                      |
|                                                        |
|      /\                                                |
|_____/  \__________                                     |
----------------------------------------------------------
```

---

# End-to-End Flow

```text
CloudWatch
      │
      ▼
Grafana Data Source
      ▲
      │
Python Script
      │
POST /api/dashboards/db
      │
      ▼
Grafana
      │
      ▼
Dashboard Created
```

---

# Interview Explanation (2 Minutes)

> "First, I would configure AWS CloudWatch as a data source in Grafana and generate a Grafana API token for authentication. In my Python script, I would use the `requests` library to call Grafana's REST API. The dashboard definition is provided as a JSON payload containing the dashboard metadata and multiple panels. Each panel is configured to use the CloudWatch data source and query a different AWS metric, such as EC2 CPU utilization, RDS CPU utilization, and Application Load Balancer request count. The script authenticates using the Bearer token and sends a POST request to the `/api/dashboards/db` endpoint. If the request succeeds, Grafana creates the dashboard automatically. This approach is useful for Infrastructure as Code because dashboards can be version-controlled and created consistently across environments."

---



