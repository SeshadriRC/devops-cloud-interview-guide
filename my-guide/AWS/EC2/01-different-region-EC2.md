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

- refer the below one, i created again bcz it above one got failed due to overlapping CIDR

<img width="1919" height="766" alt="image" src="https://github.com/user-attachments/assets/7225962a-f57d-4d10-bdf6-8b8faec631cd" />

- Accept the request

<img width="1919" height="473" alt="image" src="https://github.com/user-attachments/assets/0aa538fb-4a01-4c2f-bb65-363632fd2b90" />

- Also i created one subnet, since route table doesn't have IG , so this is called private subnet

<img width="1919" height="789" alt="image" src="https://github.com/user-attachments/assets/d023451f-3cdd-49cb-abdf-f770e28fd278" />
<img width="1904" height="796" alt="image" src="https://github.com/user-attachments/assets/1f93f700-2cb4-4fb9-80a7-577d5c95f7db" />

- But connection couldn't be established due to the above subnet, so i have modified route table of the subnet for both VPC-east and VPC-south, see the below pics.

`10.0.0.0/16` is a CIDR of East
<img width="1918" height="611" alt="image" src="https://github.com/user-attachments/assets/ec08e0cc-0ae7-4d70-bdf1-55649b1b32d3" />

`172.31.0.0/16` is a CIDR of North
<img width="1919" height="795" alt="image" src="https://github.com/user-attachments/assets/fcd34052-6fc5-47b3-80c1-696b31faa2a6" />

<img width="741" height="295" alt="image" src="https://github.com/user-attachments/assets/797a4a90-09ba-4b20-9ece-ae432a78904f" />

- connected using private ip of north EC2

<img width="1176" height="990" alt="image" src="https://github.com/user-attachments/assets/36bb53ad-d867-42c3-a7f3-482f60be9e62" />

```bash
scp -i /e/WSL/AWS/EC2/first-ec2.pem \
/e/WSL/AWS/EC2/key-pair-north.pem \
ubuntu@52.66.242.238:/home/ubuntu/
key-pair-north.pem
```

