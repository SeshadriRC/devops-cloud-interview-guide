<img width="1686" height="820" alt="image" src="https://github.com/user-attachments/assets/3ee53ff7-214e-4e98-9986-dc19e31e9114" />

# What Is the Difference Between a Resource and a Data Source in Terraform?

This is a very common Terraform interview question.

Although the concept is simple, many candidates struggle to explain it clearly.

The easiest way to understand it is to think about what you can do with any cloud resource.

For example, consider a VPC in AWS.

There are two types of operations you can perform:

1. **Create, update, or delete the VPC**
2. **Read information about the VPC**

Terraform uses two different constructs for these operations:

* **Resource** → Create, update, or delete infrastructure.
* **Data Source** → Read existing infrastructure information.

---

## Resource in Terraform

A **resource** is used when Terraform needs to manage infrastructure.

This includes:

* Creating resources
* Modifying resources
* Deleting resources

### Example: Creating an EC2 Instance

```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-12345678"
  instance_type = "t3.micro"
}
```

When you run:

```bash
terraform apply
```

Terraform creates and manages the EC2 instance.

### Resource Operations

```text
Terraform Resource
        |
        +--> Create
        +--> Update
        +--> Delete
```

---

## Data Source in Terraform

A **data source** is used when Terraform needs to read information that already exists.

Terraform does not create or modify the resource.

It simply fetches information from the cloud provider.

### Example

Suppose:

* A VPC already exists.
* A subnet already exists.
* You want to launch an EC2 instance inside that subnet.

Instead of creating a new subnet, you simply read the existing subnet details.

This is where a data source is used.

---

## Real-World Example

### Step 1: Read Existing VPC

```hcl
data "aws_vpc" "existing_vpc" {
  default = true
}
```

This does not create a VPC.

It only fetches information about the existing default VPC.

---

### Step 2: Use That Information

```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-12345678"
  instance_type = "t3.micro"

  subnet_id = data.aws_vpc.existing_vpc.default_route_table_id
}
```

Here:

* The **resource block** creates the EC2 instance.
* The **data source block** reads information from an existing VPC.

---

## Resource vs Data Source

### Resource

```hcl
resource "aws_s3_bucket" "app_bucket" {
  bucket = "my-app-bucket"
}
```

Purpose:

```text
Create S3 Bucket
Modify S3 Bucket
Delete S3 Bucket
```

---

### Data Source

```hcl
data "aws_vpc" "default" {
  default = true
}
```

Purpose:

```text
Read Existing VPC Information
```

No resource is created.

---

## How Do You Know Which Data Source to Use?

The same way you find resources.

Go to the provider documentation.

For example, in the AWS provider documentation, you'll find:

```text
Resources
   |
   +--> aws_instance
   +--> aws_s3_bucket
   +--> aws_vpc
```

and

```text
Data Sources
   |
   +--> aws_vpc
   +--> aws_subnet
   +--> aws_ami
```

Terraform documentation provides examples for both.

---

## Real-World Use Cases for Data Sources

### Read Existing VPC

```hcl
data "aws_vpc" "prod" {
  id = "vpc-123456"
}
```

### Read Existing Subnet

```hcl
data "aws_subnet" "private" {
  id = "subnet-123456"
}
```

### Read Latest AMI

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true
}
```

### Read Existing Security Group

```hcl
data "aws_security_group" "web_sg" {
  name = "web-sg"
}
```

---

## Easy Way to Remember

### Resource

> "Terraform should create or manage this."

```text
Create
Update
Delete
```

---

### Data Source

> "Terraform should only read this."

```text
Read Existing Information
```

---

## Interview Answer (Short Version)

> In Terraform, a resource is used to create, update, or delete infrastructure on a cloud provider. A data source is used to read information about existing infrastructure without managing it. For example, if I want Terraform to create an EC2 instance, I use a resource block. If I want Terraform to fetch details of an existing VPC or subnet and use those details while creating the EC2 instance, I use a data source block.

---

## Resource vs Data Source Comparison

| Feature                       | Resource                          | Data Source       |
| ----------------------------- | --------------------------------- | ----------------- |
| Creates Infrastructure        | ✅ Yes                             | ❌ No              |
| Updates Infrastructure        | ✅ Yes                             | ❌ No              |
| Deletes Infrastructure        | ✅ Yes                             | ❌ No              |
| Reads Existing Infrastructure | ✅ Can Reference Managed Resources | ✅ Primary Purpose |
| Managed by Terraform State    | ✅ Yes                             | ❌ No              |
| Used for Existing Resources   | Sometimes                         | ✅ Yes             |

---

### One-Line Interview Answer

> A Terraform resource is used to create, modify, or delete infrastructure, whereas a data source is used to read information from existing infrastructure and use that information within Terraform configurations.

