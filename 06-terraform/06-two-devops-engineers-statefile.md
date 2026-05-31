# Interview Question: What Happens If Two DevOps Engineers Run `terraform apply` at the Same Time?

This is a tricky interview question that tests whether you have worked with Terraform in a real enterprise environment.

The interviewer is primarily checking your understanding of:

* Terraform state management
* State locking
* Remote backends
* Race conditions

---

## Scenario

Suppose:

* A developer raises a critical Jira ticket requesting a new VPC.
* Two DevOps engineers, Engineer A and Engineer B, pick up the same ticket.
* Both use the same Terraform module to create the VPC.
* Both execute:

```bash
terraform apply
```

from their respective machines at almost the same time.

The interviewer may ask:

> What happens now? Will Terraform create two VPCs? Will it fail? How does Terraform handle this situation?

---

## The Correct Answer: State Locking Prevents Concurrent Modifications

Terraform uses a mechanism called **state locking** to prevent multiple users from modifying the same infrastructure simultaneously.

In enterprise environments, Terraform state files are usually stored in remote backends such as:

* Amazon S3
* Azure Blob Storage
* Google Cloud Storage (GCS)
* Terraform Cloud

These backends support state locking mechanisms.

---

## What Happens Internally?

### Engineer A

```bash
terraform apply
```

### Engineer B

```bash
terraform apply
```

Both requests are sent to the remote backend where the state file is stored.

```text
Engineer A
      |
      |
      v
Remote Backend
      ^
      |
      |
Engineer B
```

Since both operations attempt to modify the same state file, Terraform tries to acquire a lock before proceeding.

---

## Case 1: One Request Reaches First

In reality, network latency exists.

Even if both engineers click Enter at the same time, one request usually reaches the backend first.

```text
Engineer A Request
        |
        |------> Reaches First
        |
        v
State Lock Acquired
```

Terraform grants the lock to Engineer A.

```text
Engineer A
      |
Lock Acquired
      |
terraform apply
      |
VPC Created
```

Now Engineer B attempts to acquire the lock:

```text
Engineer B
      |
Waiting for Lock
      |
Lock Not Available
```

Terraform returns a lock-related error or waits depending on the configuration.

---

## Case 2: Both Requests Reach Simultaneously

The interviewer may ask:

> What if both requests reach the backend at exactly the same time?

The answer is:

> The remote backend handles the race condition.

For example:

* S3 + DynamoDB locking
* Azure Blob lease locking
* Terraform Cloud locking

These systems internally decide which request acquires the lock first.

```text
Request A ----\
               \
                > Lock Manager
               /
Request B ----/
```

The backend's locking mechanism ensures only one operation proceeds.

This is not something Terraform users need to manage manually.

---

## After the First Apply Completes

Suppose Engineer A successfully creates the VPC.

The lock is released.

```text
Engineer A
      |
Apply Complete
      |
Lock Released
```

Now Engineer B's operation starts.

Terraform refreshes the state and checks existing resources.

---

## Possible Outcomes

### Scenario 1: Same Configuration

Both engineers are trying to create the same VPC.

```text
VPC Name = shared-vpc
CIDR     = 10.0.0.0/16
```

Terraform sees that the VPC already exists.

Possible outcome:

```text
No changes required
```

or

```text
Resource already exists
```

depending on the implementation and workflow.

---

### Scenario 2: Different Configuration

Suppose Engineer B modifies:

```text
VPC Name = app-vpc
CIDR     = 172.16.0.0/16
```

Terraform treats it as a separate resource and may create another VPC.

---

## Why State Locking Is Important

Without state locking:

```text
Engineer A
      |
terraform apply

Engineer B
      |
terraform apply
```

Both processes could:

* Update the same resource simultaneously.
* Corrupt the state file.
* Cause inconsistent infrastructure.
* Produce unpredictable results.

State locking prevents all these issues.

---

## Real AWS Example

In AWS environments, a common setup is:

```text
Terraform State
       |
       v
Amazon S3
       |
       v
terraform.tfstate

Locking
       |
       v
DynamoDB Table
```

* S3 stores the state file.
* DynamoDB manages state locks.
* Only one Terraform operation can modify the state at a time.

---

## Interview Answer (Short Version)

> In enterprise environments, Terraform state files are stored in remote backends such as S3, Azure Blob Storage, or Terraform Cloud. These backends support state locking. If two DevOps engineers execute `terraform apply` simultaneously, Terraform attempts to acquire a lock on the state file. The first request that acquires the lock proceeds with the deployment, while the second request waits or receives a lock-related error. The remote backend handles any race conditions and ensures only one operation modifies the state at a time, preventing state corruption and conflicting infrastructure changes.

---

## Easy Interview Memory Trick

```text
terraform apply
        |
        v
Acquire State Lock
        |
        +---- Lock Available ---> Execute
        |
        +---- Lock Busy -------> Wait / Fail
```

### One-Line Interview Answer

> If multiple DevOps engineers run `terraform apply` simultaneously, Terraform's state locking mechanism ensures that only one operation can modify the state file at a time, while the remote backend handles race conditions and prevents state corruption.

