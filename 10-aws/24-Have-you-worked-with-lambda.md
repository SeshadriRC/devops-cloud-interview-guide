# Interview Question: Have You Worked with AWS Lambda Functions?

Interviewers ask this question not just to check whether you know what AWS Lambda is, but to understand whether you have worked with it in real-world scenarios and can explain practical use cases.

A good answer should include:

* Confirmation of your experience.
* Real-world use cases.
* Business value delivered through Lambda functions.

---

## Sample Interview Answer

> Yes, I have strong experience working with AWS Lambda functions. In my current and previous projects, I have primarily used Lambda for cost optimization, compliance enforcement, automation, and operational tasks within AWS environments.

---

## 1. Cost Optimization Using Lambda

One of the major use cases where I used Lambda was cost optimization.

### Example: Identifying Unattached EBS Volumes

In one project, I developed a Lambda function that periodically scanned AWS accounts for unattached EBS volumes.

### Workflow

```text id="91h1a0"
CloudWatch Event
        |
        v
Lambda Function
        |
        v
Find Unattached EBS Volumes
        |
        v
SNS Notification
        |
        v
Development Team
```

If an EBS volume remained unattached for a predefined period:

* The Lambda function generated an SNS notification.
* The notification was sent to the respective development team.
* Follow-up reminders were sent until action was taken.

This helped reduce unnecessary storage costs.

---

## 2. Cleaning Up Unused Resources

I also developed Lambda functions to identify:

* Stale EBS snapshots
* Unused AMIs
* Old backups
* Orphaned resources

These functions generated reports or notifications so that teams could review and clean up unused resources.

### Benefits

* Reduced AWS costs
* Improved resource governance
* Better cloud hygiene

---

## 3. Compliance Enforcement Using Lambda

Another major use case was enforcing organizational compliance standards.

### Example: Identifying Untagged Resources

Our organization required all AWS resources to have mandatory tags such as:

* Application Name
* Environment
* Cost Center
* Owner

AWS Config was already enforcing tagging for newly created resources, but management wanted visibility into older resources that were created before the policy was implemented.

### Solution

I created a Lambda function that:

1. Scanned AWS resources.
2. Checked for mandatory tags.
3. Identified non-compliant resources.
4. Sent SNS notifications to the responsible teams.

### Workflow

```text id="2t0nhn"
AWS Resources
       |
       v
Lambda Function
       |
       v
Check Required Tags
       |
       v
SNS Notification
       |
       v
Application Teams
```

This helped improve governance and compliance across the AWS environment.

---

## 4. Services Used Along with Lambda

In these solutions, Lambda commonly worked with:

* Amazon SNS
* Amazon CloudWatch
* AWS Config
* Amazon EC2
* Amazon EBS
* Amazon S3
* AWS IAM

Lambda acted as the automation layer that connected these services together.

---

## Why Lambda Was Chosen

I chose Lambda because:

* It is serverless.
* No infrastructure management is required.
* It scales automatically.
* It is cost-effective for event-driven workloads.
* It integrates easily with AWS services.

---

# Interview Answer (Short Version)

> Yes, I have worked extensively with AWS Lambda functions. I have primarily used them for cost optimization and compliance automation. For example, I created Lambda functions to identify unattached EBS volumes, stale snapshots, and unused AMIs, and then notify application teams through SNS. I have also used Lambda to enforce tagging compliance by scanning AWS resources and reporting untagged resources to the respective teams. Lambda was a good fit because it is serverless, event-driven, and integrates seamlessly with other AWS services such as SNS, CloudWatch, and AWS Config.

---

# Additional Tip for Interviews

For common AWS services such as:

* Lambda
* EC2
* S3
* VPC
* IAM
* RDS

prepare **2–3 real-world activities or use cases** you have performed.

Interviewers are usually more interested in:

> "What problems did you solve using the service?"

than

> "Can you define the service?"

Practical examples always create a stronger impression than theoretical definitions.

