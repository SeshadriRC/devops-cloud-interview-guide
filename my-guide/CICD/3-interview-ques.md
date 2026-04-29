Q1: Explain your current CI/CD Setup

There are many CI tools, I will consider Jenkins to answer this.

CI/CD is a process of continuous integration and continuous delivery. In the current organization, we use GitHub as a source code repository. We have configured a GitHub
webhook that triggers a Jenkins Pipeline on every commit, The Jenkins pipeline is set up with multiple stages.

1. Build Stage - Takes care of Static Code Analysis, Unit testing and Build.

2. Test Stage - Running smoke and functional tests.

3. Deploy Stage - Push artifacts to the registry, prepare deployment manifests and deploy on
the cluster.

---
