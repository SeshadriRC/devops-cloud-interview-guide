# Merge vs Rebase Practical

# Git Merge vs Git Rebase – Practical Demonstration

For this demonstration, I created a sample repository with five commits on the **main** branch.

You can create a similar repository and practice both **Git Merge** and **Git Rebase** yourself.

At this point:

* Only the **main** branch exists.
* It contains five commits.

---

## Step 1: Create a Feature Branch

Create a new branch called **multiplication** from the main branch.

```bash
git checkout -b multiplication
```

Now the multiplication branch contains all commits from the main branch.

---

## Step 2: Add a Commit to the Feature Branch

Let's simulate active development on the multiplication branch.

Create a file called `multiply.sh`:

```bash
#!/bin/bash
echo "Multiplication"
```

Commit the change:

```bash
git add .
git commit -m "Add multiplication feature"
```

Now the multiplication branch has **one additional commit**.

---

## Step 3: Add a Commit to Main Branch

Switch back to the main branch:

```bash
git checkout main
```

Create another file:

```bash
echo "Adding unit tests"
```

Save it as `test.sh`.

Commit the change:

```bash
git add .
git commit -m "Create tests"
```

Now:

### Multiplication Branch

```text
Commit A -> Add multiplication feature
```

### Main Branch

```text
Commit B -> Create tests
```

The order of these commits is important because it helps us understand the difference between Merge and Rebase.

---

# Preparing for the Demonstration

To show both approaches separately, create another branch from multiplication:

```bash
git checkout multiplication
git checkout -b multiplication_rebase
```

Now we have:

```text
main
multiplication
multiplication_rebase
```

The last two branches are identical.

---

# Demonstrating Git Rebase

Checkout the rebase branch:

```bash
git checkout multiplication_rebase
```

Run:

```bash
git rebase origin/main
```

Output:

```text
Successfully rebased
```

---

## What Happens After Rebase?

Git takes:

1. All commits from the main branch
2. Then reapplies the multiplication branch commits on top

If you run:

```bash
git log
```

The history looks like:

```text
Main Commit 1
Main Commit 2
Main Commit 3
...
Create tests

Add multiplication feature
```

Notice:

* All main branch commits appear first.
* The multiplication commit appears at the top.

This is called a **Linear Commit History**.

---

# Demonstrating Git Merge

Now checkout the original multiplication branch:

```bash
git checkout multiplication
```

Run:

```bash
git merge origin/main
```

Git creates a merge commit:

```text
Merge branch 'main' into multiplication
```

Now execute:

```bash
git log
```

You will see:

```text
Merge Commit
Create tests
Add multiplication feature
Previous commits...
```

The commits are preserved according to when they were actually created.

This is called **Chronological History**.

---

# Comparing the Histories

## Git Rebase

```text
Initial Commit
...
Main Branch Commits
...
Create tests
Add multiplication feature
```

Characteristics:

* Linear history
* Main branch commits first
* Feature branch commits replayed later
* No merge commit

---

## Git Merge

```text
Initial Commit
...
Add multiplication feature
Create tests
Merge Commit
```

Characteristics:

* Original history preserved
* Commit order follows actual timeline
* Merge commit created

---

# Why Does This Difference Matter?

For small projects or personal repositories:

✅ Git Rebase is often preferred because the history is clean and easy to read.

For large projects with hundreds of developers:

✅ Git Merge is often preferred because it preserves the exact history.

Benefits of preserving history:

* Easier troubleshooting
* Easier root-cause analysis
* Better visibility into when changes happened
* Simplifies conflict investigation
* Helps identify which commit introduced an issue

---

# Interview Answer

**Git Merge** and **Git Rebase** are both used to integrate changes from one branch into another.

### Git Merge

* Preserves original commit history
* Creates a merge commit
* Maintains chronological order of changes

### Git Rebase

* Replays feature branch commits on top of the latest target branch
* Produces a clean, linear history
* Rewrites commit history

### One-Line Answer

> Git Merge preserves branch history by creating a merge commit, whereas Git Rebase rewrites history by replaying commits on top of the target branch, resulting in a cleaner linear commit history.
