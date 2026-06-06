[repo](https://github.com/SeshadriRC/Ansible-VM-Monitor)

Ansible Project To Monitor VMs Health

🔹 Step 1: Update the System

sudo apt update && sudo apt upgrade -y

🔹 Step 2: Add the Ansible PPA

Ansible provides an official maintained PPA (for latest versions):

sudo add-apt-repository --yes --update ppa:ansible/ansible

🔹 Step 3: Install Ansible

sudo apt install ansible -y

# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure

Tagging Script:

```bash
#!/bin/bash

# Fetch instance IDs that match Environment=dev and Role=web
instance_ids=$(aws ec2 describe-instances \
  --filters "Name=tag:Environment,Values=dev" "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text)

# Sort instance IDs deterministically
sorted_ids=($(echo "$instance_ids" | tr '\t' '\n' | sort))
```

# Rename instances sequentially
```bash
counter=1
for id in "${sorted_ids[@]}"; do
  name="web-$(printf "%02d" $counter)"
  echo "Tagging $id as $name"
  aws ec2 create-tags --resources "$id" \
    --tags Key=Name,Value="$name"
  ((counter++))
done
```

# generate ssh key on master

```bash
ssh-keygen -t rsa -b 4096 -C "Ansible-master"
```

ansible.cfg
```bash
[defaults]
inventory = ./inventory/aws_ec2.yaml
host_key_checking = False
```
[ssh_connection]
ssh_args = -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null


## Dynamic Inventory

inventory/aws_ec2.yaml
```yaml
plugin: amazon.aws.aws_ec2
regions:
  - ap-south-1
filters:
  tag:Environment: dev
  instance-state-name: running
compose:
  ansible_host: public_ip_address
keyed_groups:
  - key: tags.Name
    prefix: name
  - key: tags.Environment
    prefix: env                               
```

```bash
# Step 1: Install venv module if not already present
sudo apt install python3-venv -y

# Step 2: Create a virtual environment
python3 -m venv ansible-env

# Step 3: Activate it
source ansible-env/bin/activate

# Step 4: Install required Python packages
pip install boto3 botocore docker
```

```bash
ansible-galaxy collection install amazon.aws


ansible-inventory -i inventory/aws_ec2.yaml --graph
```

Copy Pub Key
#!/bin/bash

# Define vars
PEM_FILE="DevOps-Shack.pem"
PUB_KEY=$(cat ~/.ssh/id_rsa.pub)
USER="ubuntu"  # or ec2-user
INVENTORY_FILE="inventory/aws_ec2.yaml"

# Extract hostnames/IPs from dynamic inventory

```bash
HOSTS=$(ansible-inventory -i $INVENTORY_FILE --list | jq -r '._meta.hostvars | keys[]')

for HOST in $HOSTS; do
  echo "Injecting key into $HOST"
  ssh -o StrictHostKeyChecking=no -i $PEM_FILE $USER@$HOST "
    mkdir -p ~/.ssh && \
    echo \"$PUB_KEY\" >> ~/.ssh/authorized_keys && \
    chmod 700 ~/.ssh && \
    chmod 600 ~/.ssh/authorized_keys
  "
done

```

Create the Project & Run below Command to execute
```bash
Project Repo: https://github.com/jaiswaladi246/Ansible-VM-Monitor.git

ansible-playbook playbook.yaml 
```
