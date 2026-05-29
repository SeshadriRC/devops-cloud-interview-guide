<img width="1694" height="713" alt="image" src="https://github.com/user-attachments/assets/b111a35d-eb64-4f1e-a398-0877ed4acc05" />

**“Have you used AWS EFS? If yes, what issues did you face?”**

Interviewers usually ask these kinds of questions to check whether you have practical working experience with a specific AWS service.

For example, if the organization heavily uses AWS EFS, they may prefer candidates who already have hands-on experience with it.

So when answering this question, follow a simple structure:

1. First explain what EFS is
2. Explain where and why it is used
3. Describe a practical issue you faced
4. Explain how you troubleshot and solved the issue

Even if you do not have deep production experience, you can still explain a simple and realistic troubleshooting scenario.

---

## Example Answer

If I were asked this question in an interview, this is how I would answer:

AWS EFS stands for Elastic File System.

It is a managed shared file storage service provided by AWS.

EFS is mainly used when multiple EC2 instances need access to the same shared storage.

Unlike EBS, which is block storage and typically attached to a single EC2 instance, EFS uses the NFS protocol and can be mounted across multiple EC2 instances simultaneously.

So whenever we need scalable, shared, and serverless file storage in AWS, EFS becomes a good solution.

---

## How We Use EFS

In our organization, we use EFS for shared data access between multiple EC2 instances.

For example:

* Shared configuration files
* Application data
* Logs
* Common directories used across servers

Since multiple EC2 instances need concurrent access to the same data, EFS works well for our use case.

---

## Issue I Faced with EFS

Recently, while supporting one of our development teams, I faced an issue related to EFS mounting.

The issue was:

**“EFS mount target unreachable”**

Basically, the EFS mount operation was failing on the EC2 instances.

---

## Troubleshooting Steps

As a first step, I started verifying whether the EFS mount targets were available in all Availability Zones where our EC2 instances were deployed.

Our EC2 instances were running across multiple Availability Zones such as:

* us-east-1a
* us-east-1b
* us-east-1c

So I checked whether EFS mount targets existed in all these Availability Zones.

---

### Step 2: Verify Security Groups

Next, I checked the security groups attached to the EFS mount targets.

We work in a highly secure environment where access is controlled strictly through security groups.

So I verified whether the required NFS port was allowed in the inbound rules.

For EFS, the required port is:

```text
2049
```

I checked whether inbound traffic on port 2049 was allowed from the EC2 instances.

---

### Step 3: Verify DNS and IAM Configuration

After that, I checked:

* DNS resolution for the EFS endpoint
* IAM permissions attached to the EFS Access Point

Finally, I identified that the actual issue was related to IAM permissions configured for the EFS Access Point.

The IAM policy did not have the required permissions for mounting the EFS.

So I updated the IAM policy with the necessary permissions.

After updating the policy, the EFS mount worked successfully and the issue was resolved.

---

## Important Interview Tip

Whenever you answer service-based scenario questions:

* First explain the service clearly
* Explain why your organization uses it
* Describe a real or practical issue
* Explain your troubleshooting steps step-by-step
* End with the resolution and outcome

This gives the interviewer confidence that you understand both the service and the troubleshooting process.

Even a simple issue explained properly creates a strong impression in interviews.
