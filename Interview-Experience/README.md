# DevOps & Cloud Engineer Interview Preparation Portal

Welcome to the **DevOps & Cloud Engineer Interview Preparation Guide**. This portal is structured to help you systematically prepare for technical interviews, system design discussions, and live troubleshooting screens covering **AWS, OpenShift, Kubernetes, Terraform, CI/CD (Jenkins & GitHub Actions), Helm, Ansible, Docker, Linux, Observability, and Scripting**.

---

## 🧭 Topic-Wise Preparation Directory

| Topic | Key Focus Areas | Quick Link |
| :--- | :--- | :--- |
| 🚀 **CI/CD & Jenkins** | Declarative pipelines, approval gates, SonarQube, release flow, secrets masking | [Explore CI/CD](CI-CD/Readme.md) |
| ☸️ **OpenShift & Kubernetes** | OVN-Kubernetes CNI, cross-namespace networking, CPU throttling, memory leaks, IRSA | [Explore OpenShift](Openshift/Readme.md) |
| 🏗️ **Terraform (IaC)** | State locking, workspaces, drift detection, provider files, lifecycle rules, recovery | [Explore Terraform](Terraform/Readme.md) |
| 📜 **Ansible** | Ad-hoc commands (`-m`), variable precedence, idempotent playbooks, Vault, limits | [Explore Ansible](Ansible/Readme.md) |
| 🐳 **Docker** | Multi-stage builds, CMD vs ENTRYPOINT, layer optimization, non-root security | [Explore Docker](Docker/Readme.md) |
| ☁️ **AWS Cloud** | High availability multi-AZ design, private RDS access via SSM, VPC Peering vs TGW | [Explore AWS](AWS/Readme.md) |
| 🐙 **ArgoCD & GitOps** | Application Controller, Dex SSO, Image Updater, push vs pull deployment | [Explore ArgoCD](ArgoCD/Readme.md) |
| 📊 **Observability** | Three pillars (Metrics/Logs/Traces), Datadog custom metrics, Prometheus, CloudWatch | [Explore Observability](Observability/README.md) |
| 🔬 **Dynatrace APM** | PurePath distributed tracing, Davis AI causation engine, OneAgent architecture | [Explore Dynatrace](Dynatrace/README.md) |
| 🐧 **Linux Administration** | Memory leaks, zombie processes, load average, system calls (`readdir`), `sudo` vs `su` | [Explore Linux](Linux/README.md) |
| 🌿 **Git Version Control** | Refs (`HEAD`, branches, tags), `git pull` vs `rebase`, log searching, conflict resolution | [Explore Git](Git/README.md) |
| 💻 **Shell Scripting** | Strict mode (`set -euo pipefail`), variable quoting, `mktemp`, error parsing | [Explore Shell Scripting](Shell-Scripting/README.md) |
| 🐍 **Python Automation** | Log parsing, REST API automation, algorithmic problem solving | [Explore Python](Python/README.md) |
| 🛠️ **Practical Challenges** | 8 Real-world hands-on coding and deployment scenarios | [Explore Practicals](Practicals/README.md) |

---

## 🎯 Top 15 Real-World DevOps Interview Questions & Model Answers

### 1. What is the CI/CD process in your project?

In enterprise environments (e.g. AWS + OpenShift / Kubernetes), our CI/CD pipeline operates as follows:

```text
[ Developer Push ] ──Webhook──► [ Jenkins / GitHub Actions ]
                                        │
                         ┌──────────────┴──────────────┐
                         ▼                             ▼
                 [ 1. Unit Tests ]             [ 2. SonarQube SAST ]
                         │                             │
                         └──────────────┬──────────────┘
                                        ▼
                             [ 3. Docker Multi-Stage Build ]
                                        │
                                        ▼
                             [ 4. Trivy / Snyk Scan ]
                                        │
                                        ▼
                             [ 5. Push to Registry (ECR/Harbor) ]
                                        │
                                        ▼
                             [ 6. Update Helm Values (GitOps) ]
                                        │
                                        ▼
                             [ 7. Deploy to K8s/OpenShift ]
                                        │
                         ┌──────────────┴──────────────┐
                         ▼                             ▼
                 [ Rolling Update ]            [ Smoke Tests / Health Checks ]
                         │                             │
                         └──────────────┬──────────────┘
                                        ▼
                             [ Prometheus / Datadog Monitoring ]
```

1. **Code Commit**: Developer pushes code to a feature branch in Git.
2. **Trigger**: Git webhook triggers the Jenkins declarative pipeline.
3. **Build & Test**: Jenkins checks out code, executes unit tests (Maven/NPM/pytest), and runs static analysis with SonarQube quality gates.
4. **Containerization**: A multi-stage Docker build produces a lean runtime image. Security scanner (Trivy) checks for CVEs.
5. **Publish**: Verified image is tagged with Git commit SHA and pushed to AWS ECR or Harbor.
6. **Deployment**: Helm charts or ArgoCD synchronize the deployment manifest to Kubernetes/OpenShift.
7. **Verification**: Kubernetes executes a zero-downtime rolling update with readiness/liveness probes, followed by automated smoke tests.
8. **Observability**: Metrics and logs are streamed to Datadog / Prometheus / CloudWatch.

---

### 2. What is your Git branching and release candidate strategy?

We follow a modified **Trunk-Based / GitHub Flow** strategy with Release Candidates (RC):

1. **`feature/*` branches**: Branched from `main` (or `develop`). Developers push small, frequent commits.
2. **Pull Requests**: Merging requires 2 peer approvals, passing CI tests, and green SonarQube quality gates.
3. **Release Candidates (`v1.2.0-rc1`)**: Tagged when code is ready for staging. Staging runs end-to-end integration tests and UAT (User Acceptance Testing).
4. **Production Tag (`v1.2.0`)**: Once approved by QA and Product owners, the RC tag is promoted to a production release tag and deployed during the approved release window.
5. **Hotfixes (`hotfix/*`)**: Branched directly from the production tag for urgent fixes, merged back into both `main` and active release branches.

---

### 3. How do you handle zero-downtime deployments and rollbacks?

* **Zero-Downtime Deployment**:
  - We use Kubernetes **Rolling Updates** configured with `maxSurge: 25%` and `maxUnavailable: 0`.
  - Every pod has configured `readinessProbe` and `livenessProbe` so traffic is only routed to pods that are fully warmed up.
* **Rollback Strategy**:
  - **Kubernetes / Helm**: If error rates spike post-deployment, we trigger an immediate rollback via `helm rollback <release> <revision>` or `kubectl rollout undo deployment/<name>`.
  - **ArgoCD / GitOps**: Revert the Git commit (`git revert <commit-SHA>`) and sync.
  - **Database Migrations**: We write forward-compatible database migrations (Expand & Contract pattern) so rolling back application code does not break the database schema.

---

### 4. Explain Jenkins architecture and Declarative Pipelines.

Jenkins uses a **Distributed Master-Agent (Controller-Node) Architecture**:

```text
               ┌──────────────────────────────────────────────┐
               │          Jenkins Controller (Master)         │
               │  - Web UI & API                              │
               │  - Job Scheduling & Pipeline Parsing         │
               │  - Secret Storage & Plugins                  │
               └──────────────┬───────────────────────────────┘
                              │ (Dispatches builds via JNLP / SSH)
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│ K8s Dynamic Pod  │ │ EC2 Build Agent  │ │ Linux Agent      │
│ Agent (Ephemeral)│ │ (Persistent)     │ │ (Static)         │
└──────────────────┘ └──────────────────┘ └──────────────────┘
```

* **Jenkins Controller**: Manages the schedule, web UI, credentials, and plugins. It does **not** run resource-heavy builds.
* **Dynamic Agents**: In our setup, Jenkins spawns ephemeral build pods inside OpenShift/Kubernetes on-demand using the Kubernetes plugin. When the build completes, the pod terminates, saving cloud costs.
* **Declarative Pipeline**: Defined in a version-controlled `Jenkinsfile` using a strict, structured syntax (`pipeline { agent { ... } stages { ... } post { ... } }`), providing syntax validation and cleaner readability compared to legacy scripted pipelines.

---

### 5. How would you implement DevOps practices in a project with manual deployments?

I would implement modern DevOps in **5 structured phases**:

1. **Phase 1: Source Control & Versioning**: Migrate all source code, configurations, and scripts to Git. Implement branch protection and commit standards.
2. **Phase 2: Continuous Integration (CI)**: Set up Jenkins or GitHub Actions. Automate compilation, unit tests, and code quality scanning (SonarQube) on every pull request.
3. **Phase 3: Containerization & Artifact Storage**: Containerize apps using Docker multi-stage builds. Store versioned images in AWS ECR or Harbor registry.
4. **Phase 4: Infrastructure as Code (IaC)**: Replace manual server provisioning with Terraform modules and remote S3 state backends with DynamoDB locking.
5. **Phase 5: Automated CD & Observability**: Deploy applications via Helm / ArgoCD using rolling updates. Add centralized logging (CloudWatch/ELK) and application monitoring (Datadog/Dynatrace).

---

### 6. What is Terraform drift?

**Terraform Drift** occurs when the actual state of cloud infrastructure deviates from the state defined in your Terraform code or Terraform state file.

* **Causes**:
  - Engineers modifying, stopping, or creating resources manually via the AWS Management Console or AWS CLI.
  - Out-of-band updates by emergency incident responders.
  - Automatic changes made by cloud provider background services or auto-scaling.
* **Impact**: Can lead to unpredictable outages, security vulnerabilities, or accidental deletion of unmanaged configurations during subsequent `terraform apply` runs.

---

### 7. How do you detect and overcome Terraform drift?

1. **Drift Detection**:
   - Run `terraform plan` or `terraform plan -refresh-only`. Terraform queries live cloud APIs, refreshes state, and displays differences.
   - Run scheduled CI/CD drift detection pipelines (e.g. nightly cron jobs) alerting via Slack if drift is found.
2. **Drift Remediation**:
   - **Option A (Revert to Desired State)**: Run `terraform apply`. Terraform will overwrite the manual console changes and bring the cloud infrastructure back into compliance with code.
   - **Option B (Adopt the Live Change)**: If the manual change was an approved hotfix, update your Terraform `.tf` code to match the new configuration, then run `terraform apply -refresh-only` to update state without resource destruction.
   - **Prevention**: Enforce least-privilege IAM permissions. Restrict human console access to read-only; mandate that all infrastructure modifications happen exclusively through CI/CD pipelines.

---

### 8. What is a Data Source in Terraform?

A **Data Source** allows Terraform to query and consume information about resources defined **outside** of the current Terraform configuration.

* **Characteristics**: Read-only; does not create, manage, or delete resources.
* **Example**:
  ```hcl
  # Lookup latest official Amazon Linux 2023 AMI
  data "aws_ami" "amazon_linux" {
    most_recent = true
    owners      = ["amazon"]

    filter {
      name   = "name"
      values = ["al2023-ami-*-x86_64"]
    }
  }

  # Lookup existing VPC by tag
  data "aws_vpc" "existing_vpc" {
    tags = {
      Environment = "production"
    }
  }
  ```

---

### 9. What is a Terraform Workspace?

A **Terraform Workspace** allows managing multiple distinct state files using the exact same Terraform configuration code.

* By default, Terraform uses the `default` workspace.
* Creating new workspaces (`dev`, `uat`, `prod`) stores each workspace's state under a distinct prefix (e.g., `env:/dev/terraform.tfstate` in S3).
* **CLI Commands**:
  ```bash
  terraform workspace list
  terraform workspace new dev
  terraform workspace select prod
  ```
* **Best Practice Note**: Workspaces are great for testing identical ephemeral environments. For production vs non-production enterprise setups, separate directory structures or separate AWS accounts are preferred to prevent accidental cross-environment blast radius.

---

### 10. What are dependencies in Terraform?

Dependencies govern the order in which Terraform provisions or destroys infrastructure resources:

* **Implicit Dependencies**: Terraform automatically creates a directed acyclic graph (DAG) by detecting resource references.
  ```hcl
  resource "aws_instance" "web" {
    subnet_id = aws_subnet.public.id # Implicit dependency: subnet must be created first
  }
  ```
* **Explicit Dependencies**: Specified manually using the `depends_on` meta-argument when Terraform cannot automatically detect the dependency (e.g., an EC2 instance that relies on an S3 bucket or IAM policy attachment).
  ```hcl
  resource "aws_instance" "web" {
    depends_on = [
      aws_iam_role_policy_attachment.app_attach
    ]
  }
  ```

---

### 11. A Linux server with a 500 GB volume provisioned via Terraform needs to increase to 750 GB. How do you perform this change?

To expand storage without data loss or server downtime:

1. **Step 1: Update Terraform Code**:
   Modify the volume size argument in the Terraform configuration:
   ```hcl
   volume_size = 750 # Changed from 500
   ```
2. **Step 2: Apply in Terraform**:
   ```bash
   terraform plan
   terraform apply
   ```
   *Verify AWS modifies the EBS volume size online using Elastic Volumes (no reboot required).*
3. **Step 3: SSH into the Linux Instance & Inspect**:
   ```bash
   lsblk
   df -h
   ```
   *(Notice the block device shows 750G, but the partition and filesystem still show 500G).*
4. **Step 4: Grow the Partition**:
   ```bash
   # Grow partition 1 on device /dev/nvme0n1
   sudo growpart /dev/nvme0n1 1
   ```
5. **Step 5: Expand the Filesystem**:
   - For **XFS**:
     ```bash
     sudo xfs_growfs /mount_point
     ```
   - For **ext4**:
     ```bash
     sudo resize2fs /dev/nvme0n1p1
     ```
6. **Step 6: Verify**:
   ```bash
   df -h
   ```
   *Confirm the filesystem now reflects 750 GB.*

---

### 12. In a 3-node Kubernetes cluster, one node is not receiving traffic. How do you troubleshoot and fix it?

Follow this systematic troubleshooting tree:

```text
[ 1. Check Node Status ]
kubectl get nodes -o wide
     ├── Status: NotReady ──► Check kubelet & container runtime on node
     └── Status: Ready ────► Check scheduling, pods, and network
                                  │
                                  ▼
[ 2. Check Scheduling & Taints ]
kubectl describe node <problem-node> | grep -i taints
kubectl get pods -o wide | grep <problem-node>
  - Is the node Cordoned? (SchedulingDisabled) ──► kubectl uncordon <node>
  - Are there NoSchedule taints?
                                  │
                                  ▼
[ 3. Inspect Service Endpoints ]
kubectl get endpoints <service-name>
  - Are the pod IPs on the problem node registered in Endpoints?
                                  │
                                  ▼
[ 4. Check CNI & Network Health ]
kubectl get pods -n kube-system -o wide
  - Is the CNI DaemonSet pod (OVN/Calico/AWS-VPC-CNI) Running & Healthy on that node?
                                  │
                                  ▼
[ 5. Node System Checks via SSH ]
sudo systemctl status kubelet
journalctl -u kubelet -e --no-pager
sudo iptables -L -n -v (or OVN flow table)
```

---

### 13. What is a CRD (Custom Resource Definition) in Kubernetes?

A **CRD (Custom Resource Definition)** extends the Kubernetes API by allowing you to define your own custom resources beyond built-in primitives (`Pod`, `Service`, `Deployment`).

* When you register a CRD, the Kubernetes API Server creates a new RESTful endpoint for it.
* A custom **Operator / Controller** watches this custom endpoint and continuously reconciles the live state of that resource.
* **Example**:
  ```yaml
  apiVersion: postgresql.cnpg.io/v1
  kind: Cluster
  metadata:
    name: prod-postgres-db
  spec:
    instances: 3
    storage:
      size: 100Gi
  ```
  The CloudNativePG operator reads this CRD and automatically provisions StatefulSets, PVCs, Secret credentials, and streaming replication.

---

### 14. What is CNI (Container Network Interface)?

**CNI (Container Network Interface)** is a CNCF specification and library that defines how third-party network plugins interact with container runtimes (containerd, CRI-O) to configure networking for Linux containers.

* **Core Responsibilities**:
  1. Dynamically allocate IP addresses to newly created pods (IPAM - IP Address Management).
  2. Create virtual ethernet pairs (`veth`) and attach pods to the host network namespace.
  3. Enable cross-node pod-to-pod communication without Network Address Translation (NAT).
  4. Enforce Kubernetes `NetworkPolicy` ingress and egress firewall rules.

---

### 15. Which CNI plugin have you used, and how is it implemented?

**OpenShift (OVN-Kubernetes)**:
> *"In our OpenShift enterprise clusters, we use the default **OVN-Kubernetes** (Open Virtual Network) CNI plugin. It runs as a DaemonSet across all master and worker nodes in the `openshift-ovn-kubernetes` namespace. It provides native overlay networking using Geneve tunnels, distributed load balancing, egress IPs, and native network policy enforcement. When troubleshooting connectivity, we inspect CNI pod health with `oc get pods -n openshift-ovn-kubernetes`, check node connectivity via Geneve UDP port 6081, and inspect OVS OpenFlow tables using `ovs-ofctl`."*

**AWS EKS (AWS VPC CNI)**:
> *"In AWS EKS environments, we use the **AWS VPC CNI plugin** (`amazon-vpc-cni-k8s`). It assigns secondary private IPv4 addresses directly from the VPC subnet to pods via Elastic Network Interfaces (ENIs). This provides native VPC routing performance and allows direct pod connectivity without overlay encapsulation."*
