## Question  
When a build fails in Jenkins, how will you send an email?

### 📝 Short Explanation  
To notify stakeholders or developers about failed builds, Jenkins can send automated emails using the **Email Notification** or **Editable Email Notification** plugin.

## ✅ Answer  

When a Jenkins build fails, I configure it to **automatically send email alerts** using either the **built-in Email Notification** system or the **Email Extension Plugin** for more control.

---

### 🧭 Steps to Configure Email Notifications on Build Failure

#### 1. 🧩 **Install Email Extension Plugin**
- Go to **Manage Jenkins → Plugin Manager → Available**
- Search and install: `Email Extension Plugin`

---

#### 2. ⚙️ **Global Configuration**
Navigate to **Manage Jenkins → Configure System**  
- Set SMTP details:
  - SMTP server (e.g., `smtp.gmail.com`)
  - Use SSL/TLS
  - Port (typically 465 or 587)
  - Jenkins Email address (default sender)
- Set up authentication (username/password or app token)

✅ Example (for Gmail):
```text
SMTP Server: smtp.gmail.com
Use SSL: true
Port: 465
```

---

#### 3. 📤 **Configure Project to Send Emails**
In your Jenkins pipeline job or freestyle job:

- Scroll to **Post-build Actions**
- Add **Editable Email Notification**
  - **Project Recipient List:** e.g., `dev-team@example.com`
  - **Triggers:** Select **Failure - Send email on build failure**

✅ Optional Email Content:
- Subject: `$PROJECT_NAME - Build #$BUILD_NUMBER - FAILED!`
- Body:
```groovy
Build failed at $BUILD_URL
Triggered by: $CAUSE
```

---

#### 4. 🧪 **Testing**
Trigger a dummy failure and confirm that email notifications are received.

---

### 🧠 Real-World Example

In our Jenkins setup:
- We used the **Email Extension Plugin**
- Configured triggers for `FAILURE`, `UNSTABLE`, and `FIXED`
- Used custom HTML templates to include links to logs and commit diffs
- For pull requests, we added author-specific alerts using `git commit --author`

---

> Summary:  
> To notify on build failure:
> - Install and configure the Email Extension Plugin  
> - Set SMTP details under Jenkins global settings  
> - Enable email triggers in your job configuration  
> - Customize recipient list and email templates for clarity

---

# Summarize

<img width="1825" height="878" alt="image" src="https://github.com/user-attachments/assets/c29a2bf1-4147-40b3-9fbb-08f16d727c85" />

## Jenkins Build Failure Email Notification – Interview Summary

### Interview Question

**"When a build fails in Jenkins, how will you send an email?"**

There are two key points:

1. Send the email **only when the build fails**.
2. The interviewer may not specify whether it's a **Freestyle Job** or a **Declarative Pipeline**, so be prepared to explain both.

---

## 1. Freestyle Jenkins Project

### Prerequisite

Ensure the **Email Extension Plugin** is installed.

### Steps

1. Open the Jenkins job.
2. Click **Configure**.
3. Go to **Post-build Actions**.
4. Select **Editable Email Notification**.
5. Configure recipients.
6. Set the trigger to **Failure**.

Result:

* An email is sent only when the build fails.

---

## 2. Declarative Jenkins Pipeline

In a Jenkinsfile, use the **post** section with a **failure** block.

Example:


<img width="1755" height="905" alt="image" src="https://github.com/user-attachments/assets/2373c11e-0806-45b0-a0d2-0b91d348aee5" />


```groovy
post {
    failure {
        mail to: 'team@example.com',
             subject: 'Build Failed',
             body: 'Please check the Jenkins build logs.'
    }
}
```


Result:

* Jenkins sends an email automatically whenever the pipeline fails.

---

## Interview Answer (Short Version)

> For a Freestyle Jenkins job, I would use the Email Extension Plugin and configure an "Editable Email Notification" post-build action with the trigger set to "Failure." For a Declarative Pipeline, I would use the `post { failure { ... } }` section in the Jenkinsfile and configure the `mail` step with the required recipients, subject, and message. This ensures notifications are sent only when the build fails.


---


