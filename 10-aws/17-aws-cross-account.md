<img width="1843" height="850" alt="image" src="https://github.com/user-attachments/assets/5cd2799d-995a-4fc4-9123-b548e47cd9b6" />

**“How can an AWS Lambda function in one AWS account access an S3 bucket in another AWS account?”**

Many candidates get nervous when interview questions involve cross-account AWS access.

But honestly, the concept is not very different from resources communicating within the same AWS account.

The main difference is usually related to IAM permissions and account IDs.

---

## Understanding the Scenario

Suppose:

* AWS Account A contains the Lambda function
* AWS Account B contains the S3 bucket

The requirement is:

> Lambda function in Account A should access the S3 bucket in Account B.

This can be achieved using:

* IAM permissions on the Lambda execution role
* S3 bucket policy in the target account

---

## Step 1: Create or Identify the Lambda Function

First, create the Lambda function in AWS Account A if it does not already exist.

Every Lambda function runs using a Lambda Execution Role.

This role determines what AWS services the Lambda function can access.

---

## Step 2: Update the Lambda Execution Role

Next, go to the Lambda Execution Role attached to the Lambda function.

Update the IAM policy attached to this role and provide permissions for the target S3 bucket.

For example:

```json id="b4lghk"
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-cross-account-bucket/*"
    }
  ]
}
```

Notice one important thing:

You do not need to mention the AWS account number inside the S3 ARN because S3 bucket names are globally unique.

---

## Step 3: Update the S3 Bucket Policy in Account B

Now go to the S3 bucket in AWS Account B.

Every S3 bucket can have a Bucket Policy attached to it.

Inside the bucket policy, allow access for the Lambda Execution Role from Account A.

You can simply copy the ARN of the Lambda Execution Role and use it in the bucket policy.

Example:

```json id="dr6o1y"
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111122223333:role/lambda-execution-role"
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-cross-account-bucket/*"
    }
  ]
}
```

---

## What Happens Here?

In this setup:

* Lambda execution role has permission to access the S3 bucket
* S3 bucket policy trusts the Lambda role from another AWS account

Both sides must allow access.

That is the key concept in cross-account AWS communication.

---

## Important Interview Point

The interviewer may try to confuse you by saying:

* “Cross-account access”
* “Different AWS accounts”
* “Resource in Account A accessing resource in Account B”

But in reality, the process is almost the same as same-account access.

The only real difference is:

* The IAM Role ARN belongs to a different AWS account ID

Everything else remains very similar.

---

## Final Interview Tip

Whenever you face cross-account AWS questions:

1. Identify the source resource
2. Identify the target resource
3. Check IAM permissions on the source side
4. Check resource policies on the target side
5. Ensure trust exists between both accounts

If you explain this clearly and confidently, interviewers will usually be satisfied with the answer.
