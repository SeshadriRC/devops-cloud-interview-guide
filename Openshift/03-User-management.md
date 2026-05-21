In OpenShift, access is usually provided using:

* users
* groups
* roles
* rolebindings

Most commonly:

```text id="c0zfe0"
User + Role + Namespace
```

---

# Example Scenario

Namespace:

```text id="m9ec7j"
dev-team
```

User:

```text id="t2t4z5"
john
```

You want:

* user `john`
* to access namespace `dev-team`

---

# Command to Give Access

## Developer/Edit Access

```bash id="9f3pgv"
oc policy add-role-to-user edit john -n dev-team
```

This gives:

* pod creation
* deployment creation
* service creation
* application management

inside namespace `dev-team`.

---

# Read-Only Access

```bash id="yw4u0m"
oc policy add-role-to-user view john -n dev-team
```

User can only:

* view resources
* logs
* events

Cannot modify.

---

# Admin Access

```bash id="k4p7jc"
oc policy add-role-to-user admin john -n dev-team
```

Can manage almost everything inside namespace.

---

# Verify RoleBindings

```bash id="hjlwm0"
oc get rolebinding -n dev-team
```

---

# Behind the Scenes

This command:

```bash id="hhq5y3"
oc policy add-role-to-user edit john -n dev-team
```

creates a RoleBinding internally.

Equivalent YAML:

```yaml id="8v0nme"
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding

metadata:
  name: john-edit
  namespace: dev-team

subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io

roleRef:
  kind: ClusterRole
  name: edit
  apiGroup: rbac.authorization.k8s.io
```

Apply using:

```bash id="w3fg6q"
oc apply -f rolebinding.yaml
```

---

# Common OpenShift Roles

| Role            | Purpose             |
| --------------- | ------------------- |
| `view`          | Read-only           |
| `edit`          | Developer access    |
| `admin`         | Namespace admin     |
| `cluster-admin` | Full cluster access |

---

# Group Access (Very Common in Enterprises)

Instead of individual users:

```bash id="vqpc8h"
oc policy add-role-to-group edit dev-team-group -n dev-team
```

This is preferred because:

* easier management
* LDAP/AD integration
* scalable

---

# Check User Permissions

Example:

```bash id="8f8h8u"
oc auth can-i create pods -n dev-team --as=john
```

Very useful troubleshooting command.

---

# Real Enterprise Flow

Usually:

```text id="0hkljr"
LDAP/AD User
    ↓
Mapped to OpenShift Group
    ↓
RoleBinding to Namespace
    ↓
User gets access
```

This is how most enterprise OpenShift environments work.

---

# So that user will be created by some other tool right like IDM / oauth ?



Yes, exactly.

In most enterprise OpenShift environments, OpenShift itself usually does NOT create users manually.

Users are typically managed externally through:

* LDAP
* Active Directory
* IDM
* OAuth
* SSO providers

OpenShift only:

* authenticates users
* maps identities
* applies RBAC permissions

---

# Typical Enterprise Architecture

```text id="yc4vxu"
User
 ↓
Corporate Identity Provider
(LDAP / AD / IDM / OAuth)
 ↓
OpenShift Authentication
 ↓
RoleBinding / Group Mapping
 ↓
Namespace Access
```

---

# Common Identity Providers

## 1. LDAP / Active Directory

Very common in enterprises.

Example:

* Microsoft AD
* Red Hat IDM
* OpenLDAP

User logs in using:

* company username/password

Example:

```text id="7zv64y"
john.doe
```

---

# 2. OAuth / SSO

Very common nowadays.

Examples:

* Okta
* Microsoft Azure AD
* Google OAuth
* GitHub OAuth
* Keycloak

User clicks:

```text id="tx4y7v"
Login with SSO
```

then redirected to identity provider.

---

# 3. Red Hat IDM / FreeIPA

Common in Red Hat/OpenShift ecosystems.

Red Hat Identity Management

Provides:

* centralized authentication
* Kerberos
* LDAP
* policies

---

# What OpenShift Actually Stores

OpenShift generally stores:

* identity mapping
* user references
* tokens

NOT actual passwords.

You can see users:

```bash id="rkl8z9"
oc get users
```

Example:

```text id="vbh42u"
NAME
john
alice
```

But authentication still happens externally.

---

# After Login What Happens?

Suppose:

* user authenticated via LDAP
* OpenShift receives identity
* OpenShift creates user object automatically

Then admin gives permissions:

```bash id="mj0m8e"
oc policy add-role-to-user edit john -n dev-team
```

or usually:

```bash id="kjvsl8"
oc policy add-role-to-group edit developers -n dev-team
```

---

# Most Enterprises Prefer Group-Based Access

Instead of:

```text id="0y58uw"
john
alice
bob
```

they create:

```text id="g7rqso"
dev-team-group
```

in:

* AD
* LDAP
* IDM

Then OpenShift maps group to namespace role.

Much easier to manage.

---

# Real Example

```text id="1i8lxj"
AD Group:
APP_DEV_TEAM
      ↓
Mapped to OpenShift Group
      ↓
RoleBinding:
edit access on dev-team namespace
```

Now:

* any new employee added to AD group
* automatically gets OpenShift access

No manual RBAC needed.

---

# In Your Project Environment

Very likely your company uses one of:

* LDAP
* Azure AD
* Okta
* Red Hat IDM

and OpenShift is configured with OAuth authentication.

You probably login through:

* company SSO page
* corporate credentials

rather than local OpenShift users.

That’s the standard enterprise model.

---

# And edit role will be coming in openshift automatically right ? we won't create that role

Yes, correct.

In OpenShift, roles like:

```text id="0c7o5g"
view
edit
admin
cluster-admin
```

are built-in default roles already provided by OpenShift/Kubernetes.

So normally you do NOT create them manually.

You simply assign them.

Example:

```bash id="s6txcv"
oc policy add-role-to-user edit john -n dev-team
```

Here:

* `edit` role already exists
* OpenShift only creates a RoleBinding

---

# Built-In Roles

You can see them:

```bash id="p62y1x"
oc get clusterrole
```

You’ll find:

```text id="0x29b3"
admin
edit
view
cluster-admin
```

These are called:

```text id="qj2hwo"
ClusterRoles
```

because they exist cluster-wide.

---

# Common Default Roles

| Role            | Purpose                  |
| --------------- | ------------------------ |
| `view`          | Read-only access         |
| `edit`          | Developer-level access   |
| `admin`         | Namespace administration |
| `cluster-admin` | Full cluster control     |

---

# What `edit` Role Usually Allows

A user with `edit` can:

* create deployments
* create services
* create pods
* scale applications
* view logs
* update resources

But typically CANNOT:

* manage RBAC
* create SCCs
* control cluster-wide settings

---

# What Happens Internally

When you run:

```bash id="jlwmg0"
oc policy add-role-to-user edit john -n dev-team
```

OpenShift creates something like:

```yaml id="jlwmg1"
kind: RoleBinding
```

which binds:

```text id="jlwmg2"
User john
        ↓
ClusterRole edit
        ↓
Namespace dev-team
```

The role itself already exists.

---

# When Do We Create Custom Roles?

Only when built-in roles are insufficient.

Example:

* allow pod restart only
* allow configmap access only
* deny secrets
* read-only to specific resources

Then admins create:

* custom Role
* custom ClusterRole

Example:

```yaml id="jlwmg3"
kind: Role
```

or:

```yaml id="jlwmg4"
kind: ClusterRole
```

---

# Real Enterprise Practice

Most organizations mainly use:

* built-in roles
* LDAP/AD groups
* RoleBindings

because:

* easier
* standardized
* secure
* simpler audits

Custom roles are usually created only for special requirements.

---

# Very Important Interview Point

## Difference Between Role and RoleBinding

| Object             | Purpose                             |
| ------------------ | ----------------------------------- |
| Role / ClusterRole | Defines permissions                 |
| RoleBinding        | Assigns permissions to users/groups |

---

# Difference Between Role and ClusterRole

| Role                    | ClusterRole           |
| ----------------------- | --------------------- |
| Namespace-scoped        | Cluster-wide          |
| Exists in one namespace | Exists across cluster |

Built-in roles like:

* edit
* view
* admin

are usually ClusterRoles reused via RoleBindings.


---
