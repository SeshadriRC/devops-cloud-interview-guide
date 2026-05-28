<img width="1696" height="448" alt="image" src="https://github.com/user-attachments/assets/c8a0a3e6-52ac-4da3-ae11-1e824b2df8a5" />

# EC2 Instance Terminated Unexpectedly — How Would You Troubleshoot?

This is a very common AWS troubleshooting interview question.

The interviewer wants to understand:

* How you investigate AWS issues
* Whether you know AWS monitoring and auditing services
* How you approach production incidents

---

# Scenario

Suppose:

* Your organization has multiple EC2 instances
* One critical EC2 instance suddenly gets terminated unexpectedly

The question is:

> “How would you troubleshoot the issue?”

---

# How to Answer This Question

You can explain it like this:

> “The first thing I would check is AWS CloudTrail.”

---

# Why CloudTrail?

Amazon Web Services AWS CloudTrail records:

* AWS API calls
* User activities
* Automation actions
* Service-triggered operations

Since the EC2 instance was terminated, I would specifically look for the API event:

# TerminateInstances

---

# What to Check in CloudTrail

In CloudTrail logs, I would verify:

* Who triggered the termination
* When the termination happened
* Which IAM user/role performed it
* Whether automation was involved

This helps identify the root cause.

---

# Possible Causes to Investigate

## 1. Auto Scaling Group (ASG)

Sometimes the EC2 instance may belong to an:

* Auto Scaling Group

If:

* Health checks fail
* Scaling policies trigger
* Desired capacity changes

then ASG may automatically terminate the instance and launch a replacement.

So I would verify whether:

* Auto Scaling policies triggered the termination.

---

# 2. Automation Scripts

I would check whether:

* Lambda functions
* Terraform pipelines
* Jenkins jobs
* Ansible scripts
* Internal automation tools

accidentally terminated the instance.

CloudTrail helps identify such automation actions.

---

# 3. Lifecycle Policies

I would verify whether:

* Scheduled cleanup jobs
* Lifecycle policies
* Cost optimization scripts

were configured to terminate unused EC2 instances.

Sometimes organizations automate cleanup for:

* Old environments
* Idle instances
* Temporary workloads

---

# 4. Spot Instance Interruption

This is another very important check.

Many organizations use:

# Spot Instances

to reduce AWS costs.

However:

* Spot instances can be interrupted anytime by AWS.

If the terminated EC2 was a Spot Instance:

* AWS may have reclaimed the capacity
* The instance could terminate unexpectedly

For critical workloads:

* Spot instances are generally avoided.

So I would verify:

* Whether the EC2 instance was launched as a Spot Instance.

---

# Key CloudTrail Event

The main event to investigate is:

```text id="o3x8zx"
TerminateInstances
```

This event provides:

* Source of termination
* User/service details
* Timestamp
* API caller information

---

# Interview-Friendly Answer

You can answer like this:

> “If an EC2 instance terminates unexpectedly, the first thing I would check is AWS CloudTrail. I would look for the `TerminateInstances` API event to identify who or what triggered the termination. I would verify whether the instance was part of an Auto Scaling Group, whether any automation scripts or lifecycle policies were involved, and also check if it was a Spot Instance, since Spot Instances can be terminated by AWS at any time.”
