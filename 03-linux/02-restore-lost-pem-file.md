## Question  
Can you restore a lost PEM file? If not, how can you still access the EC2 instance?

### 📝 Short Explanation  
This question evaluates your knowledge of secure SSH access, key-based authentication, and how to regain access to EC2 instances when your only login method (PEM file) is lost.

## ✅ Answer  
No, you **cannot restore** a lost PEM file — it’s not stored on AWS or recoverable.  
However, you can **regain access** by using a workaround: create a new key pair, attach it to the instance via a temporary EC2 rescue process, and restore SSH access.

### 📘 Detailed Explanation  

PEM files (private keys) are **never retrievable from AWS** after initial creation. Losing the PEM file means you cannot SSH into the EC2 instance using the existing key pair.

But here’s how you can still regain access:

---

### ✅ Recovery Steps:

#### Step 1: Create a new key pair
```bash
aws ec2 create-key-pair --key-name new-key --query 'KeyMaterial' --output text > new-key.pem
chmod 400 new-key.pem
```

---

#### Step 2: Stop the affected instance  
In the AWS Console or CLI:
```bash
aws ec2 stop-instances --instance-ids i-xxxxxxxxxxxxxxx
```

---

#### Step 3: Detach the root EBS volume from the stopped instance

- Go to **EC2 > Volumes**
- Find the volume attached to your instance (usually `/dev/xvda`)
- **Detach** it

---

#### Step 4: Attach the volume to a temporary (working) instance  
- Attach it as a secondary volume (e.g., `/dev/sdf`)

---

#### Step 5: SSH into the temporary instance  
Mount the volume:
```bash
sudo mkdir /mnt/recovery
sudo mount /dev/xvdf1 /mnt/recovery
```

Edit the `authorized_keys` file on the broken instance's volume:
```bash
sudo nano /mnt/recovery/home/ec2-user/.ssh/authorized_keys
```

Add the **public key** from your new key pair (`new-key.pub`)

---

#### Step 6: Detach the volume from the rescue instance  
- Unmount the volume  
- Detach it and re-attach to the original instance as `/dev/xvda`

---

#### Step 7: Start the original instance and SSH with new key  
```bash
ssh -i new-key.pem ec2-user@<public-ip>
```

---

### 🧠 Prevention Tips:

- Always back up PEM files in a secure location (like a password manager).
- Create a **secondary user** with a different key for backup access.
- Use EC2 Instance Connect for temporary browser-based access (only works for Amazon Linux 2+ and enabled roles).

> Summary:  
> You cannot restore a PEM file, but you can regain access by editing the `authorized_keys` on the root volume through a temporary rescue EC2 instance.

---
# Summarize

## What will you do if you lose the PEM file of an EC2 instance?

This is a common scenario-based interview question.

### Scenario

You created an AWS EC2 instance and selected the option to create a new PEM file during instance creation.

You downloaded the PEM file, but later accidentally deleted it from your local machine. There is no backup available.

The interviewer may ask:

1. Can you restore the deleted PEM file?
2. How will you connect to the EC2 instance now?

---

### Understanding the PEM File

Before answering, explain what a PEM file is.

A PEM file contains the **private key** used for SSH authentication.

To connect to a Linux instance through SSH, you generally need either:

* A password, or
* A private key

Example:

```bash
ssh -i private-key.pem ec2-user@<instance-ip>
```

In most cloud environments, password authentication is disabled for security reasons, so SSH access relies on the private key.

---

### Can the Deleted PEM File Be Restored?

**No.**

Once the PEM file is lost, it cannot be restored from AWS because AWS does not store a copy of the private key after it is generated and downloaded.

This is an important point to mention in the interview.

---

### Can You Still Access the EC2 Instance?

**Yes.**

Even though the original PEM file cannot be recovered, you can still regain access to the instance if alternative access methods are enabled.

For example:

* AWS Session Manager
* EC2 Instance Connect

---

### Recommended Approach

If Session Manager or EC2 Instance Connect is enabled:

#### Step 1: Connect to the Instance

Open the AWS Console.

Navigate to the EC2 instance and click **Connect**.

You may see options such as:

* EC2 Instance Connect
* Session Manager
* SSH Client

Use either EC2 Instance Connect or Session Manager to open a terminal session.


#### Step 2: Generate a New SSH Key Pair

Inside the instance, generate a new key pair:

```bash
ssh-keygen -t rsa
```

This creates:

```bash
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

* `id_rsa` → Private key
* `id_rsa.pub` → Public key


#### Step 3: Add the Public Key to authorized_keys

Append the generated public key to the authorized keys file:

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Or manually copy the contents of `id_rsa.pub` into:

```bash
~/.ssh/authorized_keys
```


#### Step 4: Copy the Private Key

Display the private key:

```bash
cat ~/.ssh/id_rsa
```

Copy its contents and save it securely on your local machine.

For example:

```bash
new-key.pem
```


#### Step 5: Set Correct Permissions

On your local machine:

```bash
chmod 400 new-key.pem
```


#### Step 6: Connect Using the New Key

```bash
ssh -i new-key.pem ec2-user@<instance-ip>
```

You should now be able to access the instance successfully.


### Interview Answer

> If the PEM file is lost, it cannot be restored because AWS does not keep a copy of the private key. However, if EC2 Instance Connect or Session Manager is enabled, I can access the instance through those services, generate a new SSH key pair using `ssh-keygen`, add the new public key to the `authorized_keys` file, securely save the new private key on my local machine, and then use that key to connect via SSH. Therefore, while the original PEM file cannot be recovered, access to the instance can still be restored.

This answer demonstrates both your understanding of SSH authentication and your ability to recover access to cloud instances in real-world situations.

---
