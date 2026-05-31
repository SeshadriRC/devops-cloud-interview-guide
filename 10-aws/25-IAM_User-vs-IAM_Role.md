# What is the Difference Between an IAM User and an IAM Role?

This is a fundamental AWS IAM interview question that tests your understanding of authentication and authorization in AWS.

---

## IAM User

An **IAM User** represents a specific person or application that needs long-term access to AWS resources.

When a new developer, DevOps engineer, or administrator joins an organization, an IAM user can be created for them and assigned the required permissions.

### Characteristics of IAM Users

* Long-term identity.
* Has permanent credentials.
* Can have:

  * Passwords (for AWS Console access)
  * Access Keys (for CLI/API access)
* Represents an individual user or application.
* Permissions are attached directly or through groups.
* Usually deleted or disabled when the user leaves the organization.

### Example

```text
Developer
    |
    v
 IAM User
    |
    v
AWS Resources
```

A DevOps engineer may have an IAM user account that allows access to EC2, S3, CloudWatch, and other AWS services based on assigned permissions.

---

## IAM Role

An **IAM Role** is a temporary identity that contains a set of permissions and can be assumed by users, AWS services, or applications when needed.

Unlike IAM users, IAM roles do not have permanent credentials.

Instead, AWS STS (Security Token Service) provides temporary credentials whenever a role is assumed.

### Characteristics of IAM Roles

* Temporary identity.
* No permanent username or password.
* Provides temporary security credentials.
* Can be assumed by:

  * IAM Users
  * AWS Services
  * Applications
  * AWS Accounts
* Commonly used for service-to-service communication.
* Supports cross-account access.

---

## Example: EC2 Accessing S3

Suppose an application running on an EC2 instance needs access to an S3 bucket.

Instead of storing AWS access keys inside the application:

1. Create an IAM role.
2. Grant S3 permissions to the role.
3. Attach the role to the EC2 instance.

```text
EC2 Instance
      |
      | Assume Role
      v
   IAM Role
      |
      | Permissions
      v
   S3 Bucket
```

The EC2 instance automatically receives temporary credentials and can access the S3 bucket securely.

This is considered a best practice because no long-term credentials need to be stored.

---

## Another Example: Lambda Accessing DynamoDB

```text
Lambda Function
       |
       | Assume Role
       v
    IAM Role
       |
       | Permissions
       v
   DynamoDB
```

The Lambda function assumes the role during execution, performs the required task, and uses temporary credentials provided by AWS.

---

## Key Difference

### IAM User

* Represents a person or application.
* Long-term identity.
* Permanent credentials.
* Used for direct access to AWS.

### IAM Role

* Represents a set of permissions.
* Temporary identity.
* Temporary credentials.
* Used by users, services, and applications when required.

---

## IAM User vs IAM Role Comparison

| Feature              | IAM User                | IAM Role                      |
| -------------------- | ----------------------- | ----------------------------- |
| Identity Type        | Long-term               | Temporary                     |
| Credentials          | Permanent               | Temporary                     |
| Password Support     | Yes                     | No                            |
| Access Keys          | Yes                     | Temporary only                |
| Represents           | User/Application        | Permissions Set               |
| Can Be Assumed       | No                      | Yes                           |
| Used By              | Humans and Applications | Users, Services, Applications |
| AWS STS Required     | No                      | Yes                           |
| Cross-Account Access | Limited                 | Commonly Used                 |

---

## Real-World Usage

### Use IAM User For

* Developers
* DevOps Engineers
* Administrators
* CI/CD systems requiring long-term access (although roles are preferred)

### Use IAM Role For

* EC2 to S3 access
* Lambda to DynamoDB access
* ECS/EKS workloads
* Cross-account access
* Temporary elevated permissions

---

## Interview Answer (Short Version)

> IAM Users and IAM Roles are both used for access management in AWS. An IAM User is a long-term identity associated with a person or application and has permanent credentials such as passwords and access keys. An IAM Role is a temporary identity that contains a set of permissions and can be assumed by users, AWS services, or applications. Roles use temporary credentials provided by AWS STS and are commonly used for service-to-service communication and cross-account access. For example, an EC2 instance can assume an IAM role to access an S3 bucket without storing permanent access keys.

---

## Easy Interview Memory Trick

### IAM User

**Who are you?**

* Long-term identity
* Permanent credentials
* Human users

### IAM Role

**What permissions can you temporarily use?**

* Temporary identity
* Temporary credentials
* Services and applications

### One-Line Interview Answer

> IAM Users provide long-term access for people or applications, whereas IAM Roles provide temporary permissions that can be assumed by users, services, or applications when needed.
