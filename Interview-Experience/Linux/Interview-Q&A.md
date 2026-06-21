1. what is readdir()

- readdir() reads entries from an opened directory and returns details like inode number, filename, record length, and file type.

---

2. Difference between `sudo su - ansible` and `su - ansible`

Good question. These two commands look similar but behave differently.

## 1. `sudo su - ansible`

```bash
sudo su - ansible
```

Meaning:

* `sudo` → execute command as **root**
* `su - ansible` → root switches to **ansible user**
* `-` → load ansible user's login environment

Flow:

```text
Current User → root → ansible
```

Example:

```text
ubuntu
 ↓ sudo
root
 ↓ su -
ansible
```

This requires **sudo permission**.

Check:

```bash
whoami
```

Output:

```text
ansible
```

Check original user:

```bash
echo $SUDO_USER
```

Output:

```text
ubuntu
```

## 2. `su - ansible`

```bash
su - ansible
```

Meaning:

* Directly switch to `ansible`
* Requires **ansible user's password**
* No sudo involved

Flow:

```text
Current User → ansible
```

Example:

```text
ubuntu
 ↓ su -
ansible
```

System asks:

```text
Password:
```

You must know `ansible` user's password.


## Real-world server usage

Usually on cloud servers:

```bash
sudo su - ansible
```

is more common because:

* Users login using SSH keys
* Service accounts (ansible, oracle, postgres) often don't have passwords

Example:

```bash
ssh ubuntu@server
sudo su - oracle
```


### Quick comparison

| Command             | Uses sudo? | Requires target user password? | Intermediate user |
| ------------------- | ---------- | ------------------------------ | ----------------- |
| `sudo su - ansible` | Yes        | No                             | root              |
| `su - ansible`      | No         | Yes                            | none              |

For AWS EC2, `sudo su - ansible` is usually what you use.


---
