<img width="1716" height="762" alt="image" src="https://github.com/user-attachments/assets/17d48b71-b78a-4c23-bb76-b92f74f8acaa" />

# Explain NACL vs Security Group

This is a very common AWS interview question.

Interviewers ask this question to check whether you clearly understand AWS networking and security concepts.

The question is usually:

> “What is the difference between a Network ACL and a Security Group, and which one do you use in your organization?”

---

# Basic Understanding

When you create a VPC in AWS, by default:

* Subnets inside the VPC can communicate with each other
* Instances in different subnets can interact internally

This happens because of the default local route in the VPC route table.

To restrict or control traffic, AWS provides two major security mechanisms:

1. Network ACL (NACL)
2. Security Group

---

# What is a Network ACL (NACL)?

A Network ACL is:

# Subnet-Level Security

It controls traffic entering and leaving an entire subnet.

You can define rules to:

* Allow traffic
* Deny traffic
* Restrict specific ports
* Block entire traffic

---

# Real-Time Example of NACL

You can explain it like this:

> “In our organization, we had a dedicated database subnet, and we wanted to restrict unnecessary access from other subnets. For this, we used Network ACLs.”

For example:

* Allow only specific ports
* Deny all other traffic
* Restrict subnet-level access

You can even create:

# Deny All Rule

which blocks all traffic for that subnet.

---

# What is a Security Group?

A Security Group is:

# Instance-Level Security

It controls traffic for individual EC2 instances or resources.

Security Groups are attached directly to instances.

---

# Real-Time Example of Security Group

Suppose multiple applications exist inside the same subnet.

Example:

| Application | Requirement   |
| ----------- | ------------- |
| App 1       | Allow Port 80 |
| App 2       | Block Port 80 |

In this case:

* NACL alone is not enough
* You need instance-level control

So you use:

# Security Groups

---

# Using NACL and Security Group Together

In real-time environments, organizations often use:

# Combination of NACL + Security Group

Example:

* NACL allows Port 80 at subnet level
* Security Groups restrict Port 80 on specific instances

Result:

* Subnet allows traffic generally
* Instance-level rules provide finer control

---

# Most Important Difference

This is the key interview point.

---

# NACL is Stateless

This means:

* Inbound rules and outbound rules are handled separately
* If you allow inbound traffic, you must also explicitly allow outbound traffic

Example:

If you allow:

```text id="g2br6g"
Inbound Port 80
```

you must also allow:

```text id="wh1w2v"
Outbound Port 80
```

Otherwise communication may fail.

---

# Security Group is Stateful

This means:

* If inbound traffic is allowed
* Return outbound traffic is automatically allowed

Example:

If Port 80 is allowed inbound:

```text id="ijqv2h"
Inbound Port 80 → Allowed
```

then response traffic is automatically permitted.

You do not need separate outbound rules for return traffic.

---

# Easy Way to Remember

| Feature            | NACL          | Security Group       |
| ------------------ | ------------- | -------------------- |
| Level              | Subnet        | Instance             |
| Stateful/Stateless | Stateless     | Stateful             |
| Allow Rules        | Yes           | Yes                  |
| Deny Rules         | Yes           | No Explicit Deny     |
| Applied To         | Entire Subnet | Individual Instances |

---

# Real-Time Usage Pattern

Typical production setup:

| Layer                     | Security Mechanism |
| ------------------------- | ------------------ |
| Subnet-level protection   | NACL               |
| Instance-level protection | Security Group     |

---

# Interview-Friendly Answer

You can answer like this:

> “Network ACLs are subnet-level security controls, whereas Security Groups are instance-level firewalls. NACLs are stateless, so inbound and outbound rules must be configured separately. Security Groups are stateful, meaning return traffic is automatically allowed. In our organization, we used NACLs for subnet-level restrictions such as protecting database subnets, and Security Groups for fine-grained instance-level access control.”
