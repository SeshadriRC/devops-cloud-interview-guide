1. What is ref in git?

A pointer to a commit (SHA) in Git. Instead of remembering long commit IDs, Git uses refs with readable names.

**Common Git refs**

| Ref                        | Meaning                                     |
| -------------------------- | ------------------------------------------- |
| `HEAD`                     | Points to current checked-out branch/commit |
| `refs/heads/main`          | Local branch                                |
| `refs/remotes/origin/main` | Remote branch                               |
| `refs/tags/v1.0`           | Tag reference                               |

---

2. What is `git log --grep=keyword`

Search commit history and show commits whose commit message contains a specific keyword.

---
