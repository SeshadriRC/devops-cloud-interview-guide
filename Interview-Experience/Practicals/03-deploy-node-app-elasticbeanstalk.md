This is a common **DevOps interview** question. The interviewer wants to evaluate your knowledge of **GitHub Actions, Node.js, AWS CLI, Elastic Beanstalk, CI/CD, and secrets management**.

---

# Solution

Suppose your Node.js application has the following structure:

```text
node-app/
│
├── app.js
├── package.json
├── package-lock.json
├── tests/
├── .github/
│    └── workflows/
│          └── deploy.yml
└── Procfile
```

The workflow file is placed at:

```text
.github/workflows/deploy.yml
```

---

# GitHub Actions Workflow

```yaml
name: Node.js CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:

  build-test-deploy:

    runs-on: ubuntu-latest

    steps:

    # Step 1: Checkout code
    - name: Checkout Source Code
      uses: actions/checkout@v4

    # Step 2: Install Node.js
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: 20

    # Step 3: Install dependencies
    - name: Install Dependencies
      run: npm install

    # Step 4: Build application
    - name: Build Application
      run: npm run build

    # Step 5: Run Unit Tests
    - name: Execute Unit Tests
      run: npm test

    # Step 6: Configure AWS Credentials
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ap-south-1

    # Step 7: Create deployment package
    - name: Create ZIP Package
      run: zip -r app.zip .

    # Step 8: Deploy to Elastic Beanstalk
    - name: Deploy Application
      run: |
        aws s3 cp app.zip s3://my-eb-bucket/app.zip

        aws elasticbeanstalk create-application-version \
          --application-name my-node-app \
          --version-label ${{ github.sha }} \
          --source-bundle S3Bucket="my-eb-bucket",S3Key="app.zip"

        aws elasticbeanstalk update-environment \
          --environment-name my-node-env \
          --version-label ${{ github.sha }}
```

---

# Explanation of Each Step

### Trigger

```yaml
on:
  push:
    branches:
      - main
```

Whenever code is pushed to the `main` branch, the pipeline starts.

---

### Checkout Repository

```yaml
uses: actions/checkout@v4
```

Downloads the latest source code from GitHub into the GitHub Actions runner.

---

### Setup Node.js

```yaml
uses: actions/setup-node@v4
```

Installs Node.js.

Example:

```yaml
with:
  node-version: 20
```

---

### Install Dependencies

```yaml
run: npm install
```

Reads `package.json` and installs required packages.

Equivalent to:

```bash
npm install
```

---

### Build Application

```yaml
run: npm run build
```

Runs the build script defined in `package.json`.

Example:

```json
"scripts": {
  "build": "webpack"
}
```

or

```json
"build": "tsc"
```

If your application doesn't have a build step (many simple Express.js apps don't), you can omit this step.

---

### Run Tests

```yaml
run: npm test
```

Executes unit tests.

Example:

```json
"scripts": {
    "test": "jest"
}
```

If any test fails:

* Pipeline stops.
* Deployment does **not** happen.

This is the **Continuous Integration (CI)** part.

---

### Configure AWS Credentials

```yaml
uses: aws-actions/configure-aws-credentials@v4
```

Uses GitHub Secrets.

```text
AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY
```

Never hardcode credentials in the workflow.

---

### Create ZIP

```bash
zip -r app.zip .
```

Elastic Beanstalk expects the application as a ZIP archive (unless using another deployment method).

---

### Upload to S3

```bash
aws s3 cp app.zip s3://my-eb-bucket/app.zip
```

Uploads the deployment package to an S3 bucket.

---

### Create New Version

```bash
aws elasticbeanstalk create-application-version
```

Registers the uploaded ZIP as a new application version.

---

### Deploy

```bash
aws elasticbeanstalk update-environment
```

Updates the Elastic Beanstalk environment to use the newly created application version.

This is the **Continuous Deployment (CD)** part.

---

# Complete Flow

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Checkout Code
    │
    ├── Install Node.js
    │
    ├── npm install
    │
    ├── npm run build
    │
    ├── npm test
    │
    ├── Configure AWS CLI
    │
    ├── Zip Application
    │
    ├── Upload ZIP to S3
    │
    ├── Create Elastic Beanstalk Version
    │
    └── Deploy to Elastic Beanstalk
```

---

# Interview Explanation (2–3 Minutes)

> "In this GitHub Actions pipeline, the workflow is triggered whenever code is pushed to the `main` branch. The runner first checks out the repository, installs the required Node.js version, and installs project dependencies using `npm install`. It then builds the application (if applicable) and runs unit tests with `npm test`. If any test fails, the workflow stops and the deployment is skipped, ensuring only validated code is released. If the tests pass, AWS credentials are securely loaded from GitHub Secrets using the AWS credentials action. The application is packaged into a ZIP file, uploaded to an S3 bucket, registered as a new Elastic Beanstalk application version, and finally the Elastic Beanstalk environment is updated to deploy the new version. This pipeline implements Continuous Integration by automatically building and testing changes, and Continuous Deployment by releasing successful builds to AWS Elastic Beanstalk."



# What is elasticbeanstalk


**AWS Elastic Beanstalk** is a **Platform as a Service (PaaS)** offered by Amazon Web Services that makes it easy to deploy, manage, and scale web applications without manually managing the underlying infrastructure.

In simple terms:

> **You provide your application code, and Elastic Beanstalk automatically provisions and manages the AWS infrastructure needed to run it.**

---

## Without Elastic Beanstalk

Suppose you want to deploy a Node.js application.

You would need to manually:

* Create an EC2 instance
* Install Node.js
* Configure Nginx or Apache (optional)
* Configure Security Groups
* Create an IAM Role
* Create an Application Load Balancer (if needed)
* Configure Auto Scaling
* Configure CloudWatch monitoring
* Deploy the application
* Handle updates and scaling

Architecture:

```text
Developer
    │
    ▼
EC2
 ├── Install Node.js
 ├── Configure Nginx
 ├── Configure Security Groups
 ├── Configure Load Balancer
 ├── Configure Auto Scaling
 └── Deploy App
```

This requires a lot of manual work.

---

# With Elastic Beanstalk

You simply upload your application.

Elastic Beanstalk automatically:

* Creates EC2 instances
* Installs the runtime (Node.js, Java, Python, etc.)
* Creates an Auto Scaling Group
* Creates a Load Balancer (if required)
* Configures Security Groups
* Sets up monitoring
* Deploys the application

Architecture:

```text
Developer
     │
Upload ZIP
     │
     ▼
Elastic Beanstalk
     │
     ├── EC2
     ├── Auto Scaling
     ├── Load Balancer
     ├── Security Groups
     ├── CloudWatch
     └── Deploy Application
```

---

# Supported Platforms

Elastic Beanstalk supports applications built with:

* Node.js
* Java
* Python
* PHP
* Ruby
* Go
* .NET
* Docker

---

# Example

Suppose you have a simple Express application:

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Welcome");
});

app.listen(3000);
```

You zip the project:

```text
myapp.zip
```

Upload it to Elastic Beanstalk.

Elastic Beanstalk automatically:

* Launches an EC2 instance
* Installs Node.js
* Starts your application
* Makes it accessible through a public URL

You don't manually SSH into the server to configure everything.

---

# Under the Hood

When you create an environment, Elastic Beanstalk creates AWS resources such as:

* EC2 Instance(s)
* Elastic Load Balancer (optional)
* Auto Scaling Group
* Security Groups
* CloudWatch Alarms
* IAM Roles
* S3 bucket (for storing application versions)

So, Elastic Beanstalk is not a new compute service—it orchestrates existing AWS services for you.

---

# Deployment Flow

```text
Developer
     │
git push
     │
     ▼
GitHub Actions
     │
     ▼
Create ZIP
     │
     ▼
Upload to Elastic Beanstalk
     │
     ▼
Elastic Beanstalk
     │
     ├── Creates EC2
     ├── Installs Node.js
     ├── Deploys Code
     ├── Starts Application
     └── Gives Public URL
```

---

# Real-Time Example

Suppose your company develops an employee portal.

Every time developers merge code into the `main` branch:

1. GitHub Actions builds the application.
2. Unit tests are executed.
3. If tests pass, the application is packaged into a ZIP file.
4. The ZIP is uploaded to Elastic Beanstalk.
5. Elastic Beanstalk deploys the new version to its EC2 instances.
6. If Auto Scaling is enabled, it adjusts the number of instances based on traffic.

Developers focus on writing code rather than provisioning and maintaining infrastructure.

---

# Elastic Beanstalk vs EC2

| EC2                              | Elastic Beanstalk                                |
| -------------------------------- | ------------------------------------------------ |
| You manage the server            | AWS manages most of the infrastructure           |
| Install software manually        | Runtime installed automatically                  |
| Configure Auto Scaling manually  | Auto Scaling can be configured through Beanstalk |
| Configure Load Balancer manually | Can create and manage a Load Balancer            |
| Manual deployments               | Simplified application deployments               |
| More control                     | Less operational overhead                        |


## Interview Answer (1–2 Minutes)

> "AWS Elastic Beanstalk is a Platform as a Service that simplifies deploying and managing web applications. Instead of manually creating and configuring EC2 instances, load balancers, Auto Scaling groups, and monitoring, I upload my application code, and Elastic Beanstalk provisions and manages the required AWS resources. It supports platforms such as Node.js, Java, Python, Docker, and .NET. While it automates much of the infrastructure management, the underlying AWS resources remain in my account, and I can still access and customize them if needed."


