# Terraform Interview Questions & Deep Dives

---

## 1. What is the `lifecycle` block in Terraform?

### Concept
The `lifecycle` meta-argument is used inside resource blocks to customize how Terraform creates, updates, or deletes that specific resource, altering the default Terraform lifecycle behavior.

### Key Lifecycle Attributes

#### 1. `create_before_destroy = true`
* **Default behavior**: Terraform destroys an existing resource before creating its replacement.
* **With `create_before_destroy`**: Terraform provisions the new replacement resource *first*, verifies it is healthy, and only then tears down the old resource.
* **Use case**: Zero-downtime deployments (e.g., updating Launch Templates or Auto Scaling Groups).

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  lifecycle {
    create_before_destroy = true
  }
}
```

#### 2. `prevent_destroy = true`
* **Purpose**: Prevents accidental deletion of critical infrastructure.
* If a plan or apply would cause the resource to be destroyed, Terraform throws an immediate error and halts execution.
* **Use case**: Production databases (RDS), storage volumes (EBS), core VPCs.

```hcl
resource "aws_db_instance" "production" {
  allocated_storage = 100
  engine            = "postgres"

  lifecycle {
    prevent_destroy = true
  }
}
```

#### 3. `ignore_changes = [ ... ]`
* **Purpose**: Instructs Terraform to ignore external modifications to specific resource attributes during `terraform plan` and `apply`.
* **Use case**: Attributes managed by external systems or auto-scalers (e.g., Auto Scaling Group `desired_capacity`, or AWS tags managed by tag policies).

```hcl
resource "aws_autoscaling_group" "app_asg" {
  max_size         = 10
  min_size         = 2
  desired_capacity = 2

  lifecycle {
    ignore_changes = [
      desired_capacity,
      tags
    ]
  }
}
```

#### 4. `replace_triggered_by = [ ... ]`
* **Purpose**: Introduced in Terraform 1.2+, forces the resource to be replaced whenever a referenced resource or attribute changes.
* **Use case**: Restarting/re-provisioning instances when a User Data script or IAM role changes.

---

## 2. Terraform state file is corrupted — how would you recover it?

### Interview Answer & Step-by-Step Recovery Procedure

When a Terraform state file becomes corrupted (invalid JSON syntax, interrupted write, or merge collision), follow these recovery steps:

```text
[ Detect Corruption ]
         │
         ▼
[ 1. Acquire State Lock & Isolate ]
  Prevent any other team members or CI/CD pipelines from running.
         │
         ▼
[ 2. Restore from Remote Backend Versioning ]
  - S3 Versioning / GCS Versioning / Azure Blob Versioning
  - Download the most recent non-corrupted state version
         │
         ▼
[ 3. Validate State JSON Integrity ]
  jq . state-backup.json
         │
         ▼
[ 4. Force Push Recovered State ]
  terraform state push -force state-backup.json
         │
         ▼
[ 5. Run Refresh-Only Plan ]
  terraform plan -refresh-only
  Reconcile state with live cloud reality without modifying infrastructure.
         │
         ▼
[ 6. Repair Drift / Import Missing Resources ]
  terraform import <resource> <id> (if needed)
```

### Detailed Commands:
1. **Pull corrupted state for inspection**:
   ```bash
   terraform state pull > corrupted_state.json
   ```
2. **Retrieve previous version from S3**:
   ```bash
   aws s3api list-object-versions --bucket my-tf-state-bucket --prefix env/prod/terraform.tfstate
   aws s3api get-object --bucket my-tf-state-bucket --key env/prod/terraform.tfstate --version-id <PREVIOUS_VERSION_ID> recovered_state.json
   ```
3. **Verify JSON validity**:
   ```bash
   jq . recovered_state.json > /dev/null && echo "Valid JSON"
   ```
4. **Push recovered state back to remote backend**:
   ```bash
   terraform state push -force recovered_state.json
   ```
5. **Re-synchronize with live cloud resources**:
   ```bash
   terraform plan -refresh-only
   ```
   If everything matches, run `terraform apply -refresh-only`.

---

## 3. What are Terraform Provisioners and why are they considered a last resort?

### What are Provisioners?
Provisioners are used to execute scripts or shell commands on a local machine or a remote compute resource as part of resource creation or deletion.

* **`local-exec`**: Runs a command locally on the machine executing Terraform.
  ```hcl
  resource "aws_instance" "web" {
    ami           = "ami-0123456789"
    instance_type = "t3.micro"

    provisioner "local-exec" {
      command = "echo ${self.private_ip} >> inventory.ini"
    }
  }
  ```
* **`remote-exec`**: Connects via SSH or WinRM to the provisioned instance to run inline commands or scripts.

### Why are Provisioners a "Last Resort"?
1. **Break Declarative Model**: Terraform cannot model or track the state of individual shell commands.
2. **Lack of Idempotency**: If a script partially fails, Terraform cannot easily roll it back or know which commands succeeded.
3. **Connectivity Requirements**: `remote-exec` requires opening inbound port 22 (SSH) or 5985 (WinRM), storing private keys/passwords in Terraform state, and having direct network access to instances.
4. **Fragile Lifecycle**: Network timeouts or dependency failures during script execution cause Terraform to mark the entire resource as **tainted**.

### What Should You Use Instead?
* **Cloud-Init / EC2 User Data**: Pass startup scripts directly through the cloud provider API (`user_data = file("bootstrap.sh")`).
* **Golden Images (Packer)**: Pre-bake all dependencies, software, and configs into custom AMIs or container images before Terraform runs.
* **Configuration Management**: Use Ansible, Chef, or Puppet triggered via CI/CD after Terraform outputs the infrastructure details.
