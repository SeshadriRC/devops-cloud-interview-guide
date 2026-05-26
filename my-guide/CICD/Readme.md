**1. Can you explain now what exactly your or how exactly your CI/CD pipeline looks like and what are the different stages that are involved what is your end goal ?**

### CI/CD Pipeline Summary

* Using GitHub Actions for Continuous Integration (CI) and Argo CD for Continuous Deployment (CD)
* CI/CD pipeline is triggered automatically whenever developers push code changes
* Static code analysis stage checks:

  * Unused variables/functions
  * Linting issues
  * Vulnerable modules
* Unit testing stage validates newly added and existing test cases
* Build stage compiles the application and creates artifacts
* Docker stage creates container images for the application
* Security scanning performed using Snyk to identify vulnerabilities in Docker images
* Container images are pushed to centralized registries like:

  * Amazon Elastic Container Registry (ECR)
  * Azure Container Registry (ACR)
* Helm manifests/values are updated with the latest image version
* Argo CD reads updated Helm values and deploys applications to Kubernetes namespaces
* Pipeline is actively maintained with updated GitHub Actions versions and security practices
* Overall process follows DevSecOps principles with automation, testing, security, and continuous deployment


---
