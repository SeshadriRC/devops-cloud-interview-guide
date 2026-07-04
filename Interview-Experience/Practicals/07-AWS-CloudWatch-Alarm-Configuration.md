# AWS CloudWatch Alarm Configuration

Write a CloudFormation template in YAML that sets up an AWS CloudWatch alarm for an EC2 instance. The alarm should trigger when the CPU usage exceeds a certain threshold.

Your template should include:

- The alarm resource configuration.
- Notification configuration using SNS
- Appropriate tags for resource management.


Skills Evaluated:

- AWS CloudFormation
- CloudWatch metrics
- Infrastructure as code

---

This question tests whether you know **Infrastructure as Code (CloudFormation)** and how to configure a **CloudWatch Alarm**.

Let's build it step by step.

---

# What is being asked?

Create a **CloudFormation YAML template** that:

1. Creates a **CloudWatch Alarm**
2. Monitors an **EC2 instance's CPU Utilization**
3. Triggers when CPU exceeds a threshold (say **80%**)
4. Sends a notification using **SNS**
5. Includes **Tags**

Architecture:

```text
EC2 Instance
     │
CPU Utilization
     │
     ▼
CloudWatch Alarm
     │
CPU > 80%
     │
     ▼
SNS Topic
     │
     ▼
Email Notification
```

---

# Complete CloudFormation Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: CloudWatch Alarm for EC2 CPU Utilization

Resources:

  CPUAlarm:

    Type: AWS::CloudWatch::Alarm

    Properties:

      AlarmName: HighCPUAlarm

      AlarmDescription: Alarm when CPU exceeds 80%

      Namespace: AWS/EC2

      MetricName: CPUUtilization

      Statistic: Average

      Period: 300

      EvaluationPeriods: 2

      Threshold: 80

      ComparisonOperator: GreaterThanThreshold

      Dimensions:
        - Name: InstanceId
          Value: i-0123456789abcdef0

      AlarmActions:
        - !Ref AlarmTopic

      Tags:
        - Key: Environment
          Value: Production

        - Key: Team
          Value: DevOps

  AlarmTopic:

    Type: AWS::SNS::Topic

    Properties:

      TopicName: CPUAlarmTopic
```

---

# Explanation

## AWSTemplateFormatVersion

```yaml
AWSTemplateFormatVersion: '2010-09-09'
```

Specifies the CloudFormation template format version.

---

## Description

```yaml
Description: CloudWatch Alarm for EC2 CPU Utilization
```

A description of the template.

---

## Resource Type

```yaml
Type: AWS::CloudWatch::Alarm
```

Creates a CloudWatch Alarm resource.

---

## Alarm Name

```yaml
AlarmName: HighCPUAlarm
```

The name shown in the CloudWatch console.

---

## Namespace

```yaml
Namespace: AWS/EC2
```

Specifies that the metric comes from EC2.

Other examples:

* `AWS/RDS`
* `AWS/ApplicationELB`
* `AWS/Lambda`

---

## Metric

```yaml
MetricName: CPUUtilization
```

The metric to monitor.

Examples include:

* CPUUtilization
* NetworkIn
* NetworkOut
* DiskReadOps

---

## Statistic

```yaml
Statistic: Average
```

CloudWatch calculates the average CPU utilization over the evaluation period.

Other valid values:

* Maximum
* Minimum
* Sum
* SampleCount

---

## Period

```yaml
Period: 300
```

CloudWatch evaluates the metric every **300 seconds (5 minutes)**.

---

## EvaluationPeriods

```yaml
EvaluationPeriods: 2
```

The condition must be met for **2 consecutive evaluation periods** before the alarm changes to the `ALARM` state.

With a period of 300 seconds, this means the CPU must stay above the threshold for about **10 minutes**.

---

## Threshold

```yaml
Threshold: 80
```

Trigger the alarm if CPU usage exceeds **80%**.

---

## Comparison Operator

```yaml
ComparisonOperator: GreaterThanThreshold
```

Meaning:

```text
CPU > 80%
```

Other operators include:

* LessThanThreshold
* GreaterThanOrEqualToThreshold
* LessThanOrEqualToThreshold

---

## Dimensions

```yaml
Dimensions:
  - Name: InstanceId
    Value: i-0123456789abcdef0
```

Specifies which EC2 instance to monitor.

---

## Alarm Action

```yaml
AlarmActions:
  - !Ref AlarmTopic
```

When the alarm enters the `ALARM` state, it publishes a message to the SNS topic.

---

## SNS Topic

```yaml
Type: AWS::SNS::Topic
```

Creates the SNS topic that receives alarm notifications.

In a real deployment, you'd also add an `AWS::SNS::Subscription` resource to subscribe an email address or another endpoint to the topic.

---

## Tags

```yaml
Tags:
  - Key: Environment
    Value: Production

  - Key: Team
    Value: DevOps
```

Tags help organize and manage resources for cost allocation, ownership, and filtering.

---

# Real-Time Example

Suppose your production web server suddenly receives heavy traffic.

```text
Normal CPU
      │
      ▼
30%
      │
      ▼
45%
      │
      ▼
92%
```

CloudWatch evaluates the metric:

```text
CPU = 92%

Threshold = 80%

Condition Met
```

The alarm changes to the `ALARM` state:

```text
CloudWatch Alarm
        │
        ▼
SNS Topic
        │
        ▼
Email Sent

"Production EC2 CPU is above 80%"
```

The DevOps team can then investigate or scale the application.

---

# Interview Explanation (2 Minutes)

> "This CloudFormation template creates a CloudWatch Alarm that monitors the `CPUUtilization` metric for a specific EC2 instance in the `AWS/EC2` namespace. The alarm checks the average CPU usage every five minutes, and if it remains above 80% for two consecutive evaluation periods, it transitions to the `ALARM` state. When triggered, it publishes a notification to an SNS topic, which can be subscribed to by email, SMS, or other endpoints. The template also includes resource tags for easier management and identification. This approach automates monitoring using Infrastructure as Code, making it repeatable and version-controlled."
