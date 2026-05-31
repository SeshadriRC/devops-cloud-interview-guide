<img width="1401" height="657" alt="image" src="https://github.com/user-attachments/assets/8c04fee0-516f-4617-b283-dbf669d4c3b9" />

<img width="1919" height="865" alt="image" src="https://github.com/user-attachments/assets/60592f74-273d-4758-856d-d0edd703aad3" />


# What is a Trust Policy in AWS and Why Is It Used?

This is a common IAM interview question, but many people struggle to answer it because trust policies are not frequently used in basic DevOps projects.

## What is a Trust Policy?

A **Trust Policy** is a special policy attached to an IAM role that defines **who is allowed to assume that role**.

In AWS, services, users, or principals can temporarily assume an IAM role using AWS STS (Security Token Service). However, not everyone should be allowed to assume every role.

A trust policy acts as a gatekeeper and answers the question:

> "Who is trusted to assume this IAM role?"

Without a trust policy, AWS will deny the role assumption request, even if the requester has permission to call `AssumeRole`.

---

## Why is a Trust Policy Used?

Trust policies are used to:

* Control who can assume an IAM role.
* Enable secure access between AWS services.
* Support cross-account access.
* Prevent unauthorized users or services from using privileged roles.
* Implement the principle of least privilege.

---

## How It Works

Consider an IAM role that has permission to read data from DynamoDB.

```text
IAM Role
   |
   | Permissions
   v
DynamoDB
```

A Lambda function wants to use this role temporarily to fetch data from DynamoDB.

The Lambda function calls AWS STS to assume the role.

Before STS grants temporary credentials, AWS checks the role's trust policy.

```text
Lambda Function
       |
       | AssumeRole
       v
    AWS STS
       |
       | Check Trust Policy
       v
    IAM Role
       |
       | Access
       v
   DynamoDB
```

If the trust policy allows Lambda to assume the role, STS issues temporary credentials.

If not, the request is denied.

---

## Real-World Example

Suppose:

* A Lambda function needs to read items from DynamoDB.
* An IAM role already exists with DynamoDB read permissions.
* The Lambda function should only have access during execution.

In this scenario:

1. Lambda calls AWS STS.
2. STS checks the IAM role's trust policy.
3. If Lambda is trusted, STS returns temporary credentials.
4. Lambda uses those credentials to access DynamoDB.
5. The credentials expire automatically after a specified time.

This provides secure, temporary access without exposing long-term credentials.

---

## Example Trust Policy for Lambda

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### What Does This Mean?

* **Principal** → Lambda service.
* **Action** → Allowed to perform `sts:AssumeRole`.
* **Effect** → Allow.

This policy tells AWS:

> "Lambda is trusted to assume this IAM role."

---

## Cross-Account Example

Trust policies are heavily used for cross-account access.

```text
AWS Account A
   Lambda Function
          |
          | AssumeRole
          v
AWS Account B
      IAM Role
```

In this case:

* The IAM role exists in Account B.
* The Lambda function exists in Account A.
* The role's trust policy must explicitly trust Account A.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111122223333:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Without this trust relationship, the cross-account role assumption will fail.

---

## Where Can You Find the Trust Policy?

In the AWS Console:

```text
IAM
 └── Roles
      └── Select Role
            └── Trust Relationships
                  └── Trust Policy
```

Here you can define:

* AWS services (Lambda, EC2, ECS, etc.)
* IAM users
* IAM roles
* AWS accounts

that are allowed to assume the role.

---

## Interview Answer (Short Version)

> A Trust Policy is an IAM role policy that defines who is allowed to assume the role. When a user, service, or AWS principal tries to assume a role using AWS STS, AWS checks the trust policy first. If the trust policy allows the requester, temporary credentials are issued. Trust policies are commonly used for service-to-service access and cross-account role assumptions. For example, a Lambda function can assume an IAM role with DynamoDB permissions only if the role's trust policy trusts the Lambda service.

---

## Key Points to Remember for Interviews

* Trust Policy is attached to an **IAM Role**.

* It defines **who can assume the role**.

* Used together with **AWS STS**.

* Required for **cross-account access**.

* Can trust:

  * Lambda
  * EC2
  * ECS/EKS
  * IAM Users
  * IAM Roles
  * Entire AWS Accounts

* Answers the question:

  **"Who can use this role?"**

* Permissions policies answer:

  **"What can this role do?"**

### Easy Interview Memory Trick

* **Trust Policy** → *Who can assume the role?*
* **Permission Policy** → *What can the role do after it is assumed?*

