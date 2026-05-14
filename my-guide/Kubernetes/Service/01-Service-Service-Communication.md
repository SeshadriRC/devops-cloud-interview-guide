
### If the interviewer asks how one service connects to another in Kubernetes, you can explain it like this:

[repo-link](https://github.com/SeshadriRC/ultimate-devops-project-demo/blob/main/kubernetes/quote/deploy.yaml)  -> go through the yaml files of `shipping` and `quote` deployments.

Suppose the **Shipping Service** needs to connect to the **Quote Service**.

In Kubernetes, services usually communicate using:

* Environment Variables
* ConfigMaps
* Service Discovery (most common)

In our setup, we use **environment variables**.

For example, inside the Shipping application deployment, we define environment variables like:

```yaml
env:
  - name: QUOTE_SERVICE_HOST
    value: quote-service

  - name: QUOTE_SERVICE_PORT
    value: "8080"
```

Here:

* `quote-service` is the Kubernetes Service name
* Kubernetes DNS automatically resolves this service name
* This is called **Service Discovery in Kubernetes**

So the Shipping service connects to the Quote service using:

```bash
http://quote-service:8080
```

Instead of hardcoding IP addresses.

You can also mention:

> Kubernetes automatically provides internal DNS-based communication between services, making microservice connectivity easier and scalable.

Typically this configuration can be managed through:

* Environment Variables
* ConfigMaps
* Secrets (for sensitive data)

In our project, we mainly use environment variables, which is a very common approach.
