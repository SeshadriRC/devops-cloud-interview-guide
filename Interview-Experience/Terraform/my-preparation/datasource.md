A data source reads information about existing infrastructure without creating it.

Example:

```hcl
data "aws_vpc" "existing" {
  default = true
}
```

You mention a **data source** inside your Terraform configuration (`.tf`) files, just like you define resources. It is usually placed in files such as:

* `main.tf`
* `network.tf`
* `data.tf` (many teams keep all data sources here)

### Example project structure

```text
terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── data.tf
└── terraform.tfvars
```

A common practice is to create a dedicated `data.tf` file:

```hcl
# data.tf

data "aws_vpc" "existing" {
  default = true
}
```

Then, in another file (for example, `main.tf`), you can reference the data source:

```hcl
resource "aws_subnet" "private" {
  vpc_id     = data.aws_vpc.existing.id
  cidr_block = "10.0.1.0/24"
}
```

Notice the syntax:

```hcl
data.<provider>_<resource_type>.<data_source_name>.<attribute>
```

In this example:

```text
data.aws_vpc.existing.id
│     │       │        │
│     │       │        └── Attribute
│     │       └────────── Data source name
│     └────────────────── Resource type
└──────────────────────── Keyword
```

### Real-time interview example

Suppose your networking team has already created a VPC:

```text
VPC Name : production-vpc
VPC ID   : vpc-12345678
```

As a DevOps engineer, you are **not allowed to create another VPC**. Instead, you read the existing VPC and create resources inside it.

```hcl
data "aws_vpc" "prod" {
  filter {
    name   = "tag:Name"
    values = ["production-vpc"]
  }
}

resource "aws_subnet" "app" {
  vpc_id     = data.aws_vpc.prod.id
  cidr_block = "10.0.10.0/24"
}
```

Terraform fetches the existing VPC ID and uses it.

Common examples:

* Existing VPC
* Existing Security Group
* Existing AMI
* Existing Subnet


Here:

* The **data source** fetches the existing VPC.
* The **resource** creates a new subnet inside that VPC.

### Interview answer

If asked *"Where do you use a data source?"*, you can say:

> "We define data sources in our Terraform configuration files (commonly `data.tf` or `main.tf`). They are used to fetch information about existing infrastructure—such as VPCs, subnets, security groups, or AMIs—and then reference those values while creating new resources. This avoids recreating infrastructure that already exists."
