# AWS Secrets Manager vs AWS Systems Manager Parameter Store

This is a common interview question because both services can store sensitive information such as:

* Database usernames and passwords
* API tokens
* Application secrets
* Connection strings
* Encryption keys
* CI/CD pipeline credentials

Because both services seem similar, interviewers often ask:

> "When would you use AWS Secrets Manager instead of AWS Systems Manager Parameter Store?"

---

## Quick Answer

* Use **AWS Systems Manager Parameter Store** when you need a simple, low-cost solution for storing configuration values and secrets.
* Use **AWS Secrets Manager** when you need advanced security features such as automatic secret rotation, cross-account sharing, and deeper integration with AWS services.

---

# AWS Systems Manager Parameter Store

Parameter Store is a feature of AWS Systems Manager that allows you to store:

* Configuration values
* Environment variables
* Application settings
* Sensitive data (SecureString)

### Advantages

✅ Low cost (Standard parameters are free)

✅ Easy to use

✅ Supports encryption using AWS KMS

✅ IAM-based access control

✅ CloudTrail logging and auditing

✅ Suitable for storing application configuration

### Limitations

❌ No built-in automatic secret rotation

❌ Limited secret lifecycle management

❌ Fewer advanced secret management features compared to Secrets Manager

---

# AWS Secrets Manager

AWS Secrets Manager is designed specifically for managing sensitive credentials and secrets.

### Advantages

✅ Built-in automatic secret rotation

✅ Native integration with databases

✅ Cross-account secret sharing

✅ Fine-grained access control

✅ Versioning and secret lifecycle management

✅ CloudTrail auditing

✅ Designed specifically for passwords, API keys, and tokens

### Limitations

❌ Higher cost compared to Parameter Store

❌ May be unnecessary for simple configuration values

---

# Key Difference: Automatic Rotation

One of the biggest advantages of Secrets Manager is **automatic rotation**.

Example:

```text
Database Password
       |
       v
AWS Secrets Manager
       |
       v
Automatic Rotation
(Every 30, 60, or 90 days)
```

Secrets Manager can automatically:

* Generate a new password
* Update the database
* Store the new secret
* Make it available to applications

without manual intervention.

Parameter Store does not provide this capability natively.

---

# Key Difference: Cross-Account Sharing

Suppose you have:

```text
AWS Dev Account
AWS UAT Account
AWS Production Account
```

All environments need access to the same secret.

### Secrets Manager

* Supports secure cross-account secret sharing.
* Can grant access to secrets stored in another AWS account.

### Parameter Store

* Does not provide the same level of native cross-account secret sharing capabilities.

---

# Cost Comparison

| Feature                     | Parameter Store                 | Secrets Manager |
| --------------------------- | ------------------------------- | --------------- |
| Cost                        | Lower                           | Higher          |
| Free Tier                   | Available (Standard Parameters) | Limited         |
| Secret Rotation             | No                              | Yes             |
| Cross-Account Sharing       | Limited                         | Yes             |
| Versioning                  | Basic                           | Advanced        |
| Secret Lifecycle Management | Limited                         | Yes             |
| Database Integration        | No                              | Yes             |

---

# Real-World Usage

### Use Parameter Store For

* Environment variables
* Application configuration
* Non-critical secrets
* Infrastructure-as-Code parameters
* CI/CD configuration values

Examples:

```text
APP_ENV=production
LOG_LEVEL=INFO
S3_BUCKET=my-app-bucket
```

---

### Use Secrets Manager For

* Database passwords
* API tokens
* OAuth credentials
* Third-party service secrets
* Production application credentials

Examples:

```text
PostgreSQL Password
MongoDB Credentials
GitHub Token
Stripe API Key
AWS Access Keys
```

---

# Interview Answer (Short Version)

> AWS Systems Manager Parameter Store and AWS Secrets Manager can both store sensitive information such as passwords and API keys. Parameter Store is a low-cost solution that supports encryption, IAM access control, and auditing, making it suitable for configuration values and basic secret storage. AWS Secrets Manager is designed specifically for secret management and provides advanced features such as automatic secret rotation, cross-account sharing, versioning, and database integration. If I need simple secret storage, I would use Parameter Store. If I require enterprise-grade secret management with automatic rotation and enhanced security, I would use Secrets Manager.

---

# Easy Interview Memory Trick

### Parameter Store

* Simple
* Low Cost
* Configuration Storage
* Basic Secret Management

### Secrets Manager

* Advanced Security
* Automatic Rotation
* Cross-Account Sharing
* Secret Lifecycle Management

### One-Line Interview Answer

> Parameter Store is a cost-effective configuration and secret storage service, whereas Secrets Manager is a dedicated secret management service that provides automatic rotation, cross-account sharing, and advanced security features.

