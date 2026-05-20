## Difference Between `for_each` and `for` in Terraform

### Question  
What is the difference between `for_each` and `for` in Terraform?

### Short explanation of the question  
Both keywords relate to “looping,” yet they operate at **different layers**: `for_each` is a **meta-argument** that drives how many resource instances Terraform creates, whereas `for` is an **expression** you embed inside variables, locals, or arguments to build or transform collections.

### Answer  
- **`for_each`** tells Terraform to create **one instance per element** in a map or set (resources, modules, or data blocks).  
- **`for`** is a collection **expression** used to **generate or reshape** lists/maps; it never creates resources on its own.

### Detailed explanation of the answer for readers’ understanding

| Aspect              | `for_each` (meta-argument)                     | `for` (expression)                                 |
|---------------------|-----------------------------------------------|----------------------------------------------------|
| **Primary role**    | Creates or manages **multiple instances**      | Builds or transforms **values** in place           |
| **Where used**      | Resource, module, data, `dynamic` block header | Any argument, variable default, local, output      |
| **Input type**      | Map or set of strings                          | List, map, or set (any iterable)                   |
| **Outcome**         | New resource addresses (`aws_instance.web["a"]`)| A new collection value (no extra resources)        |

---

<img width="1605" height="776" alt="image" src="https://github.com/user-attachments/assets/ffbc5bb8-4c0a-40a8-a63d-48e7fb2c46f0" />

#### Example 1 — `for_each` creating three S3 buckets

```hcl
resource "aws_s3_bucket" "logs" {
  for_each = {
    us = "us-east-1"
    eu = "eu-west-1"
    ap = "ap-southeast-1"
  }

  bucket = "app-logs-${each.key}"
  region = each.value
}
```

Terraform plans three distinct buckets:
aws_s3_bucket.logs["us"], aws_s3_bucket.logs["eu"], aws_s3_bucket.logs["ap"].

#### Example 2 — for expression shaping data

```
variable "allowed_cidrs" {
  type    = list(string)
  default = ["10.0.0.0/24", "10.1.0.0/24"]
}

locals {
  cidr_to_rule = {
    for cidr in var.allowed_cidrs :
    cidr => { protocol = "tcp", port = 22 }
  }
}
```

This produces a map:

```
{
  "10.0.0.0/24": { "protocol": "tcp", "port": 22 },
  "10.1.0.0/24": { "protocol": "tcp", "port": 22 }
}
```

Key takeaways

    for_each → use when you need Terraform to create N physical resources.

    for → use when you need a derived list or map for arguments or outputs.

    They complement each other: you might build a map with for, then pass it to for_each for scalable resource creation.
