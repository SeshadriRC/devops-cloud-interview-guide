````md id="y9d0x4"
# Different Types of Secrets in Kubernetes

This is a very common Kubernetes interview question.

Kubernetes provides different types of secrets to store various forms of sensitive information such as:
- Database credentials
- API tokens
- Docker registry credentials
- SSH private keys
- TLS certificates

---

# Why Kubernetes Provides Multiple Secret Types

Different secret types help with:
- Better organization
- Proper formatting
- Specific use cases
- Easier integration with Kubernetes components

---

# 1. Opaque Secret

## Purpose

Opaque is the default and most commonly used secret type.

Used for storing:
- Username/password
- Database credentials
- API keys
- Generic key-value pairs

---

## Example YAML

```yaml id="mjlwmx"
apiVersion: v1
kind: Secret

metadata:
  name: db-secret

type: Opaque

data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
````

(Base64 encoded values)

---

## Common Use Cases

* MySQL username/password
* PostgreSQL credentials
* API tokens
* Generic application secrets

---

# 2. Docker Registry Secret

## Purpose

Used for storing:

* Container registry credentials

Examples:

* Docker Hub
* AWS ECR
* Azure ACR
* GCR

This allows Kubernetes to pull private container images.

---

## Secret Type

```text id="5b1tqj"
kubernetes.io/dockerconfigjson
```

---

## Example YAML

```yaml id="5s4u1i"
apiVersion: v1
kind: Secret

metadata:
  name: docker-registry-secret

type: kubernetes.io/dockerconfigjson

data:
  .dockerconfigjson: <base64-encoded-docker-config-json>
```

---

## Common Use Cases

* Pulling private Docker images
* ECR authentication
* Docker Hub private repositories

---

# 3. Basic Authentication Secret

## Purpose

Used for storing:

* Username and password for basic authentication

---

## Secret Type

```text id="jlwm6n"
kubernetes.io/basic-auth
```

---

## Example YAML

```yaml id="c2rjys"
apiVersion: v1
kind: Secret

metadata:
  name: basic-auth-secret

type: kubernetes.io/basic-auth

stringData:
  username: admin
  password: mypassword
```

---

## Common Use Cases

* Tool authentication
* Application login credentials
* API basic authentication

---

# 4. SSH Authentication Secret

## Purpose

Used for storing:

* SSH private keys

Typically used for:

* Git access
* SSH-based authentication
* Secure Linux server access

---

## Secret Type

```text id="zzg0vg"
kubernetes.io/ssh-auth
```

---

## Example YAML

```yaml id="u6h5y5"
apiVersion: v1
kind: Secret

metadata:
  name: ssh-key-secret

type: kubernetes.io/ssh-auth

stringData:
  ssh-privatekey: |
    -----BEGIN OPENSSH PRIVATE KEY-----
    YOUR_PRIVATE_KEY_CONTENT
    -----END OPENSSH PRIVATE KEY-----
```

---

## Common Use Cases

* GitHub private repository access
* SSH authentication
* Secure server access

---

# 5. TLS Secret

## Purpose

Used for storing:

* TLS certificates
* HTTPS certificates
* SSL private keys

Commonly used with:

* Ingress
* HTTPS applications

---

## Secret Type

```text id="0usjlwm"
kubernetes.io/tls
```

---

## Example YAML

```yaml id="31pglq"
apiVersion: v1
kind: Secret

metadata:
  name: tls-secret

type: kubernetes.io/tls

data:
  tls.crt: <base64-encoded-certificate>
  tls.key: <base64-encoded-private-key>
```

---

## Common Use Cases

* HTTPS Ingress
* SSL certificates
* Secure communication

---

# 6. Service Account Token Secret

## Purpose

Used for:

* Authentication between Pods and Kubernetes API Server

Kubernetes can automatically generate these secrets.

---

## Secret Type

```text id="1n7jzl"
kubernetes.io/service-account-token
```

---

## Example YAML

```yaml id="bgrps6"
apiVersion: v1
kind: Secret

metadata:
  name: sa-token-secret
  annotations:
    kubernetes.io/service-account.name: my-service-account

type: kubernetes.io/service-account-token
```

---

# Summary Table

| Secret Type                         | Purpose                          |
| ----------------------------------- | -------------------------------- |
| Opaque                              | Generic key-value secrets        |
| kubernetes.io/dockerconfigjson      | Docker registry credentials      |
| kubernetes.io/basic-auth            | Username/password authentication |
| kubernetes.io/ssh-auth              | SSH private keys                 |
| kubernetes.io/tls                   | TLS/SSL certificates             |
| kubernetes.io/service-account-token | Kubernetes API authentication    |

---

# Easy Interview Answer

“Kubernetes provides multiple types of secrets for storing sensitive information securely.

* Opaque secrets are used for generic key-value data like DB credentials.
* Docker registry secrets store container registry credentials.
* Basic-auth secrets store username/password pairs.
* SSH-auth secrets store SSH private keys.
* TLS secrets store SSL/TLS certificates.
* Service account token secrets are used for Kubernetes API authentication.

These secret types help organize and securely manage different categories of sensitive information.”

```
```

