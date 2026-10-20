# AWS DevOps & Cloud Architect Interview Preparation

Welcome to the AWS Interview Guide. This folder covers foundational cloud architectures, VPC networking, security best practices, and real-world scenario questions asked in DevOps and Cloud Engineer interviews.

---

## 📑 Table of Contents
1. [High Availability Architecture on AWS](#1-high-availability-architecture-on-aws)
2. [Secure Access to RDS in a Private Subnet](#2-secure-access-to-rds-in-a-private-subnet)
3. [VPC Peering vs. AWS Transit Gateway](#3-vpc-peering-vs-aws-transit-gateway)
4. [AWS CI/CD & Compute Scenario Q&A](#4-aws-cicd--compute-scenario-qa)
   - [CodeBuild Dependency Failure](#codebuild-dependency-failure)
   - [ECS Blue/Green Deployment with CodeDeploy](#ecs-bluegreen-deployment-with-codedeploy)
   - [Troubleshooting CodeDeploy Rollbacks](#troubleshooting-codedeploy-rollbacks)
   - [DynamoDB Scalable Table Design](#dynamodb-scalable-table-design)
   - [CodePipeline Trigger Troubleshooting](#codepipeline-trigger-troubleshooting)
5. [Additional Reference](#5-additional-reference)

---

## 1. High Availability Architecture on AWS

### Interview Question
> *"How do you design an end-to-end highly available and fault-tolerant architecture on AWS?"*

### Architecture Overview

```text
                  [ Amazon Route 53 (DNS / Health Checks) ]
                                      │
                                      ▼
                        [ AWS WAF + CloudFront (CDN) ]
                                      │
                                      ▼
                     [ Application Load Balancer (ALB) ]
                                      │
         ┌────────────────────────────┴────────────────────────────┐
         ▼ (Multi-AZ Public/Private)                               ▼
┌───────────────────────────────┐         ┌───────────────────────────────┐
│ Availability Zone A           │         │ Availability Zone B           │
│  [Private App Subnet]         │         │  [Private App Subnet]         │
│  Auto Scaling Group (EC2/EKS) │ ◄─────► │  Auto Scaling Group (EC2/EKS) │
│               │               │         │               │               │
│  [Private Data Subnet]        │         │  [Private Data Subnet]        │
│  RDS Primary (Read/Write)     │ ══════► │  RDS Standby (Sync Replica)   │
└───────────────────────────────┘ (Replication)└───────────────────────────────┘
                                      │
                 [ Amazon S3 (Cross-Region Replication / Backups) ]
```

### Key Pillars of the Design

1. **DNS & Global Traffic Routing (Route 53)**:
   - Route 53 configured with health checks and latency-based or failover routing policies.
   - CloudFront CDN edge caching with AWS WAF for DDoS and web application protection.

2. **Load Balancing & Ingress (ALB)**:
   - Application Load Balancer deployed across at least two public subnets in different Availability Zones.
   - Performs continuous HTTP/HTTPS health checks to route traffic only to healthy targets.

3. **Compute Layer (Auto Scaling Groups / EKS / ECS)**:
   - Application containers or EC2 instances deployed across multiple Availability Zones in **private subnets**.
   - Auto Scaling Groups (ASG) dynamically scale capacity based on target metrics (CPU utilization, request count per target).
   - Multi-AZ deployment ensures zero downtime if an entire AZ experiences an outage.

4. **Data Layer (Multi-AZ RDS / Aurora & ElastiCache)**:
   - **Amazon RDS Multi-AZ**: Synchronous physical replication to a standby instance in a secondary AZ. Automatic failover occurs in 60–120 seconds if the primary instance fails.
   - **Read Replicas**: Asynchronous replicas to offload read-heavy database traffic.
   - **ElastiCache (Redis)**: Multi-AZ cluster with auto-failover to cache database queries and manage user sessions.

5. **Storage & Backups (S3 & AWS Backup)**:
   - S3 Standard bucket storage (99.999999999% durability).
   - Versioning, Cross-Region Replication (CRR), and automated lifecycle policies.
   - Centralized AWS Backup policies for automated EBS snapshots and RDS point-in-time recovery.

---

## 2. Secure Access to RDS in a Private Subnet

### Interview Question
> *"How do you securely access an RDS database located in a private subnet without exposing it to the public internet?"*

### Recommended Solution: AWS Systems Manager (SSM) Session Manager Port Forwarding

The industry-standard, most secure method is using **AWS Systems Manager (SSM) Session Manager** with port forwarding.

```text
Local Machine (Admin / DBeaver)
        │
        │ [ Encrypted TLS via AWS CLI / SSM ]
        ▼
Private Subnet: EC2 Bastion Host (No Public IP, No Open Inbound Port 22)
  ├── IAM Role: AmazonSSMManagedInstanceCore
  └── Security Group: Outbound port 3306/5432 to RDS Security Group
        │
        ▼
Private Subnet: Amazon RDS (Port 3306 / 5432)
  └── Security Group: Inbound port 3306/5432 ONLY from Bastion Security Group
```

#### Why this is best practice:
* **No Public IP needed** on the bastion host.
* **No open inbound ports** in Security Groups (port 22 SSH is completely closed).
* Authentication is handled via **IAM and AWS STS MFA**.
* Complete audit logging of all sessions in **AWS CloudTrail**.

#### Step-by-Step Execution:
1. Launch a small EC2 instance (Amazon Linux 2023) in a private subnet with outbound HTTPS access (via NAT Gateway or SSM VPC Endpoints).
2. Attach an IAM Role with `AmazonSSMManagedInstanceCore`.
3. Configure the RDS Security Group to only allow port 3306 (MySQL) or 5432 (Postgres) from the Bastion's Security Group ID.
4. From your local machine, run AWS CLI port forwarding:
   ```bash
   aws ssm start-session \
     --target i-0123456789abcdef0 \
     --document-name AWS-StartPortForwardingSessionToRemoteHost \
     --parameters '{"host":["mydb.xxxxxx.us-east-1.rds.amazonaws.com"],"portNumber":["3306"],"localPortNumber":["3306"]}'
   ```
5. Connect your local database client (e.g., DBeaver, `mysql-client`, `psql`) to `localhost:3306`.

#### Alternative Enterprise Solutions:
* **AWS Client VPN / Site-to-Site VPN**: Connect corporate network directly to the VPC private subnets.
* **AWS Direct Connect**: Dedicated private fiber connection between on-premise data center and AWS VPC.

---

## 3. VPC Peering vs. AWS Transit Gateway

### Interview Question
> *"What is the difference between VPC Peering and AWS Transit Gateway, and when should you choose one over the other?"*

### Comparison Matrix

| Feature | VPC Peering | AWS Transit Gateway (TGW) |
| :--- | :--- | :--- |
| **Topology** | Point-to-point mesh (`N*(N-1)/2` peering links) | Centralized Hub-and-Spoke router |
| **Transitive Routing** | ❌ **No**. Traffic cannot hop through an intermediate VPC | ✅ **Yes**. Fully supports transitive routing across VPCs and VPNs |
| **Scalability** | Hard to manage when VPC count grows (> 10 VPCs) | Scales to thousands of VPCs and on-premise connections |
| **On-Premise Integration**| Requires separate VPN/Direct Connect per VPC | Connects once to TGW; all attached VPCs gain access |
| **Network Segmentation**| Managed by individual VPC route tables and security groups | Managed centrally using Route Domains / TGW Route Tables |
| **Cost** | No hourly charge; only standard cross-AZ/cross-region data transfer fees | Hourly attachment fee per VPC + data processing fee per GB |

### When to Use Which?
* **Use VPC Peering** when:
  - You only have 2 to 5 VPCs that need low-latency, high-throughput communication.
  - You want the most cost-effective option (no hourly base fee).
* **Use AWS Transit Gateway** when:
  - You operate an enterprise multi-account, multi-VPC architecture (AWS Organizations / Landing Zone).
  - You need centralized inspection (e.g., centralized firewall / inspection VPC).
  - You need transitive connectivity between on-premises (Direct Connect / VPN) and multiple cloud VPCs.

---

## 4. AWS CI/CD & Compute Scenario Q&A

### CodeBuild Dependency Failure
* **Scenario**: Your AWS CodeBuild project fails because an external dependency (e.g., an NPM package or Python package) is not found. How do you fix it?
* **Answer**:
  1. Inspect the `buildspec.yml` file. Dependencies must be installed in the `install` or `pre_build` phase before the `build` phase executes.
  2. For private registries (e.g., AWS CodeArtifact or private npm/pip), ensure the build script authenticates and obtains an auth token before attempting package downloads.
  3. Ensure CodeBuild's VPC configuration has outbound internet access (NAT Gateway) if running inside a custom VPC.

### ECS Blue/Green Deployment with CodeDeploy
* **Scenario**: A customer-facing AI API runs as an ECS service behind a load balancer requiring zero downtime and rapid recovery. Which deployment approach is most suitable?
* **Answer**:
  - Use **AWS CodeDeploy with Blue/Green deployment using two ECS task sets** behind an Application Load Balancer.
  - Traffic is shifted gradually or all at once to the Green task set.
  - A test listener allows running automated health and smoke tests against Green before shifting production traffic.
  - If any error or CloudWatch alarm triggers, CodeDeploy instantly rolls back traffic to the Blue task set.

### Troubleshooting CodeDeploy Rollbacks
* **Scenario**: You deploy a new version using AWS CodeDeploy, but the deployment rolls back. As a DevOps engineer, how do you troubleshoot?
* **Answer**:
  1. Open the CodeDeploy console and check the specific deployment lifecycle event that failed (e.g., `ApplicationStop`, `BeforeInstall`, `AfterInstall`, `ApplicationStart`, or `ValidateService`).
  2. Check the deployment execution logs in **Amazon CloudWatch Logs** or directly on instances at `/opt/codedeploy-agent/deployment-root/deployment-logs/codedeploy-agent-deployments.log`.
  3. Inspect container exit codes or target group health checks if the failure occurred during the `ValidateService` phase.

### DynamoDB Scalable Table Design
* **Scenario**: You are designing a DynamoDB datastore to record electric meter readings from millions of homes once a week. The week must be part of the primary query pattern. How do you design for optimal efficiency?
* **Answer**:
  - Use a composite primary key with **`meter_id` as the Partition Key (PK)** and **`timestamp` or `reading_date` as the Sort Key (SK)** to ensure uniform data distribution across partitions and avoid hot partitions.
  - Create a **Global Secondary Index (GSI)** with **`week_number` (or `year_week`) as the Partition Key** and `timestamp` as the Sort Key to efficiently query all readings for a given week.

### CodePipeline Trigger Troubleshooting
* **Scenario**: You update code in AWS CodeCommit repository, but CodePipeline does NOT trigger a build. What should you check?
* **Answer**:
  1. Check if the **Amazon EventBridge rule** (or CloudWatch Events rule) that detects CodeCommit repository state changes is enabled and targeting the pipeline.
  2. Verify that the branch name configured in the CodePipeline source stage exactly matches the branch being pushed to.
  3. Inspect the IAM Service Role assigned to CodePipeline to ensure it has `codecommit:GetBranch`, `codecommit:GetCommit`, and `codecommit:UploadArchive` permissions.

---

## 5. Additional Reference
* Detailed Q&A file: [Interview-Q&A.md](Interview-Q&A.md)
* Practical AWS CloudWatch Alarm Challenge: [07-AWS-CloudWatch-Alarm-Configuration.md](../Practicals/07-AWS-CloudWatch-Alarm-Configuration.md)
* AWS Elastic Beanstalk CI/CD: [03-deploy-node-app-elasticbeanstalk.md](../Practicals/03-deploy-node-app-elasticbeanstalk.md)
