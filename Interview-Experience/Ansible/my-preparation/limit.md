If your inventory looks like this:

```ini
[webservers]
host1
host2
host3

[dbservers]
host4
host5
```

and you want to run a playbook **only on the `webservers` group and `host3`**, there are a few possibilities depending on what you mean.

### Option 1: Run only on `host3`

If `host3` is part of the `webservers` group:

```bash
ansible-playbook -i inventory.ini playbook.yml --limit host3
```

This executes only on `host3`.

---

### Option 2: Limit to the `webservers` group

Run on all hosts in the `webservers` group:

```bash
ansible-playbook -i inventory.ini playbook.yml --limit webservers
```

Runs on:

* host1
* host2
* host3

---

### Option 3: Limit to both a group and a host

If you specifically want to express **host3 within the webservers group**, use an intersection:

```bash
ansible-playbook -i inventory.ini playbook.yml --limit "webservers:&host3"
```

The `:&` operator means **intersection**. Since `host3` belongs to `webservers`, only `host3` will be targeted.

---

### Other useful `--limit` patterns

| Requirement                     | Command                       |
| ------------------------------- | ----------------------------- |
| Only `host3`                    | `--limit host3`               |
| Only `webservers`               | `--limit webservers`          |
| `host1` and `host3`             | `--limit "host1,host3"`       |
| All `webservers` except `host2` | `--limit "webservers:!host2"` |
| Intersection of two groups      | `--limit "webservers:&prod"`  |

### Interview Tip

A common interview question is: **"How do you run a playbook only on one host without modifying the playbook?"**

The expected answer is:

```bash
ansible-playbook playbook.yml --limit host3
```

using the `--limit` option to restrict execution to the desired host.


If you want to **run the playbook on all `webservers` except `host3`**, use:

```bash
ansible-playbook -i inventory.ini playbook.yml --limit "webservers:!host3"
```

or (newer syntax, also supported):

```bash
ansible-playbook -i inventory.ini playbook.yml --limit "webservers,!host3"
```

### Example

**Inventory:**

```ini
[webservers]
host1
host2
host3
```

**Command:**

```bash
ansible-playbook -i inventory.ini playbook.yml --limit "webservers:!host3"
```

**Execution:**

* ✅ host1
* ✅ host2
* ❌ host3 (excluded)

### Interview Tip

The `!` operator is used to **exclude** hosts or groups.

Some useful examples:

```bash
# Run only on host3
--limit host3

# Run on all webservers
--limit webservers

# Run on webservers except host3
--limit "webservers:!host3"

# Run on host1 and host3 only
--limit "host1,host3"

# Run on webservers that are also in the prod group
--limit "webservers:&prod"
```

This is a frequently asked Ansible interview topic, especially for production maintenance where you need to exclude one or more hosts during deployments.
