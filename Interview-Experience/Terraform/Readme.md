# Terraform Interview Preparation Guide

This guide covers Terraform infrastructure-as-code concepts, state file internals, remote backends, locking mechanisms, drift detection, and troubleshooting.

---

## 📑 Categorized Preparation Roadmap

### 1. State Management, Locking & Workspaces
* 🔒 **State Locking & DynamoDB**: [What is LockID and How State Locking Works](my-preparation/lockid.md)  
  *Preventing concurrent state mutations using DynamoDB LockID table.*
* 🗂️ **Workspaces vs. State Isolation**: [Difference Between Statefile and Workspace Statefiles](my-preparation/statefile-workspace-statefile.md)  
  *When to use Terraform workspaces vs. dedicated directory/backend isolation for environments.*
* 🩹 **State Corruption Recovery**: [How to Recover from Corrupted Terraform State](Interview-Q&A.md#2-terraform-state-file-is-corrupted--how-would-you-recover-it)  
  *Step-by-step restoration using remote backend object versioning, `state pull/push`, and `refresh-only`.*

### 2. Drift Detection & State Refresh
* 🌪️ **Drift Management**: [What is Terraform Drift and How to Overcome It](my-preparation/terraform-drift.md)  
  *Detecting manual cloud console changes, schedule-based drift detection pipelines, and remediation.*
* 🔄 **State Refreshing**: [Difference Between terraform refresh and terraform plan -refresh-only](my-preparation/terraform-refresh-refreshonly.md)  
  *Why `terraform refresh` was deprecated in favor of safe `terraform plan -refresh-only` and `terraform apply -refresh-only`.*

### 3. Core Architecture & Syntax
* 📡 **Data Sources**: [Data Sources in Terraform Explained](my-preparation/datasource.md)  
  *Fetching live information (VPC IDs, existing subnets, latest AMI IDs) defined outside Terraform.*
* ⚙️ **Provider Configuration**: [Contents and Structure of provider.tf](my-preparation/provider-file.md)  
  *Configuring `required_providers`, versions, authentication, assume_role, and aliases.*
* 🧬 **Resource Lifecycle Controls**: [Terraform Lifecycle Meta-Arguments](Interview-Q&A.md#1-what-is-the-lifecycle-block-in-terraform)  
  *Deep dive on `create_before_destroy`, `prevent_destroy`, `ignore_changes`, and `replace_triggered_by`.*
* ⚠️ **Provisioners Best Practices**: [Terraform Provisioners: When and Why They Are a Last Resort](Interview-Q&A.md#3-what-are-terraform-provisioners-and-why-are-they-considered-a-last-resort)  
  *Evaluating `local-exec` and `remote-exec` vs. Packer and User Data.*

---

## 🛠️ Practical Hands-on Terraform Scenarios
* [EC2 Provisioning with Configurable AMI and Type](../Practicals/02-Deploy-containerize-Flask-app.md#scenario-2-write-terraform-code-to-create-an-ec2-instance-where-ami-id-and-instance-type-are-configurable)
