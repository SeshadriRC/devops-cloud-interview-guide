No problem! This is a very common Ansible interview question.

## Step 1: Create an encrypted file

Create a file to store secrets:

```bash
ansible-vault create secrets.yml
```

It will ask:

```text
New Vault password:
Confirm New Vault password:
```

Enter a password (for example, `MyVault@123`).

An editor opens. Add your secrets:

```yaml
db_user: admin
db_password: Admin@123
```

Save and exit.

---

## Step 2: Use the encrypted file in your playbook

```yaml
---
- hosts: webservers

  vars_files:
    - secrets.yml

  tasks:
    - name: Display username
      debug:
        msg: "{{ db_user }}"

    - name: Display password
      debug:
        msg: "{{ db_password }}"
```

Although the file is encrypted, Ansible decrypts it during execution.

---

## Step 3: Run the playbook

### Option 1: Prompt for the vault password

```bash
ansible-playbook playbook.yml --ask-vault-pass
```

It asks:

```text
Vault password:
```

Enter the password you used when creating `secrets.yml`.

---

### Option 2: Use a password file (recommended for automation)

Create a file:

```bash
echo "MyVault@123" > vault.pass
```

Restrict its permissions:

```bash
chmod 600 vault.pass
```

Run:

```bash
ansible-playbook playbook.yml --vault-password-file vault.pass
```

This is commonly used in Jenkins or other CI/CD tools.

---

# Encrypt only a single variable

Instead of encrypting an entire file:

```bash
ansible-vault encrypt_string 'Admin@123' --name 'db_password'
```

Output:

```yaml
db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          613561663138...
```

Use it in a variables file:

```yaml
db_user: admin

db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          613561663138...
```

---

# Edit an encrypted file

```bash
ansible-vault edit secrets.yml
```

---

# View an encrypted file

```bash
ansible-vault view secrets.yml
```

---

# Change the vault password

```bash
ansible-vault rekey secrets.yml
```

---

# Encrypt an existing file

```bash
ansible-vault encrypt secrets.yml
```

---

# Decrypt a file

```bash
ansible-vault decrypt secrets.yml
```

---

## Real-world Jenkins Example

```groovy
stage('Deploy') {
    steps {
        sh '''
        ansible-playbook deploy.yml \
          --inventory inventory.ini \
          --vault-password-file vault.pass
        '''
    }
}
```

In production, the `vault.pass` file is typically provided securely by Jenkins Credentials rather than stored in source control.

---

## Interview Answer (30–45 seconds)

> "I use Ansible Vault to encrypt sensitive data such as passwords, API keys, or tokens. First, I create an encrypted file using `ansible-vault create secrets.yml` and store variables like `db_password`. In the playbook, I include that file using `vars_files`. During execution, I provide the vault password using either `--ask-vault-pass` or `--vault-password-file`, which is commonly used in CI/CD pipelines. This ensures sensitive information isn't stored in plain text in the repository."
