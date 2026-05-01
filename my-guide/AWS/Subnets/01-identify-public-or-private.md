1. How to identify if subnet is private or public in aws console ?


**Public Subnet**

A subnet is public if its route table contains:

0.0.0.0/0  → Internet Gateway (igw-xxxx)

Meaning:

- Internet traffic goes directly to Internet Gateway
- Instances can get public internet access

<img width="1916" height="822" alt="image" src="https://github.com/user-attachments/assets/4f9f506d-abf4-40aa-a7df-4f19c326bd06" />


**Private Subnet**

A subnet is private if:

It does NOT have route to Internet Gateway
Usually routes internet traffic through:
0.0.0.0/0 → NAT Gateway (nat-xxxx)

or no internet route at all.

---
