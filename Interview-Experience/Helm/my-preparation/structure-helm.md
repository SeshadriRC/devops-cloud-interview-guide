# Helm Chart Directory Structure & Best Practices

In DevOps and Kubernetes interviews, interviewers frequently ask you to describe the anatomy of a Helm chart, how templates are evaluated, and how configuration is structured in enterprise deployments.

---

## 1. Standard Helm Chart Directory Structure

When you create a chart with `helm create mychart`, Helm creates the following canonical layout:

```text
mychart/
├── Chart.yaml              # Metadata about the chart (name, version, description)
├── values.yaml             # Default configuration values for templates
├── values.schema.json      # (Optional) JSON schema to validate values.yaml inputs
├── charts/                 # Subcharts / Chart dependencies
├── templates/              # Kubernetes resource template manifests
│   ├── _helpers.tpl        # Named templates and reusable template snippets
│   ├── deployment.yaml     # Kubernetes Deployment definition
│   ├── service.yaml        # Kubernetes Service definition
│   ├── ingress.yaml        # Kubernetes Ingress definition
│   ├── serviceaccount.yaml # ServiceAccount definition
│   ├── hpa.yaml            # HorizontalPodAutoscaler definition
│   ├── configmap.yaml      # ConfigMap definition
│   ├── secret.yaml         # Secret template
│   ├── NOTES.txt           # Help text printed to user after `helm install`
│   └── tests/              # Test pods executed via `helm test`
│       └── test-connection.yaml
├── .helmignore             # Files and patterns to ignore during packaging
└── README.md               # Documentation for chart consumers
```

---

## 2. Key File Breakdown

### 1. `Chart.yaml` (Chart Metadata)
Contains top-level chart definitions:
```yaml
apiVersion: v2
name: payment-service
description: Helm chart for deploying the Payment Microservice
type: application # 'application' or 'library'
version: 1.2.0    # Chart version (SemVer)
appVersion: "2.4.1" # The version of the application container image
maintainers:
  - name: Seshadri
    email: devops@example.com
dependencies:
  - name: redis
    version: 17.3.1
    repository: https://charts.bitnami.com/bitnami
    condition: redis.enabled
```

### 2. `values.yaml` (Default Values)
Defines the parameters that consumers can customize:
```yaml
replicaCount: 2

image:
  repository: 123456789012.dkr.ecr.us-east-1.amazonaws.com/payment-service
  pullPolicy: IfNotPresent
  tag: "v2.4.1"

service:
  type: ClusterIP
  port: 8080

resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi

autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
```

### 3. `templates/_helpers.tpl` (Template Partials)
Contains reusable Go template helpers, usually prefixed with the chart name to avoid collisions:
```yaml
{{/*
Expand the name of the chart.
*/}}
{{- define "payment-service.name" -}}
{{- default .Chart.Name .Values.nameOverride | trunc 63 | trimSuffix "-" }}
{{- end }}

{{/*
Create standard labels for resources.
*/}}
{{- define "payment-service.labels" -}}
helm.sh/chart: {{ include "payment-service.name" . }}-{{ .Chart.Version }}
app.kubernetes.io/name: {{ include "payment-service.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}
```

### 4. `templates/deployment.yaml` (Template Manifest)
Consumes `.Values` and `.Release` objects:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "payment-service.name" . }}
  labels:
    {{- include "payment-service.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "payment-service.name" . }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "payment-service.name" . }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - containerPort: {{ .Values.service.port }}
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

---

## 3. Helm Built-In Objects Quick Reference

| Object | Description | Example |
| :--- | :--- | :--- |
| **`.Values`** | Values passed from `values.yaml` or CLI `--set` | `{{ .Values.image.tag }}` |
| **`.Release`** | Information about the current release | `{{ .Release.Name }}`, `{{ .Release.Namespace }}` |
| **`.Chart`** | Data from `Chart.yaml` | `{{ .Chart.Name }}`, `{{ .Chart.Version }}` |
| **`.Files`** | Access to non-template files in the chart | `{{ .Files.Get "config.json" }}` |
| **`.Capabilities`**| Kubernetes cluster versions and API versions | `{{ .Capabilities.KubeVersion }}` |

---

## 4. Multi-Environment Pattern in Production

In real-world production projects, do not modify `values.yaml` directly for each environment. Maintain separate environment overlay values files:

```text
helm upgrade --install payment-svc ./mychart \
  -f ./mychart/values.yaml \
  -f ./environments/values-prod.yaml \
  --namespace production
```

---

## 5. Essential Helm Commands for Interviews

```bash
# Validate chart syntax
helm lint ./mychart

# Render templates locally without contacting cluster (dry-run)
helm template myapp ./mychart -f values-prod.yaml

# Install or upgrade
helm upgrade --install myapp ./mychart -n default --wait

# View deployment history and revisions
helm history myapp -n default

# Rollback to revision 2
helm rollback myapp 2 -n default
```
