# Git Version Control Interview Preparation Guide

This guide covers Git internals, branch management, merge vs. rebase strategies, and essential commands tested in DevOps interviews.

---

## 📑 Core Topics & Study Files

* **Q&A Document**: [Git Interview Q&A](Interview-Q&A.md)
  - What are Git refs (`HEAD`, `refs/heads/`, `refs/remotes/`, `refs/tags/`)?
  - Searching commit history with `git log --grep="keyword"`.

---

## 💡 Frequently Asked Git Interview Questions

### 1. `git pull` vs. `git rebase`
* **`git pull`** (default `git fetch + git merge`):
  - Fetches changes from remote and merges them into the current local branch.
  - Creates a **merge commit** preserving complete non-linear branch history.
* **`git pull --rebase`** (or `git rebase`):
  - Moves your local unpushed commits on top of the fetched upstream branch commits.
  - Rewrites commit SHAs to produce a clean, linear project history.
* **When to avoid rebase**: Never rebase public branches (like `main` or `develop`) that other team members are actively branching off from.

### 2. `git merge` vs. `git squash`
* **Standard Merge**: Keeps all individual commits from the feature branch.
* **Squash Merge**: Combines all commits from a feature branch into a single clean commit on the target branch. Keeps the `main` branch history tidy and easy to revert.

### 3. Recovering Lost Commits with `git reflog`
* `git reflog` tracks every change to `HEAD`, including discarded commits, hard resets (`git reset --hard`), and deleted branches.
* To recover: Run `git reflog`, identify the commit SHA prior to the mistake, and run `git checkout -b recovery-branch <commit-SHA>`.

### 4. Git Branching Strategies: GitHub Flow vs. GitFlow
* **GitHub Flow**: Simple and continuous deployment friendly. Branch off `main` → make PR → code review / CI test → merge to `main` → deploy to production.
* **GitFlow**: Structured release cadence. Uses persistent branches: `main` (production), `develop` (integration), plus short-lived branches (`feature/`, `release/`, `hotfix/`). Best for traditional versioned releases.
