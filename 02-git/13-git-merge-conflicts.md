## Question  
How do you handle merge conflicts in Git?

### 📝 Short Explanation  
Merge conflicts are a common part of collaborative Git workflows. This question is meant to test how calmly and effectively you resolve conflicts and whether you understand why they occur.

## ✅ Answer  
When I encounter a merge conflict, I pause to understand which files are affected, examine the conflicting changes, and manually resolve them using a visual diff tool or editor. Once resolved, I mark the conflicts as resolved, stage the changes, and complete the merge or rebase.

### 📘 Detailed Explanation  
Merge conflicts usually happen when:
- Two branches modify the same lines in a file
- One branch deletes a file the other modifies
- A rebase applies commits that overlap with existing changes

Here’s how I handle them:

#### 🔍 Step 1: Identify the conflict
Git clearly indicates the files with conflicts during a `git merge` or `git rebase`:
```bash
Auto-merging app.py  
CONFLICT (content): Merge conflict in app.py  
```

#### 🛠️ Step 2: Open and resolve the conflict
Conflicted files contain markers like:
```diff
<<<<<<< HEAD
print("Hello from main")
=======
print("Hello from feature-branch")
>>>>>>> feature-branch
```

I manually edit the file to reflect the correct final code, based on the intended logic. Sometimes I use tools like:
- `git diff` to understand the changes
- Visual Studio Code or GitKraken for visual resolution

#### ✅ Step 3: Mark as resolved
Once edited:
```bash
git add app.py
```

Then complete the merge:
```bash
git commit         # if using merge
# or
git rebase --continue  # if using rebase
```

> Merge conflicts aren’t errors — they’re just Git asking you to make a decision. Handling them well is part of being a collaborative engineer.

---

## Summarize

# How Do You Handle Merge Conflicts?

Merge conflicts are very common when working with Git, and interviewers frequently ask:

> **"How do you handle merge conflicts?"**

Before answering, it is important to understand what a merge conflict is.

---

# What Is a Merge Conflict?

Consider a repository called **Calculator Application**.

Suppose a developer creates a feature branch from the main branch:

```bash
git checkout -b feature
```

Now there are two branches:

```text
main
 └── feature
```

---

## Scenario

### Developer A (Feature Branch)

Developer A modifies `calculator.sh`.

Original code:

```bash
read number2
```

Updated code:

```bash
read number3
```

Developer A commits the change.

---

### Developer B (Main Branch)

At the same time, another developer working on the main branch modifies the same line.

Original code:

```bash
read number2
```

Updated code:

```bash
read number4
```

Developer B also commits the change.

---

## Conflict Occurs

Now the feature branch is ready and needs to be merged into the main branch.

When Git attempts the merge:

```bash
git merge feature
```

Git sees:

```text
Feature Branch : read number3
Main Branch    : read number4
```

Both branches modified the same line differently.

Git cannot determine which version is correct.

Result:

```text
Merge Conflict
```

or

```text
Cannot automatically merge
```

---

# How Do I Handle Merge Conflicts as a DevOps Engineer?

The answer depends on whether I own the code or not.

---

# Scenario 1: Application Code Conflict

Suppose the conflict occurs in:

* Java Spring Boot code
* Golang application code
* Node.js application code
* Any application source code

As a DevOps engineer, I am not the owner of the application code.

### My Approach

#### 1. Identify the Conflicting Developers

I review:

* Pull Request
* Commit history
* Git blame (if required)

to determine who modified the conflicting code.

```bash
git log
git blame file_name
```

---

#### 2. Communicate with Developers

I inform the developers:

> "A merge conflict occurred while integrating the feature branch into the main branch."

For example:

```text
Developer A → Feature Branch
Developer B → Main Branch
```

Since they understand the business logic and source code, they are the right people to decide which change should be retained.

---

#### 3. Facilitate Resolution

If required:

* Arrange a discussion.
* Create a quick meeting.
* Help both developers review the conflicting changes.

The goal is to determine:

```text
Which code is correct?
```


#### 4. Ask Developers to Resolve the Conflict

One developer updates the code and commits the resolution.

Example:

```bash
git add .
git commit -m "Resolve merge conflict"
```

#### 5. Validate the Changes

Before completing the merge:

* Run CI/CD pipeline.
* Execute automated tests.
* Perform code validation.


#### 6. Complete the Merge

Once testing succeeds:

```bash
git merge feature
```

or approve the Pull Request.


# Scenario 2: DevOps Code Conflict

Suppose the conflict is in code owned by the DevOps team, such as:

* Terraform
* Kubernetes manifests
* Helm charts
* Ansible playbooks
* Shell scripts
* Python automation scripts

In this case, I am the owner of the code.


## My Approach

### 1. Review the Conflict

Example:

```terraform
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Two DevOps engineers may have modified the same block differently.


### 2. Collaborate with the Other Engineer

We review:

* Why each change was made
* Business requirements
* Infrastructure requirements

### 3. Select the Correct Implementation

We agree on:

* One version
* Or a combination of both versions


### 4. Resolve and Commit

```bash
git add .
git commit -m "Resolve Terraform merge conflict"
```


### 5. Validate

Run:

* Terraform validation
* Terraform plan
* Helm lint
* Kubernetes manifest validation
* Automated pipeline checks

depending on the technology involved.


# Typical Conflict Resolution Workflow

```text
Merge Conflict Detected
          │
          ▼
Identify Affected Developers
          │
          ▼
Communicate and Discuss
          │
          ▼
Resolve Conflict
          │
          ▼
Commit Resolution
          │
          ▼
Run CI/CD Validation
          │
          ▼
Complete Merge
```


# Interview Answer (Short Version)

> When a merge conflict occurs, I first identify the developers who modified the conflicting code and communicate the issue to them. If the conflict is in application code, I facilitate discussions between the developers because they understand the business logic better than I do. Once the conflict is resolved, I ensure the changes are validated through CI/CD pipelines and testing before completing the merge. If the conflict is in DevOps-owned code such as Terraform, Kubernetes manifests, Helm charts, or automation scripts, I collaborate directly with the relevant team members, resolve the conflict, validate the changes, and then proceed with the merge.

This answer demonstrates both **technical understanding** and **cross-team collaboration**, which interviewers typically look for in DevOps engineers.


---
