Terraform drift occurs when someone manually changes infrastructure outside Terraform.

Example:

Terraform created:

```
EC2 = t3.medium
```

Someone manually changes it in AWS to:

```
t3.large
```

Terraform state still says:

```
t3.medium
```

This mismatch is called **Terraform drift**.

---

## overcome drift

Methods:

* Run `terraform plan` regularly.
* Detect differences between state and actual infrastructure.
* Use `terraform apply` to restore the desired state.
* Prevent manual changes using IAM permissions.
* Use CI/CD for all infrastructure changes.
* Import manually created resources using:

```bash
terraform import
```
