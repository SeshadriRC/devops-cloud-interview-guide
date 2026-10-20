This is a common Terraform interview question.

## What is LockID in DynamoDB?

When Terraform uses **state locking**, it creates a **lock record** in the DynamoDB table before modifying the infrastructure.

The **`LockID`** is the **primary key** that uniquely identifies that lock.

Example DynamoDB table:

```text
Table: terraform-locks

-----------------------------------------------
| LockID                        | Info         |
-----------------------------------------------
| dev/terraform.tfstate         | {...}        |
-----------------------------------------------
```

or sometimes:

```text
LockID
-----------------------------------
prod/network/terraform.tfstate
```

Terraform uses this `LockID` to know:

* Which state file is currently locked.
* Who owns the lock.
* Whether another `terraform apply` is already running.

---

## What happens during `terraform apply`?

Suppose you run:

```bash
terraform apply
```

Terraform performs the following steps:

```
1. Acquire Lock
        │
        ▼
Create LockID in DynamoDB
        │
        ▼
Read terraform.tfstate from S3
        │
        ▼
Provision Infrastructure
        │
        ▼
Update terraform.tfstate
        │
        ▼
Delete LockID
```

If another engineer runs `terraform apply` while the lock exists, Terraform detects the existing `LockID` and fails with a lock error.

---

## What information is stored with the lock?

Besides the `LockID`, Terraform stores metadata such as:

* Operation (`apply`, `plan`, etc.)
* User/host that acquired the lock
* Terraform version
* Timestamp
* A unique lock identifier

Example:

```text
LockID:
prod/network/terraform.tfstate

Info:
{
  ID: "6c8a9b8d-2b6d-4d2d-a9d2-xxxxxxxx",
  Operation: "OperationTypeApply",
  Who: "seshadri@laptop",
  Version: "1.8.5",
  Created: "2026-07-17T10:20:15Z"
}
```

---

## What is the `LOCK_ID` used with `terraform force-unlock`?

Sometimes Terraform crashes before releasing the lock.

You might see:

```text
Error acquiring the state lock

Lock Info:
  ID:        6c8a9b8d-2b6d-4d2d-a9d2-xxxxxxxx
  Path:      dev/terraform.tfstate
  Operation: OperationTypeApply
  Who:       seshadri@laptop
```

To remove that stale lock (after confirming no Terraform operation is still running):

```bash
terraform force-unlock 6c8a9b8d-2b6d-4d2d-a9d2-xxxxxxxx
```

**Important:** The `LockID` in the DynamoDB table (the table's primary key, often the state file path) is **not the same thing** as the **lock `ID`** shown in the Terraform error message. The `force-unlock` command uses the **unique lock ID** from the error message, not the DynamoDB partition key.

---

## Interview Answer (30 seconds)

> "The `LockID` is the unique key used by Terraform in the DynamoDB lock table to represent a lock for a particular Terraform state file. Before running `terraform apply`, Terraform creates a lock record. If another user tries to modify the same state, Terraform detects the existing lock and prevents concurrent changes. Once the operation completes successfully, Terraform removes the lock. If the process crashes, the stale lock can be removed using `terraform force-unlock` with the lock ID shown in the error message."
