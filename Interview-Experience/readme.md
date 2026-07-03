These are common DevOps interview questions. Since your background is in AWS, OpenShift, Kubernetes, Terraform, Helm, and CI/CD, here are interview-ready answers.

---

## 1. What is the CI/CD process in your project?

In my project, the CI/CD pipeline works like this:

1. Developer pushes code to Git.
2. Jenkins detects the code change using a webhook.
3. Jenkins checks out the source code.
4. Unit tests and static code analysis are executed.
5. Docker image is built.
6. The image is pushed to a Docker registry (such as ECR or Harbor).
7. Helm charts are updated with the new image version.
8. Jenkins deploys the application to OpenShift/Kubernetes using Helm.
9. Kubernetes performs a rolling update without downtime.
10. Post-deployment smoke tests and health checks are executed.
11. Monitoring is done using Prometheus, Grafana, CloudWatch, or Datadog.


## 4. Explain Jenkins.

Jenkins is an automation server used to implement CI/CD.

It automates:

* Build
* Test
* Docker image creation
* Infrastructure deployment
* Kubernetes deployment
* Notifications

Pipeline example:

```
Git Push
   ↓
Jenkins
   ↓
Build
   ↓
Test
   ↓
Docker Build
   ↓
Push Image
   ↓
Deploy using Helm
```

---

## 5. How would you implement DevOps practices in a project that currently has manual deployments?

I would implement it step by step:

1. Move source code to Git.
2. Create branching strategy.
3. Create Jenkins pipeline.
4. Automate build process.
5. Containerize using Docker.
6. Store images in a registry.
7. Use Terraform for infrastructure.
8. Use Helm for Kubernetes deployments.
9. Configure rolling updates.
10. Implement monitoring and logging.
11. Automate testing.
12. Implement approval gates for Production.

Result:

* Faster deployments
* Fewer manual errors
* Easy rollback
* Consistency

---

## 6. What is Terraform drift?


## 7. How do you overcome Terraform drift?


---

## 8. What is a data source in Terraform?




---

## 9. What is a Terraform workspace?

A workspace allows multiple state files using the same Terraform configuration.

Example:

```
default

dev

uat

prod
```

Each workspace has its own Terraform state.

Commands:

```bash
terraform workspace list

terraform workspace new dev

terraform workspace select prod
```

---

## 10. What are dependencies in Terraform?

Dependencies determine the order in which resources are created.

Implicit dependency:

```hcl
resource "aws_instance" "web" {
  subnet_id = aws_subnet.public.id
}
```

Terraform automatically knows the subnet must be created first.

Explicit dependency:

```hcl
depends_on = [
  aws_security_group.web
]
```

Used when Terraform cannot automatically determine the dependency.

---

## 11. A Linux server with a 500 GB data volume was provisioned using Terraform. The application team wants to increase it to 750 GB. How would you perform this change?

Steps:

1. Update Terraform:

```hcl
volume_size = 750
```

2. Run:

```bash
terraform plan
```

Verify only the volume size changes.

3. Apply:

```bash
terraform apply
```

4. Verify the EBS volume size in AWS.

5. SSH into the server.

6. Extend the partition.

Example:

```bash
sudo growpart /dev/nvme0n1 1
```

7. Resize the filesystem.

For XFS:

```bash
sudo xfs_growfs /
```

For ext4:

```bash
sudo resize2fs /dev/nvme0n1p1
```

8. Verify:

```bash
df -h
```

---

## 12. You have three nodes, and one node is not receiving traffic. How would you identify the problematic node, troubleshoot it, and fix the issue?

Steps:

1. Check node status:

```bash
kubectl get nodes
```

2. Check pods:

```bash
kubectl get pods -o wide
```

3. Verify which node is not hosting pods.

4. Check if the node is Ready:

```bash
kubectl describe node <node-name>
```

5. Check taints:

```bash
kubectl describe node
```

6. Check kubelet:

```bash
systemctl status kubelet
```

7. Check CNI:

```bash
kubectl get pods -n kube-system
```

8. Check logs:

```bash
journalctl -u kubelet
```

9. Verify network connectivity and firewall rules.

10. If the node is cordoned:

```bash
kubectl uncordon <node>
```

11. Restart kubelet if required.

12. Verify pods are scheduled and traffic resumes.

---

## 13. What is a CRD (Custom Resource Definition) in Kubernetes?

A **CRD** extends Kubernetes by allowing you to create your own resource types.

Instead of only using built-in resources like:

* Pod
* Deployment
* Service

you can define custom resources.

Example:

```yaml
kind: PostgreSQL
```

An operator watches this custom resource and automatically creates the required components, such as StatefulSets, Services, and PersistentVolumes.

In your project, operators use CRDs to provision services like PostgreSQL, Redis, or AWS resources.

---

## 14. What is CNI (Container Network Interface)?

CNI is the networking layer for Kubernetes.

Responsibilities:

* Assign IP addresses to pods.
* Enable pod-to-pod communication.
* Configure network policies.
* Connect pods to the cluster network.

Without a CNI plugin, pods cannot communicate.

---

## 15. Which CNI plugin have you used, and how did you implement it?

Based on your OpenShift experience, you can answer:

"I have primarily worked with OpenShift, which uses the **OVN-Kubernetes** CNI plugin by default. I did not manually install the CNI, as it was already configured during cluster installation. My responsibilities included verifying pod networking, troubleshooting connectivity issues, checking CNI pods in the `openshift-ovn-kubernetes` namespace, validating network policies, and ensuring pod-to-pod and service communication. When troubleshooting, I checked node status, CNI pod health, routes, services, and logs to identify networking issues."

This answer aligns well with an OpenShift administration role and reflects practical experience.
