<img width="1749" height="726" alt="image" src="https://github.com/user-attachments/assets/169fc7d9-65b2-41bf-9f75-31307e35b038" />

# How to Enable Internet Access to an Application Deployed in a Private Subnet in AWS

This is a very common AWS networking interview question.

Applications deployed inside a **private subnet** cannot directly access the internet because private subnets usually do not have a route to an Internet Gateway.

To provide secure outbound internet access, AWS uses:

# NAT Gateway

---

# Why Private Subnets Cannot Access the Internet Directly

A private subnet typically:

* Does not have a route like:

```text id="hpyc8m"
0.0.0.0/0 → Internet Gateway
```

* Contains only private IP addresses
* Blocks direct internet communication

Because of this, applications inside the private subnet cannot:

* Download packages
* Access GitHub
* Pull Docker images
* Reach external APIs

---

# Solution: Use NAT Gateway

To enable internet access securely:

1. Create a Public Subnet
2. Attach an Internet Gateway to the VPC
3. Create a NAT Gateway inside the Public Subnet
4. Associate an Elastic IP with the NAT Gateway
5. Update the Route Table of the Private Subnet

---

# Step-by-Step Flow

## Step 1: Create Internet Gateway

Attach an Internet Gateway to the VPC.

This allows internet communication for public subnet resources.

---

## Step 2: Create Public Subnet

The NAT Gateway must be deployed inside a:

# Public Subnet

because it requires internet access.

The public subnet route table should contain:

```text id="4dlc3s"
0.0.0.0/0 → Internet Gateway
```

---

## Step 3: Create NAT Gateway

Create a NAT Gateway in the public subnet and assign:

* Elastic IP

This gives the NAT Gateway a public IP address.

---

## Step 4: Update Private Subnet Route Table

Modify the private subnet route table:

```text id="4j8pna"
0.0.0.0/0 → NAT Gateway
```

Now all outbound internet traffic from private instances goes through the NAT Gateway.

---

# Traffic Flow

The request flow works like this:

```text id="txpm7z"
Private EC2/Application
        ↓
Private Route Table
        ↓
NAT Gateway
        ↓
Internet Gateway
        ↓
Internet
```

Responses return through the same path.

---

# What NAT Gateway Does

NAT performs:

# Network Address Translation

It replaces:

* Private source IP

with:

* NAT Gateway public IP

This ensures:

* Private IPs remain hidden
* Instances stay secure
* Only outbound internet access is allowed

---

# Security Advantage

Using NAT Gateway:

✅ Applications can access the internet
✅ Private instances remain protected
✅ No direct inbound internet access is allowed

This is why backend applications are usually deployed in private subnets with NAT Gateway access.

---

# Real-Time Example

Suppose backend applications in private subnets need to:

* Download dependencies from GitHub
* Pull Docker images
* Install updates

Without NAT Gateway, this is not possible.

With NAT Gateway:

* Applications get outbound internet access securely.

---

# Interview-Friendly Answer

You can answer like this in interviews:

> “To enable internet access for applications deployed in a private subnet, I would create a NAT Gateway in a public subnet and attach an Elastic IP to it. Then I would update the private subnet route table so that outbound traffic (`0.0.0.0/0`) points to the NAT Gateway. This allows private subnet resources to securely access the internet without exposing their private IP addresses.”
