# What is AWS STS and Why Is It Used?

This is a common interview question, and here's a structured way to answer it.

## What is AWS STS?

**AWS STS (Security Token Service)** is a service that provides **temporary security credentials** to AWS users, services, or applications.

Whenever an AWS service, IAM user, or IAM principal needs to **assume an IAM role** and obtain temporary access to AWS resources, AWS STS is used.

In simple terms:

> AWS STS allows users, applications, or AWS services to temporarily assume an IAM role and receive temporary credentials (Access Key ID, Secret Access Key, and Session Token) to perform actions permitted by that role.

---

## Why Do We Use AWS STS?

AWS STS is used to:

* Provide temporary access instead of long-term credentials.
* Improve security by avoiding hardcoded access keys.
* Enable cross-account access.
* Allow applications and AWS services to assume roles dynamically.
* Implement the principle of least privilege.

---

## Real-World Example

Consider the following scenario:

* A Lambda function needs to read data from a DynamoDB table.
* The Lambda function itself does not have direct permissions to access DynamoDB.
* An IAM role already exists with permissions to read items from DynamoDB.

During execution, the Lambda function can:

1. Use AWS STS.
2. Assume the IAM role.
3. Receive temporary security credentials.
4. Use those credentials to access DynamoDB.

After the session expires, the credentials become invalid automatically.

---

## How STS Works

```text
Lambda Function
       |
       | AssumeRole
       v
   AWS STS
       |
       | Temporary Credentials
       v
    IAM Role
       |
       | Permissions
       v
   DynamoDB
```

---

## Common Python Example Using Boto3

```python
import boto3

sts_client = boto3.client('sts')

response = sts_client.assume_role(
    RoleArn='arn:aws:iam::123456789012:role/DynamoDBReadRole',
    RoleSessionName='LambdaSession'
)

credentials = response['Credentials']

dynamodb = boto3.client(
    'dynamodb',
    aws_access_key_id=credentials['AccessKeyId'],
    aws_secret_access_key=credentials['SecretAccessKey'],
    aws_session_token=credentials['SessionToken']
)
```

### What Happens Here?

1. Lambda creates an STS client.
2. STS performs `AssumeRole`.
3. Temporary credentials are returned:

   * Access Key ID
   * Secret Access Key
   * Session Token
4. Lambda uses these credentials to access DynamoDB.

---

## Cross-Account Access

STS can also be used when the IAM role exists in another AWS account.

Example:

```text
AWS Account A
   Lambda Function
          |
          | AssumeRole
          v
AWS Account B
      IAM Role
```

For cross-account access:

* STS is used to assume the role.
* The target IAM role must contain a **Trust Policy** allowing the source account or service to assume it.

Without the trust policy, the role assumption will fail even if permissions exist.

---

## Interview Answer (Short Version)

> AWS STS (Security Token Service) is used to generate temporary security credentials for users, applications, or AWS services. It enables them to assume IAM roles and perform actions permitted by those roles without using permanent credentials. A common use case is a Lambda function assuming an IAM role through STS to temporarily access DynamoDB, S3, or other AWS resources. STS is also widely used for secure cross-account access, where the target role must have an appropriate trust policy configured.

---

## Key Points to Remember for Interviews

* STS = **Security Token Service**
* Provides **temporary credentials**
* Common API: **AssumeRole**
* Returns:

  * Access Key ID
  * Secret Access Key
  * Session Token
* Used by:

  * Lambda
  * EC2
  * ECS
  * EKS
  * Applications
  * IAM Users
* Supports **cross-account access**
* Requires **Trust Policy** for cross-account role assumption
* More secure than long-term access keys because credentials expire automatically.


<img width="1392" height="859" alt="image" src="https://github.com/user-attachments/assets/ed302a6e-2c1e-4c0f-ae70-3dbdb623c83b" />
