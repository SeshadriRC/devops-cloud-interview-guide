In **Jenkins**, manual approval is implemented using the **`input`** step in a Pipeline. When the pipeline reaches the `input` step, it pauses until an authorized user approves or aborts it.

## Example Pipeline

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t product-catalog:${BUILD_NUMBER} .'
            }
        }

        stage('Approval for Production') {
            steps {
                input message: 'Deploy to Production?', ok: 'Approve'
            }
        }

        stage('Deploy to Production') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
```

### What happens?

1. Build completes.
2. Tests complete.
3. Docker image is built.
4. Jenkins pauses at the **Approval** stage.
5. An authorized user clicks **Approve**.
6. Deployment continues.

---

## Restrict Approval to Specific Users

You can allow only certain users to approve:

```groovy
stage('Approval') {
    steps {
        input(
            message: 'Deploy to Production?',
            ok: 'Deploy',
            submitter: 'devopsadmin,release_manager'
        )
    }
}
```

Only `devopsadmin` or `release_manager` can approve.

---

## Capture User Input

You can also collect values during approval.

```groovy
stage('Approval') {
    steps {
        script {
            def version = input(
                message: 'Approve Deployment',
                parameters: [
                    string(
                        name: 'VERSION',
                        defaultValue: '1.0.0',
                        description: 'Enter image version'
                    )
                ]
            )

            echo "Deploying version ${version}"
        }
    }
}
```

The approver enters a version, which can then be used in later stages.

---

## Real-World Pipeline

A production pipeline often looks like this:

```text
Developer
     │
     ▼
Git Push
     │
     ▼
Jenkins Trigger
     │
     ▼
Checkout Code
     │
     ▼
Compile
     │
     ▼
Unit Tests
     │
     ▼
SonarQube Analysis
     │
     ▼
Quality Gate
     │
     ▼
Build Docker Image
     │
     ▼
Push Image to Registry
     │
     ▼
Deploy to DEV (Automatic)
     │
     ▼
Integration Tests
     │
     ▼
Deploy to UAT (Automatic)
     │
     ▼
───────────────
Manual Approval
(Release Manager)
───────────────
     │
     ▼
Deploy to Production
     │
     ▼
Health Check
     │
     ▼
Success Notification
```

## Interview Answer (2–3 lines)

> "In Jenkins, manual approval is implemented using the `input` step in a Pipeline. The pipeline pauses at that stage until an authorized user approves or rejects the deployment. We typically place this approval before deploying to production and can restrict it to specific users or groups using the `submitter` option."
