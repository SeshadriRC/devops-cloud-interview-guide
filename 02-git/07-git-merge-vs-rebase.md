## Question  
What is the difference between `git rebase` and `git merge`? When would you use each?

### 📝 Short Explanation  
This question evaluates your understanding of how Git manages branch history and collaboration. It’s a common topic in interviews because both commands integrate changes from one branch to another — but they do it in very different ways.

## ✅ Answer  
- `git merge` integrates changes by creating a new merge commit, preserving the history of both branches.
- `git rebase` moves your branch on top of another, rewriting commit history to create a linear sequence.

### 📘 Detailed Explanation  
Let’s say you have two branches:
- `main`
- `feature` (branched off earlier from `main`)

#### 👉 Using `git merge`:
```bash
git checkout feature
git merge main
```

This pulls changes from `main` into `feature` and creates a **merge commit**, like this:
```
A---B---C (main)
     \
      D---E---F (feature)
             \
              G (merge commit)
```

**Pros:**
- Preserves full history and context
- Safer in teams: no history rewriting
- Good for long-lived shared branches

**Cons:**
- History becomes messy with many merge commits
- Harder to trace linear commit flow

---

#### 👉 Using `git rebase`:
```bash
git checkout feature
git rebase main
```

This **re-applies your commits on top of the latest `main`**, like this:
```
A---B---C (main)
             \
              D'---E'---F' (rebased feature)
```

**Pros:**
- Clean, linear history
- Easier to `git log` and `git bisect`
- Preferred before merging short-lived branches into main

**Cons:**
- Rewrites commit history
- Risky if already pushed and others have based work on it
- Not ideal for shared/public branches

---

### 🧠 When to Use What

| Use `merge` when...            | Use `rebase` when...                  |
|-------------------------------|---------------------------------------|
| You're collaborating on shared branches | You're working alone or before a PR merge |
| You want to preserve commit context     | You want a clean, linear history          |
| History safety is a concern             | You're cleaning up before pushing         |

> Summary:  
> Use `merge` to combine, use `rebase` to simplify.

---

![Alt text](./images/git-merge-vs-rebase.png)

---

# Summary

# What is the Difference Between Git Merge and Git Rebase?

This is one of the most frequently asked Git interview questions, yet many people struggle to explain it clearly.

The reason is simple: most explanations focus on theory and diagrams, but not on practical usage.

To understand it properly, let's take a real-world example.

---

## Scenario

Assume there is a project called **Calculator Application**.

The development team is actively working on the **main** branch and has already made 100 commits.

On **January 1st**, management decides to introduce a new feature called **Complex Multiplication**.

Since this feature may or may not be released to customers, the team creates a separate feature branch called **multiplication** from the 100th commit of the main branch.

At the time of branch creation:

* Main branch = 100 commits
* Multiplication branch = same 100 commits

---

## Development Progress

### Multiplication Branch

Two developers work on the multiplication feature for three months and add:

* 70 new commits

### Main Branch

Meanwhile, other developers continue working on:

* New features
* Bug fixes
* Security patches
* Improvements

As a result, the main branch receives:

* 150 additional commits

By **March 31st**:

* Main branch = 250 commits (100 + 150)
* Multiplication branch = 170 commits (100 + 70)

---

## The Problem

The multiplication team now wants to create a Pull Request (PR) to merge their feature into the main branch.

However, Git reports:

> Main branch is 150 commits ahead of the multiplication branch.

Before merging, the feature team must first integrate those 150 commits into their branch and verify that their feature still works correctly.

To achieve this integration, Git provides two strategies:

1. **Git Merge**
2. **Git Rebase**

Both solve the same problem:

> Integrating changes from one branch into another.

The difference lies in **how they maintain commit history**.

---

# Git Rebase

### Command

```bash
git checkout multiplication
git rebase origin/main
```

### What Happens?

Git takes all commits from the main branch and places them first.

Then it reapplies the multiplication branch commits on top of them.

Resulting history:

```text
Main Branch:
1 --- 2 --- 3 --- ... --- 250

Feature Commits:
251 --- 252 --- ... --- 320
```

When you run:

```bash
git log
```

You will see:

1. All commits from the main branch
2. Followed by all commits from the multiplication branch

This creates a **linear commit history**.

---

## Characteristics of Rebase

### Advantages

* Clean and linear history
* Easier to read Git logs
* Better for maintaining a tidy project history
* Preferred before raising a Pull Request

### Disadvantages

* Rewrites commit history
* Can be risky if the branch is already shared with other developers
* May require resolving conflicts multiple times

---

# Git Merge

### Command

```bash
git checkout multiplication
git merge origin/main
```

### What Happens?

Git combines both branches while preserving their original history.

Instead of rearranging commits, Git creates a new **merge commit**.

Result:

```text
          Feature Commit A
         /
1 --- 2 --- 3 --- 4 --- 5
         \
          Main Commit B

                |
                v

           Merge Commit
```

Commit history remains exactly as it happened in reality.

---

## Characteristics of Merge

### Advantages

* Preserves complete history
* Does not rewrite commits
* Safer for shared branches

### Disadvantages

* History becomes more complex
* Multiple merge commits can clutter the log
* Harder to follow long-running projects

---

# Quick Comparison

| Feature                            | Git Merge  | Git Rebase    |
| ---------------------------------- | ---------- | ------------- |
| History Type                       | Non-linear | Linear        |
| Creates Merge Commit               | Yes        | No            |
| Rewrites History                   | No         | Yes           |
| Safe for Shared Branches           | Yes        | Use Carefully |
| Clean Git Log                      | No         | Yes           |
| Easier Debugging of Actual History | Yes        | No            |

---

# Interview Answer (Short Version)

**Git Merge** and **Git Rebase** are both used to integrate changes from one branch into another.

* **Git Merge** preserves the original branch history and creates a merge commit.
* **Git Rebase** rewrites the branch history by replaying feature branch commits on top of the latest target branch, creating a clean linear history.

In practice:

* Use **Merge** when working on shared branches where history should be preserved.
* Use **Rebase** when you want a clean, linear commit history before creating a Pull Request.

**One-line interview answer:**

> Git Merge preserves branch history by creating a merge commit, whereas Git Rebase creates a linear history by replaying commits on top of the latest branch state. Both integrate branches, but they manage commit history differently.


---
