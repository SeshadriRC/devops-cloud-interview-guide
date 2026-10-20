This is a common interview question because **`terraform refresh` is deprecated** in newer versions of Terraform. Here's the difference.

| Feature              | `terraform refresh`                                      | `terraform plan -refresh-only`                   |
| -------------------- | -------------------------------------------------------- | ------------------------------------------------ |
| Purpose              | Updates the Terraform state to match real infrastructure | Refreshes the state and shows what would change  |
| State file           | **Directly updates** the state file                      | Refreshes state during planning and shows a plan |
| Shows execution plan | ❌ No                                                     | ✅ Yes                                            |
| Requires approval    | ❌ No                                                     | ✅ You can review the plan before applying        |
| Deprecated           | ✅ Yes                                                    | ❌ No (recommended)                               |
| Safe to use          | Less safe because it modifies state immediately          | Safer because you can review changes first       |

---

## 1. `terraform refresh` (Deprecated)

Suppose Terraform state says:

```
EC2 instance type = t3.medium
```

Someone manually changes it in AWS to:

```
t3.large
```

Running:

```bash
terraform refresh
```

Terraform will:

* Read the actual AWS infrastructure.
* Update the local state file immediately.
* **No execution plan is shown.**

Result:

```
Terraform State
----------------
Instance type = t3.large
```

The infrastructure remains unchanged; only the state is updated.

---

## 2. `terraform plan -refresh-only` (Recommended)

Using the same example:

```
State
-----
t3.medium

Actual AWS
----------
t3.large
```

Run:

```bash
terraform plan -refresh-only
```

Terraform:

* Reads the actual infrastructure.
* Compares it with the current state.
* Displays what would be updated in the state.
* **Does not immediately write those changes to the state.**

Example output:

```text
Terraform will perform the following actions:

  # aws_instance.web has changed

  ~ instance_type = "t3.medium" -> "t3.large"

Plan: 0 to add, 0 to change, 0 to destroy.
```

If you want to persist those refreshed values to the state, you run:

```bash
terraform apply -refresh-only
```

This updates the state after you review and approve the changes.

---

## Which should you use?

Use:

```bash
terraform plan -refresh-only
```

because it is:

* Recommended by HashiCorp.
* Safer, since you can review changes before updating the state.
* The modern replacement for `terraform refresh`.

---

### Interview answer

> **`terraform refresh`** directly updates the Terraform state to match the current infrastructure without showing a plan, and it is deprecated. **`terraform plan -refresh-only`** refreshes the state during the planning phase, displays the detected changes for review, and lets you update the state later with `terraform apply -refresh-only`. Because it provides visibility and approval before modifying the state, `plan -refresh-only` is the recommended approach.
