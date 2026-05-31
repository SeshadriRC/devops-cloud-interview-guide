# Scenario-Based Interview Question

<img width="1467" height="704" alt="image" src="https://github.com/user-attachments/assets/66c10ff5-4111-48f9-80ac-cdc5885df565" />

<img width="1916" height="825" alt="image" src="https://github.com/user-attachments/assets/568ab369-e557-483e-b456-0953e0391e6f" />


## How Can a Lambda Function in AWS Account A Interact with a DynamoDB Table in AWS Account B?

This is a classic **cross-account access** scenario.

### Understanding the Problem

Suppose:

* A Lambda function exists in **AWS Account A**.
* A DynamoDB table exists in **AWS Account B**.
* During execution, the Lambda function needs to read data from the DynamoDB table.

By default, AWS blocks access across accounts. Therefore, the Lambda function cannot directly access the DynamoDB table in another account.

To enable this communication, we use:

* **IAM Roles**
* **AWS STS (Security Token Service)**
* **Trust Policies**

---

## Solution Overview

```text
AWS Account A                          AWS Account B
------------------                    -------------------
Lambda Function  ----AssumeRole---->  IAM Role
                                            |
                                            |
                                     DynamoDB Permissions
                                            |
                                            v
                                      DynamoDB Table
```

The Lambda function assumes a role in Account B and temporarily gains permissions to access the DynamoDB table.

---

## Step-by-Step Implementation

### Step 1: Create an IAM Role in Account B

In **AWS Account B**, create an IAM role.

Attach a policy that grants the required DynamoDB permissions.

Example policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:Query",
        "dynamodb:Scan"
      ],
      "Resource": "*"
    }
  ]
}
```

This role now has permission to read data from DynamoDB.

---

### Step 2: Create the Lambda Function in Account A

Create the Lambda function in **AWS Account A**.

Inside the Lambda code, use **AWS STS** to assume the role created in Account B.

The Lambda function will:

1. Call STS.
2. Assume the IAM role in Account B.
3. Receive temporary credentials.
4. Use those credentials to access DynamoDB.

---

### Step 3: Configure the Trust Policy

By default, the AssumeRole request will fail.

To allow the Lambda function to assume the role, update the **Trust Policy** of the IAM role in Account B.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<ACCOUNT_A_ID>:role/<LAMBDA_EXECUTION_ROLE>"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

This trust policy tells AWS:

> "I trust this Lambda execution role from Account A to assume this role."

---

## Lambda Code Example

```python
import boto3

sts = boto3.client('sts')

response = sts.assume_role(
    RoleArn='arn:aws:iam::<ACCOUNT_B_ID>:role/CrossAccountDynamoDBRole',
    RoleSessionName='CrossAccountSession'
)

credentials = response['Credentials']

dynamodb = boto3.client(
    'dynamodb',
    aws_access_key_id=credentials['AccessKeyId'],
    aws_secret_access_key=credentials['SecretAccessKey'],
    aws_session_token=credentials['SessionToken']
)

response = dynamodb.get_item(
    TableName='EmployeeTable',
    Key={
        'EmployeeId': {'S': '1001'}
    }
)

print(response)
```

---

## What Happens Internally?

```text
1. Lambda Function Starts
           |
           v
2. Calls AWS STS AssumeRole
           |
           v
3. STS Checks Trust Policy
           |
           v
4. Temporary Credentials Issued
           |
           v
5. Lambda Uses Credentials
           |
           v
6. DynamoDB Access Successful
```

---

## Important Interview Point

Many candidates say:

> "I will attach a DynamoDB policy to Lambda."

That works only when both resources are in the same account.

For **cross-account access**, you must mention:

* IAM Role in Account B
* Trust Policy
* AWS STS AssumeRole
* Temporary Credentials

These are the key concepts interviewers expect to hear.

---

## Interview Answer (Short Version)

> To allow a Lambda function in AWS Account A to access a DynamoDB table in AWS Account B, I would create an IAM role in Account B with the required DynamoDB permissions. Then, I would update the role's trust policy to trust the Lambda execution role from Account A. Inside the Lambda function, I would use AWS STS AssumeRole to obtain temporary credentials for that role. Using those temporary credentials, the Lambda function can securely access the DynamoDB table in Account B. This is the standard AWS cross-account access pattern.

---

## Key Points to Remember

* Cross-account access is blocked by default.
* Create an IAM role in the target account (Account B).
* Attach DynamoDB permissions to that role.
* Configure a Trust Policy allowing Account A's Lambda role.
* Use AWS STS `AssumeRole`.
* Temporary credentials are returned.
* Lambda uses those credentials to access DynamoDB.
* This follows AWS security best practices and avoids sharing permanent credentials.
