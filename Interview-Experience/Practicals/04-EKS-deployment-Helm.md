## EKS Deployment with Helm

Create a Helm chart for deploying a simple web application on AWS EKS. Your chart should allow custom configuration of the number of replicas and the application version.
Include the following elements in Deployment configuration, Service definition for exposing the application. Values.yaml file for customization
Skills Evaluated:
· Kubernetes with EKS
· Helm chart development
· Microservices architecture


----

This is another common **DevOps/Platform Engineer** interview question. The interviewer wants to check whether you know:

* Kubernetes basics
* Helm chart structure
* How applications are deployed on EKS
* How to make Helm charts reusable using `values.yaml`

Let's build it from scratch.

---

# Step 1: Understand the Requirement

You need to deploy a simple web application on **AWS EKS** using **Helm**.

The interviewer specifically asks for:

✅ Deployment configuration

✅ Service configuration

✅ `values.yaml`

where the **replica count** and **application version (Docker image tag)** should be customizable.

---

# What is Helm?

Normally in Kubernetes, you create several YAML files.

For example:

```text
deployment.yaml
service.yaml
configmap.yaml
secret.yaml
ingress.yaml
```

If you want to deploy the same application again, you edit these files manually.

Helm solves this by allowing you to create **templates** and provide values separately.

Think of it as:

```text
Helm = Package Manager for Kubernetes

Similar to

apt install nginx
yum install httpd
```

Except Helm installs Kubernetes applications.

---

# Helm Chart Structure

```text
my-web-app/
│
├── Chart.yaml
├── values.yaml
│
└── templates/
      │
      ├── deployment.yaml
      └── service.yaml
```

---

# Chart.yaml

This file contains chart metadata.

```yaml
apiVersion: v2
name: my-web-app
description: Simple Node.js application
version: 0.1.0
appVersion: "1.0"
```

---

# values.yaml

This is where customization happens.

```yaml
replicaCount: 3

image:
  repository: myrepo/node-app
  tag: "1.0.0"

service:
  type: LoadBalancer
  port: 80
```

If tomorrow you want **5 replicas** instead of **3**, you only edit:

```yaml
replicaCount: 5
```

If a new Docker image is available:

```yaml
tag: "2.0.0"
```

No template changes are needed.

---

# deployment.yaml

Notice the placeholders enclosed in `{{ }}`.

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: my-web-app

spec:
  replicas: {{ .Values.replicaCount }}

  selector:
    matchLabels:
      app: my-web-app

  template:

    metadata:
      labels:
        app: my-web-app

    spec:

      containers:

      - name: node-app

        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"

        ports:
        - containerPort: 3000
```

Helm replaces these placeholders with values from `values.yaml`.

---

# service.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: my-web-app

spec:

  selector:
    app: my-web-app

  ports:

  - port: {{ .Values.service.port }}

    targetPort: 3000

  type: {{ .Values.service.type }}
```

This creates a Kubernetes Service to expose the application.

Since the service type is `LoadBalancer`, on EKS AWS automatically provisions an **Elastic Load Balancer** and assigns a public endpoint.

---

# Installing the Chart

```bash
helm install node-app ./my-web-app
```

Helm reads:

* Chart.yaml
* values.yaml
* deployment.yaml
* service.yaml

It substitutes the template values and sends the resulting Kubernetes manifests to the EKS cluster.

---

# Updating Values

Suppose you need:

* 6 replicas
* New image version

Update `values.yaml`:

```yaml
replicaCount: 6

image:
  repository: myrepo/node-app
  tag: "2.0.0"
```

Run:

```bash
helm upgrade node-app ./my-web-app
```

Helm performs a rolling update of the deployment.

---

# Deployment Flow

```text
Developer
      │
      ▼
Helm Chart
      │
      ├── Chart.yaml
      ├── values.yaml
      └── templates
              │
              ├── deployment.yaml
              └── service.yaml
      │
      ▼
helm install
      │
      ▼
Amazon EKS
      │
      ├── Deployment
      ├── Pods
      ├── Service
      └── Load Balancer
```

---

# How EKS Fits In

Remember:

* **EKS** is the managed Kubernetes control plane provided by AWS.
* Helm doesn't deploy directly to AWS services; it deploys Kubernetes resources to the EKS cluster.
* When the Service type is `LoadBalancer`, Kubernetes on EKS asks AWS to provision an **Elastic Load Balancer** automatically.

---

# Interview Answer (2–3 Minutes)

> "I would create a Helm chart with a standard structure containing `Chart.yaml`, `values.yaml`, and templates for the Deployment and Service. In the Deployment template, I would use Helm variables such as `{{ .Values.replicaCount }}` for the number of replicas and `{{ .Values.image.repository }}:{{ .Values.image.tag }}` for the container image, making these configurable through `values.yaml`. The Service template would expose the application, typically using the `LoadBalancer` type on EKS so AWS automatically provisions an external load balancer. To deploy, I would run `helm install`, and for future changes such as scaling replicas or updating the application version, I would modify `values.yaml` and use `helm upgrade`. This approach keeps the deployment reusable, parameterized, and easy to maintain across different environments.`


