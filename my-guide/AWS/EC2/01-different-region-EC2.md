1. One EC2 in east and other one in south, how it will communication with one other ?

**Case 1: Both have pulic IP**

- So target EC2(north) should have below SG configuration, so you are able to access

```bash
ping <public-ip>
```

<img width="1918" height="715" alt="image" src="https://github.com/user-attachments/assets/af7924fe-7de8-41b2-ab67-262f40f6deb3" />


**Case 2: North EC2 doesn't have public IP**

- Target EC2(north side) doesn't have public IP, only private
- So in this case we can use VPN peering or Transit gateway

**VPN Peering**

- Make sure both VPC have different CIDR block, otherwise it will fail

```
Eg: VPC-1 --> 172.31.0.0/16
    VPC-2 --> 10.0.0.0/16 
```
<img width="1918" height="825" alt="image" src="https://github.com/user-attachments/assets/586d3e77-ad27-4cae-a3ad-3ae17710039d" />
<img width="1768" height="318" alt="image" src="https://github.com/user-attachments/assets/d4dfb0fe-a666-44a0-aa34-d2e71f7c0260" />
