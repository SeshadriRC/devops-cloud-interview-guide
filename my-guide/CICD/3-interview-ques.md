Q1: Explain your current CI/CD Setup

There are many CI tools, I will consider Jenkins to answer this.

CI/CD is a process of continuous integration and continuous delivery. In the current organization, we use GitHub as a source code repository. We have configured a GitHub
webhook that triggers a Jenkins Pipeline on every commit, The Jenkins pipeline is set up with multiple stages.

1. Build Stage - Takes care of Static Code Analysis, Unit testing and Build.

2. Test Stage - Running smoke and functional tests.

3. Deploy Stage - Push artifacts to the registry, prepare deployment manifests and deploy on
the cluster.

---

Q2: How do you Handle Secrets ?

Secrets exist throughout the delivery lifecycle as application secrets, credentials, and other sensitive information should be secured, obfuscated, and governed.

· Code repositories like GitHub, GitLab, and BitBucket.
· Continuous Integration servers and Continuous Delivery platforms - Jenkins, Bamboo, JFrog, TeamCity, Drone, Harness, GitLab CI/CD, CircleCI, and so on.
· Cloud Providers and Container Platforms - AWS, GCP, Azure, Kubernetes (as keys,
configuration files, and other information).

· Infrastructure - (SSH keys, load balancer credentials).

Tools: Hashicorp Vault, AWS Systems Manager, Azure Vault

---

<img width="1919" height="1064" alt="image" src="https://github.com/user-attachments/assets/e90148e2-6212-4ab9-859c-50b711b22a85" />

---

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2f347bb7-f6fa-4e8d-acc3-b01893859ca7" />

---
