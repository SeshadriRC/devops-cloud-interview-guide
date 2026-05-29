Cost optimization is a very common activity nowadays.

Every organization expects their DevOps and Cloud Engineers to optimize cloud costs, so you can expect a question like this in interviews.

**“Explain one cost optimization activity you performed in your current organization.”**

You should always be prepared with an answer for this question.

So when the interviewer asks this, instead of taking time to think, answer confidently so that the interviewer understands that you have actually worked on such activities in your current or previous organization.

If I am asked this question, this is how I would answer:

---

I would tell the interviewer that cost optimization is part of my day-to-day responsibilities.

As a DevOps Engineer in my current organization, I am expected to continuously monitor and optimize cloud costs.

Recently, I worked on a cloud cost optimization task where my responsibility was to identify unused EBS volumes and remove them.

### Background

Many developers in our organization create EBS volumes regularly.

Sometimes they request us to create EBS volumes for their applications, environments, or testing activities.

After usage, some of these volumes remain unused, but they are still available in the AWS account and continue generating costs.

Over time, especially across one or two years, a large number of unused EBS volumes accumulated in the environment.

Some volumes were attached to EC2 instances, some already had snapshots taken, and some were completely unused.

So the activity assigned to me was to identify such unused EBS volumes and clean them up.

---

### Initial Approach

As a first step, I used the AWS CLI command:

```bash
aws ec2 describe-volumes
```

to identify the list of available volumes.

But later I realized that manually checking volumes was not efficient and automation would be better.

---

### Automation Using Lambda and Python

So I moved to an automated solution using AWS Lambda with Python.

Inside the Lambda function, I used the Python `boto3` library to interact with AWS resources.

Using Boto3, I:

* Identified unused EBS volumes
* Filtered volumes based on status and usage
* Removed unnecessary volumes automatically

Boto3 made the implementation simple because it already provides AWS service clients like:

* EC2 client
* S3 client
* Other AWS service clients

Using these clients, we can directly interact with AWS resources without writing complex code.

---

### Scheduling the Automation

I also configured the Lambda function to run periodically using EventBridge scheduling.

The function was configured to execute every fourth Friday of the month.

This ensured that cleanup happened automatically without manual intervention.

Because of this automation, we were able to significantly reduce unnecessary cloud costs for the organization.

This is one recent cost optimization activity that I worked on.

---

### Another Example You Can Explain

You can also explain another optimization activity related to EBS volume types.

Inside AWS EBS, there are different volume types like:

* GP2
* GP3

GP2 is an older generation volume type and generally more expensive.

GP3 provides better performance with lower cost and higher IOPS flexibility.

In our environment, many older volumes were still running on GP2 because they were created long back.

So I wrote another Lambda function using Python and Boto3 to:

* Identify EBS volumes using GP2
* Automatically migrate or modify them to GP3

This was done using a simple Python automation script with Boto3 APIs.

By converting old GP2 volumes to GP3, we reduced storage costs while also improving performance.

---

This is how you can explain a real-time cloud cost optimization activity in a DevOps interview.
