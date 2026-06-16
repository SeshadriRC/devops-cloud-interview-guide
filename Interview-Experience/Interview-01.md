SCENARIO 1: Developer shares a Python Flask application. You must:

This is a common DevOps interview scenario — package the app → deploy to Kubernetes → provision infrastructure in AWS.

Assume:

* Flask app file → `app.py`
* Requirements file → `requirements.txt`
* Docker image → pushed to DockerHub
* Kubernetes cluster already exists (EKS/minikube)

Project structure:

```plaintext
flask-app/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── deployment.yaml
├── service.yaml
├── main.tf
```

---

# 1. requirements.txt

```txt
flask
gunicorn
```

---

# 2. Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

Build:

```bash
docker build -t flask-app .
```

Run:

```bash
docker run -p 5000:5000 flask-app
```

Push to DockerHub:

```bash
docker tag flask-app username/flask-app:v1

docker push username/flask-app:v1
```

---

# 3. Kubernetes Deployment

### deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: flask-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: flask

  template:
    metadata:
      labels:
        app: flask

    spec:
      containers:
      - name: flask-container

        image: username/flask-app:v1

        ports:
        - containerPort: 5000
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Verify:

```bash
kubectl get pods
```

---

# 4. Kubernetes Service

### service.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: flask-service

spec:
  type: LoadBalancer

  selector:
    app: flask

  ports:
  - port: 80
    targetPort: 5000
```

Apply:

```bash
kubectl apply -f service.yaml
```

Check:

```bash
kubectl get svc
```

Flow:

```plaintext
Internet
   ↓
LoadBalancer Service
   ↓
Deployment
   ↓
Pods
   ↓
Flask Container
```

---

# 5. Terraform to create AWS EC2

### provider.tf

```terraform
provider "aws" {
  region = "ap-south-1"
}
```

---

### main.tf

```terraform
resource "aws_instance" "flask_server" {

  ami           = "ami-0f58b397bc5c1f2e8"
  instance_type = "t2.micro"

  tags = {
    Name = "Flask-App"
  }
}
```

Initialize:

```bash
terraform init
```

Preview:

```bash
terraform plan
```

Create:

```bash
terraform apply
```

Destroy:

```bash
terraform destroy
```

---

### End-to-end workflow

```plaintext
Developer → Flask Code
        ↓
Dockerfile → Build Image
        ↓
Push to DockerHub
        ↓
Kubernetes Deployment
        ↓
Service exposes app
        ↓
Terraform creates AWS infra
```

Interview explanation (short):

> "First I containerize the Flask application using Docker, push the image to registry, deploy using Kubernetes Deployment and Service, and provision AWS infrastructure using Terraform."

---

# Scenario 2: Write Terraform code to create an EC2 instance where AMI ID and instance type are configurable.


For interview purpose, if AMI ID and instance type should be configurable, use **variables**.

Project structure:

```plaintext
terraform/
│
├── provider.tf
├── variables.tf
├── main.tf
├── terraform.tfvars
```

### provider.tf

```hcl
provider "aws" {
  region = "ap-south-1"
}
```


### variables.tf

```hcl
variable "ami_id" {
  description = "AMI ID for EC2"
  type        = string
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
}
```

### main.tf

```hcl
resource "aws_instance" "my_ec2" {

  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = "Terraform-EC2"
  }
}
```


### terraform.tfvars

```hcl
ami_id        = "ami-0f58b397bc5c1f2e8"
instance_type = "t2.micro"
```

---

Commands:

Initialize:

```bash
terraform init
```

Preview:

```bash
terraform plan
```

Create:

```bash
terraform apply
```

Destroy:

```bash
terraform destroy
```

### How it works

```plaintext
terraform.tfvars
     ↓
variables.tf
     ↓
main.tf
     ↓
AWS EC2 created
```

`var.ami_id` → reads value from `terraform.tfvars`
`var.instance_type` → reads value from `terraform.tfvars`

This approach avoids hardcoding values and makes the code reusable.

---

# Declarative and Scripted pipeline

In Jenkins, **Declarative Pipeline** and **Scripted Pipeline** are two ways to write CI/CD pipelines.

## 1. Declarative Pipeline

* Uses a **structured and simple syntax**
* Easier to read and maintain
* Best for **standard CI/CD workflows**
* Has predefined blocks like `pipeline`, `agent`, `stages`, `steps`

Example:

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building app'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying app'
            }
        }
    }
}
```

Flow:

```plaintext
pipeline
  ↓
stages
  ↓
steps
```

### Advantages

* Easy syntax
* Less coding
* Built-in validation
* Better for beginners/interview scenarios

---

## 2. Scripted Pipeline

* Uses **Groovy scripting**
* More flexible and programmable
* Suitable for **complex workflows**
* You control logic manually

Example:

```groovy
node {

    stage('Build') {
        echo 'Building app'
    }

    stage('Test') {
        echo 'Running tests'
    }

    stage('Deploy') {
        echo 'Deploying app'
    }

}
```

Example with conditions:

```groovy
node {

    if (env.BRANCH_NAME == "main") {
        stage('Deploy') {
            echo 'Deploy to production'
        }
    }

}
```

---

## Difference

| Feature     | Declarative    | Scripted          |
| ----------- | -------------- | ----------------- |
| Syntax      | Simple         | Groovy script     |
| Structure   | Fixed          | Flexible          |
| Learning    | Easy           | Moderate          |
| Logic       | Limited        | Advanced          |
| Readability | High           | Medium            |
| Use case    | Standard CI/CD | Complex workflows |
| Starts with | `pipeline {}`  | `node {}`         |

### Interview short answer:

> Declarative pipeline is structured and easier to maintain using `pipeline {}` syntax, whereas Scripted pipeline uses Groovy with `node {}` and provides more flexibility for complex CI/CD logic.


## so in JenkinsFile is there in github --> it uses declarative or scripted ?


A **Jenkinsfile in GitHub can use either Declarative or Scripted pipeline** — Jenkins supports both.

When Jenkins pulls the `Jenkinsfile` from GitHub, it simply reads and executes whichever syntax is written inside.

### Example 1: Declarative Jenkinsfile (most common)

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Build started'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying'
            }
        }
    }
}
```

Starts with:

```groovy
pipeline {
```

→ Declarative Pipeline

---

### Example 2: Scripted Jenkinsfile

```groovy
node {

    stage('Build') {
        echo 'Build started'
    }

    stage('Deploy') {
        echo 'Deploying'
    }

}
```

Starts with:

```groovy
node {
```

→ Scripted Pipeline

---

Typical real-world flow:

```plaintext
Developer
   ↓
Push code + Jenkinsfile to GitHub
   ↓
Webhook triggers Jenkins
   ↓
Jenkins reads Jenkinsfile
   ↓
Executes pipeline (Declarative or Scripted)
```

In most projects today, **Declarative Pipeline is more commonly used** because it is easier to maintain and review. Scripted is usually chosen only when the pipeline logic becomes very dynamic or complex.

---

# So what is git rebase and git pull. just explain in simple manner

Think of both as ways to **bring latest changes from GitHub (remote repo) into your local branch**, but they do it differently.

## 1. `git pull`

`git pull` = **Download latest changes + merge them into your branch**

Command:

```bash
git pull origin main
```

What happens:

```plaintext
GitHub (main)
A → B → C

Local
A → B

After git pull

A → B → C → Merge Commit
```

Example:

* Your teammate added code to GitHub.
* You run `git pull`
* Git downloads and merges automatically.

Result:

* History may contain **merge commits**

Simple meaning:

> "Bring latest code and combine it with my code."

---

## 2. `git rebase`

`git rebase` = **Take your changes, move them on top of latest code**

Command:

```bash
git pull --rebase origin main
```

or

```bash
git rebase main
```

What happens:

Before:

```plaintext
GitHub
A → B → C

Your branch
A → B → D
```

After rebase:

```plaintext
A → B → C → D
```

Git:

1. Gets latest changes (`C`)
2. Temporarily removes your commit (`D`)
3. Applies latest code
4. Re-applies your changes

Result:

* Cleaner history
* No extra merge commit

Simple meaning:

> "First update from GitHub, then place my work on top."

---

## Quick difference

|                      | git pull         | git rebase        |
| -------------------- | ---------------- | ----------------- |
| Purpose              | Fetch + Merge    | Reapply commits   |
| Creates merge commit | Yes              | Usually No        |
| History              | Can become messy | Cleaner           |
| Easy to use          | Yes              | Slightly advanced |

Example:

```plaintext
git pull
→ Join two roads

git rebase
→ Move your car to latest road and continue
```

Interview short answer:

> `git pull` fetches and merges remote changes into local branch, whereas `git rebase` moves local commits on top of the latest remote commits to maintain a clean commit history.


---
