Great question. This is one of the most frequently asked EKS interview topics. Let's go step by step and understand **exactly what happens** when a Pod uses a **Kubernetes ServiceAccount + IAM Role (IRSA)** to access S3.

---

# Problem

Suppose you have this deployment.

```text
Deployment

      │
      ├── Pod-1
      ├── Pod-2
      └── Pod-3
```

Each pod needs to

* Upload files
* Read files
* Delete files

from

```text
S3 Bucket
```

Question:

> How will AWS know that these Pods are allowed to access S3?

Obviously, Pods themselves don't have an AWS identity.

So we need to give them one.

---

# Step 1: Create an IAM Policy

First define **what permissions** the application needs.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": [
        "arn:aws:s3:::my-company-bucket/*"
      ]
    }
  ]
}
```

This policy says:

> Whoever gets this policy can

* Read objects
* Upload objects
* Delete objects

only in

```
my-company-bucket
```

---

# Step 2: Create an IAM Role

Create an IAM Role.

Example:

```
S3ApplicationRole
```

Attach the above policy.

Now the role has permission.

But nobody is using it yet.

---

# Step 3: Enable OIDC Provider on EKS

This is the most important step.

Normally AWS trusts

* IAM Users
* IAM Roles
* EC2

But now we want AWS to trust

```
Kubernetes Pods
```

AWS doesn't understand Kubernetes.

So EKS exposes an **OIDC Identity Provider**.

Think of it like this:

```
EKS
 │
 │ "I can verify Pods"
 │
 ▼
OIDC Provider
```

When you create an EKS cluster, it has an OIDC URL such as:

```
https://oidc.eks.ap-south-1.amazonaws.com/id/ABCD1234
```

You register this OIDC provider in your AWS account.

Now AWS trusts tokens issued by your EKS cluster.

---

# Step 4: Create Trust Relationship

The IAM Role must know **who can assume it**.

Trust policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect":"Allow",
      "Principal":{
        "Federated":"arn:aws:iam::<ACCOUNT_ID>:oidc-provider/oidc.eks.ap-south-1.amazonaws.com/id/ABCD1234"
      },
      "Action":"sts:AssumeRoleWithWebIdentity",
      "Condition":{
        "StringEquals":{
          "oidc.eks.ap-south-1.amazonaws.com/id/ABCD1234:sub":"system:serviceaccount:default:app-sa"
        }
      }
    }
  ]
}
```

Meaning:

> Only the Kubernetes ServiceAccount `app-sa` in the `default` namespace can assume this IAM role.

---

# Step 5: Create Kubernetes ServiceAccount

```yaml
apiVersion: v1
kind: ServiceAccount

metadata:
  name: app-sa

  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/S3ApplicationRole
```

This annotation links the ServiceAccount to the IAM Role.

---

# Step 6: Use ServiceAccount in Deployment

```yaml
spec:

  serviceAccountName: app-sa

  containers:

  - name: node-app

    image: myrepo/node-app
```

Now every Pod created by this Deployment uses:

```
app-sa
```

---

# Step 7: Pod Starts

Suppose Pod-1 starts.

```
Pod-1
```

Inside the pod Kubernetes automatically mounts a **ServiceAccount Token**.

Example:

```
/var/run/secrets/eks.amazonaws.com/serviceaccount/token
```

This token proves

> "I am Pod-1 using ServiceAccount app-sa."

---

# Step 8: AWS SDK Needs Credentials

Application runs:

```javascript
const s3 = new S3Client({});
```

No access key.

No secret key.

AWS SDK starts searching for credentials.

It finds:

```
Service Account Token
```

---

# Step 9: AWS SDK Calls STS

SDK sends the token to AWS STS.

```
AWS STS
```

using

```
AssumeRoleWithWebIdentity
```

Request:

```
Hello AWS

Here is my Kubernetes token.

Can I assume this IAM Role?
```

---

# Step 10: AWS Verifies Token

AWS checks

✔ Is token valid?

✔ Issued by trusted OIDC?

✔ Correct namespace?

✔ Correct ServiceAccount?

If yes

AWS replies

```
Temporary Credentials
```

Example

```
Access Key

Secret Key

Session Token
```

valid for about one hour.

---

# Step 11: SDK Uses Temporary Credentials

Now SDK sends request

```
PUT Object

GET Object

DELETE Object
```

to S3.

S3 verifies

```
Temporary Credentials
```

and performs the operation.

---

# Entire Flow

```text
                    AWS

          +----------------------+
          |      S3 Bucket       |
          +----------▲-----------+
                     │
             Signed API Request
                     │
          Temporary Credentials
                     │
          AWS Security Token Service
                     ▲
                     │
 AssumeRoleWithWebIdentity
                     │
              ServiceAccount Token
                     ▲
                     │
            Kubernetes ServiceAccount
                     ▲
                     │
             Deployment (3 Pods)
```

---

# What about Pod-2 and Pod-3?

Exactly the same.

```
Pod-1 ----\
           \
Pod-2 ------> app-sa ---> IAM Role ---> S3
           /
Pod-3 ----/
```

Each Pod:

* Has its own service account token.
* Requests its own temporary AWS credentials.
* Uses those credentials independently.
* Shares the same permissions because they all use the same IAM role.

---

# Why is this better than Secrets?

Suppose you stored:

```
AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY
```

inside Kubernetes Secrets.

Problems:

* Keys are long-lived.
* You must rotate them manually.
* If leaked, they remain valid until revoked.
* Every Pod gets the same static credentials.

With IRSA:

* ✅ No hardcoded credentials.
* ✅ Temporary credentials issued by STS.
* ✅ Automatic rotation by AWS SDK.
* ✅ Fine-grained permissions per ServiceAccount.
* ✅ Follows the principle of least privilege.

---

## Interview Answer (3 Minutes)

> "In Amazon EKS, I would use IAM Roles for Service Accounts (IRSA). First, I create an IAM policy granting only the required S3 permissions, such as `GetObject`, `PutObject`, and `DeleteObject`, and attach it to an IAM role. Next, I enable the EKS cluster's OIDC provider and configure the IAM role's trust policy so that only a specific Kubernetes ServiceAccount can assume it. I then create that ServiceAccount with an annotation referencing the IAM role and configure the Deployment to use it. When a Pod starts, Kubernetes mounts a service account token into the Pod. The AWS SDK uses that token to call AWS STS with `AssumeRoleWithWebIdentity`. After AWS validates the token through the cluster's OIDC provider, it returns temporary credentials. The SDK automatically uses those credentials to securely access S3. This eliminates the need to store AWS access keys in the application and is the recommended AWS security practice for EKS."
