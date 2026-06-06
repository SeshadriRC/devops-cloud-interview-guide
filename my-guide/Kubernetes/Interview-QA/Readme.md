[**Scenario-based-Ques**](https://github.com/SeshadriRC/DevOps-Docs/blob/main/Kubernetes/k8s%20scenario%20Q%26A.md)

---
**How do you manage your Kubernetes clusters in this case cuz you said you have a DevOps engineer dedicated to a development team but end of the day all these microservices from different development teams I hope are deployed to the common cluster so how exactly you manage these clusters can you walk me through that**


- this is where the centralized nature of our DevOps team comes into picture although I work for a single development team we cannot create a Kubernetes cluster for each development team because that would be huge costing for the organization and also a lot of maintenance activity so what we do as a centralized DevOps team we have set up a Kubernetes cluster per environment so we have dev environment staging or pro and production environment for each environment there is a Kubernetes cluster and as a DevOps engineer I make sure the microservices of my development team are deployed to a particular name space so we use namespace in Kubernetes per isolation for example customer experience microservices are deployed to customer experience namespace on the dev cluster customer experience namespace on the staging and also customer experience on production


---

**Service-to-Service-communication-Kubernetes**

[refer](https://github.com/SeshadriRC/devops-cloud-interview-guide/blob/main/my-guide/Kubernetes/Service/01-Service-Service-Communication.md)

---

**How the User request is travelled**

[link](https://github.com/SeshadriRC/devops-cloud-interview-guide/blob/main/my-guide/Kubernetes/Concepts/user-request-travel.md)

---
