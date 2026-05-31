<img width="1837" height="884" alt="image" src="https://github.com/user-attachments/assets/dcc29bbc-5884-4d0c-8ad7-891501f92a6e" />

# Interview Question: Write Terraform Code to Create a Resource on AWS

This is a very common Terraform interview question.

The interviewer may ask:

* Write Terraform code to create an EC2 instance.
* Write Terraform code to create an S3 bucket.
* Write Terraform code to create a VPC.
* Write Terraform code to create an Azure VM.
* Write Terraform code to create an Azure Storage Account.

---

## What Is the Interviewer Actually Testing?

The interviewer is **not expecting you to memorize every Terraform resource block**.

Think about it:

* Terraform supports AWS, Azure, GCP, Kubernetes, OpenStack, VMware, and many other providers.
* AWS alone has hundreds of resources.
* Each resource has dozens of configuration parameters.

It is practically impossible to remember every resource name and every field.

In real projects, every DevOps engineer refers to the official provider documentation while writing Terraform code.

The interviewer is mainly checking:

* Do you understand the Terraform workflow?
* Do you know which files are required?
* Do you understand Terraform best practices?
* Do you know how providers, resources, and variables work?

---

# How I Would Answer

Suppose the interviewer asks:

> Write Terraform code to create an S3 bucket.

I would explain the process first and then write the structure.

---

## Step 1: Define the Provider

I always start with a **terraform block** and define the required provider.

### main.tf

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

### Why?

When someone runs Terraform for the first time:

```bash
terraform init
```

Terraform downloads the required provider plugin.

By specifying the version, every DevOps engineer uses the same provider version.

This avoids compatibility issues.

---

## Step 2: Configure the Provider

After defining the provider, I configure it.

```hcl
provider "aws" {
  region = var.aws_region
}
```

This tells Terraform:

* Which provider to use.
* Which AWS region to deploy resources into.

---

## Step 3: Create the Resource

Next, I define the resource block.

For an S3 bucket:

```hcl
resource "aws_s3_bucket" "app_bucket" {
  bucket = var.bucket_name
}
```

I may not remember every field or argument.

In a real project, I would refer to the AWS provider documentation and use the required parameters.

The important part is understanding the syntax:

```hcl
resource "<resource_type>" "<logical_name>" {
   ...
}
```

Example:

```hcl
resource "aws_s3_bucket" "app_bucket" {
}
```

---

## Step 4: Use Variables Instead of Hard-Coding

Instead of hard-coding values, I use variables.

### variables.tf

```hcl
variable "aws_region" {
  type = string
}

variable "bucket_name" {
  type = string
}
```

---

## Step 5: Provide Values Through terraform.tfvars

### terraform.tfvars

```hcl
aws_region = "ap-south-1"

bucket_name = "company-dev-bucket"
```

This allows different environments to reuse the same Terraform code.

Example:

```text
Dev Environment
      |
terraform.tfvars

QA Environment
      |
terraform.tfvars

Production Environment
      |
terraform.tfvars
```

Only variable values change.

The Terraform code remains the same.

---

## Typical Terraform File Structure

```text
terraform-project/
|
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
└── provider.tf
```

Some teams keep everything in `main.tf`.

Some split files based on resource type:

```text
terraform-project/
|
├── provider.tf
├── variables.tf
├── s3.tf
├── ec2.tf
├── vpc.tf
└── outputs.tf
```

Both approaches are acceptable.

---

## Best Practices to Mention

### Use Provider Versioning

```hcl
required_providers {
  aws = {
    source  = "hashicorp/aws"
    version = "~> 5.0"
  }
}
```

### Avoid Hard-Coding Values

Use variables instead.

### Separate Configuration Files

Keep:

* Providers
* Variables
* Resources
* Outputs

in separate files where possible.

### Use Remote State

Store state in:

* S3
* Azure Blob Storage
* GCS
* Terraform Cloud

instead of local machines.

---

## Interview Answer (Short Version)

> When writing Terraform code, I first define the Terraform block and specify the required provider and version. Then I configure the provider, such as AWS and the target region. Next, I create the resource block using the appropriate resource type from the provider documentation. Instead of hard-coding values, I define variables in `variables.tf` and provide values through `terraform.tfvars`. This makes the code reusable across environments and follows Terraform best practices.

---

## Easy Interview Memory Trick

```text
1. Terraform Block
      |
2. Provider Block
      |
3. Resource Block
      |
4. Variables
      |
5. terraform.tfvars
      |
6. terraform apply
```

### One-Line Interview Answer

> I start by defining the Terraform and provider blocks, create the required resource block using the provider documentation, externalize values through variables, and follow best practices such as versioning, reusable code, and remote state management.
