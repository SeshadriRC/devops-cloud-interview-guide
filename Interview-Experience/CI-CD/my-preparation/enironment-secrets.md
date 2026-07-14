This is a very common enterprise setup. The idea is that **the same Jenkins pipeline** is used for **DEV, QA, and PROD**, and the pipeline fetches **environment-specific secrets** from HashiCorp Vault.

The implementation generally looks like this:

```text
Developer
     │
     ▼
Git Push
     │
     ▼
Jenkins Pipeline
     │
     ▼
User selects Environment (DEV/QA/PROD)
     │
     ▼
Authenticate to Vault
     │
     ▼
Read secrets from the selected Vault path
     │
     ▼
Inject secrets as environment variables
     │
     ▼
Build / Deploy
```

---

# Step 1: Organize Secrets in Vault

A common structure is:

```text
secret/
   dev/
      app/
         db_username
         db_password
         api_key

   qa/
      app/
         db_username
         db_password
         api_key

   prod/
      app/
         db_username
         db_password
         api_key
```

or KV v2:

```text
kv/data/dev/app
kv/data/qa/app
kv/data/prod/app
```

Each environment has its own secrets.

---

# Step 2: Add Environment Parameter in Jenkins

```groovy
parameters {
    choice(
        name: 'ENV',
        choices: ['dev', 'qa', 'prod'],
        description: 'Select deployment environment'
    )
}
```

Now the user chooses:

```
ENV = dev
```

or

```
ENV = qa
```

or

```
ENV = prod
```

---

# Step 3: Construct the Vault Path

Instead of hardcoding:

```text
secret/dev/app
```

build it dynamically:

```groovy
def vaultPath = "secret/${params.ENV}/app"
```

If:

```
ENV=dev
```

then

```
secret/dev/app
```

If:

```
ENV=prod
```

then

```
secret/prod/app
```

---

# Step 4: Authenticate Jenkins with Vault

There are several authentication methods:

* AppRole (very common for Jenkins)
* Kubernetes Auth
* AWS IAM Auth
* Token (not recommended for production)

In most enterprises, **AppRole** is used.

Jenkins authenticates using:

```
Role ID
Secret ID
```

Vault returns a temporary token.

---

# Step 5: Read the Secrets

Using the Jenkins HashiCorp Vault Plugin:

```groovy
def secrets = [
  [
    path: "secret/${params.ENV}/app",
    secretValues: [
      [envVar: 'DB_USER', vaultKey: 'db_username'],
      [envVar: 'DB_PASS', vaultKey: 'db_password']
    ]
  ]
]

withVault(vaultSecrets: secrets) {
    sh '''
    echo "Deploying..."
    '''
}
```

Inside `withVault`, Jenkins exports:

```
DB_USER
DB_PASS
```

as environment variables for the duration of that block.

---

# Step 6: Use the Secrets

For example:

```groovy
sh '''
java -jar app.jar \
--db.user=$DB_USER \
--db.password=$DB_PASS
'''
```

or

```groovy
sh '''
helm upgrade app chart \
--set db.username=$DB_USER \
--set db.password=$DB_PASS
'''
```

The secrets are **not stored in Git** or hardcoded in the Jenkinsfile.

---

# Step 7: Restrict Access with Vault Policies

Even though the pipeline accepts `ENV`, you don't want every Jenkins job to read every environment.

Example policies:

**Dev Jenkins**

```
path "secret/data/dev/*" {
  capabilities = ["read"]
}
```

**QA Jenkins**

```
path "secret/data/qa/*" {
  capabilities = ["read"]
}
```

**Prod Jenkins**

```
path "secret/data/prod/*" {
  capabilities = ["read"]
}
```

This follows the principle of least privilege.

---

# Best Practice

Avoid letting any user simply choose `prod`. Instead:

* Developers deploy to DEV.
* QA team deploys to QA.
* Only release managers or approved pipelines deploy to PROD.
* Use Jenkins RBAC and manual approval gates before production deployment.

---

# Example Pipeline Flow

```text
Start Pipeline
      │
      ▼
Choose Environment
      │
      ├── dev
      ├── qa
      └── prod
      │
      ▼
Authenticate with Vault (AppRole)
      │
      ▼
Read:
secret/dev/app
OR
secret/qa/app
OR
secret/prod/app
      │
      ▼
Inject Secrets
      │
      ▼
Deploy Application
```

---

# Interview Answer (2 Minutes)

> "In our CI/CD pipeline, we keep separate secrets for each environment in HashiCorp Vault, such as `secret/dev/app`, `secret/qa/app`, and `secret/prod/app`. The Jenkins pipeline accepts the target environment as a parameter and dynamically constructs the Vault path based on that value. Jenkins authenticates to Vault using AppRole, retrieves only the required secrets for that environment through the HashiCorp Vault plugin, and injects them as temporary environment variables during the deployment. The secrets are never stored in Git or hardcoded in the Jenkinsfile. We also use Vault policies and Jenkins RBAC so that production secrets can only be accessed by authorized production pipelines."

This is the pattern you'll see in most enterprise Jenkins + Vault deployments.
