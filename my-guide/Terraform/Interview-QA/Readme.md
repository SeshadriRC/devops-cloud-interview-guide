**Terraform State file and State locking**

* Terraform code (`.tf` / HCL files) is stored in a Git repository.
* Terraform state file (`terraform.tfstate`) is stored remotely in an AWS S3 bucket so multiple team members can access the same infrastructure state.
* Earlier, teams commonly used:

  * S3 → for storing state file
  * DynamoDB → for state locking

### Why locking is needed?

Locking prevents multiple users from running Terraform at the same time and corrupting the state file.

### What they mean here:

Previously:

* S3 = state storage
* DynamoDB = locking mechanism

Now AWS/Terraform supports native locking with S3 itself, so they removed DynamoDB and use only S3 for:

* Remote backend
* State locking

### Example backend configuration

```hcl
terraform {
  backend "s3" {
    bucket = "terraform-state-bucket"
    key    = "prod/terraform.tfstate"
    region = "ap-south-1"
  }
}
```

In interviews, you can simply say:

> We store Terraform state files in an S3 remote backend for centralized state management. Earlier we used DynamoDB for state locking, but now we use native S3 locking support to prevent concurrent Terraform executions.


Correct.

With newer Terraform/OpenTofu versions and updated AWS S3 backend support, you can use only the S3 backend configuration, and separate DynamoDB locking is no longer mandatory.

So this is enough:

```hcl
terraform {
  backend "s3" {
    bucket = "terraform-state-bucket"
    key    = "prod/terraform.tfstate"
    region = "ap-south-1"
  }
}
```

Earlier, people used something like this for locking:

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state-bucket"
    key            = "prod/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-locks"
  }
}
```

Now the locking can be handled directly through S3, so no additional DynamoDB table configuration is required in newer setups.

Purpose of locking:

* Prevents two users/pipelines from running `terraform apply` simultaneously
* Avoids corruption of the `terraform.tfstate` file
* Ensures safe team collaboration

You can explain it in interviews like:

> Earlier we used S3 for backend storage and DynamoDB for state locking. Recently, we moved to native S3 locking support, so we no longer maintain separate DynamoDB tables for Terraform locking.


---
