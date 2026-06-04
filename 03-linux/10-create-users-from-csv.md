[refer](https://github.com/SeshadriRC/devops-cloud-interview-guide/blob/main/03-linux/create_users.sh)

## Question  
You’ve received a CSV file with a list of usernames and passwords to create users on a Linux system.  

**Task:**  
Write a shell script to read the CSV and:
- Create each user with the specified password.
- Force password change on first login.

```csv
username,password
alice,Password@123
bob,Secure@456
carol,DevOps@789
```

### 📝 Short Explanation  
This task tests your ability to read structured data (CSV) and automate user management using shell scripting — commonly required for onboarding automation in DevOps roles.

## ✅ Answer


```bash
#!/bin/bash

INPUT="users.csv"

# Check if the file exists
if [[ ! -f "$INPUT" ]]; then
  echo "CSV file not found!"
  exit 1
fi

# Skip header and read each line
tail -n +2 "$INPUT" | while IFS=',' read -r username password; do
  # Check if user already exists
  if id "$username" &>/dev/null; then
    echo "User '$username' already exists. Skipping..."
    continue
  fi

  # Create the user
  useradd "$username"

  # Set the password
  echo "${username}:${password}" | chpasswd

  # Force password change on first login
  chage -d 0 "$username"

  echo "User '$username' created successfully."
done

```

---

### ✅ Script Usage:
Make it executable and run with root privileges:
```bash
chmod +x create_users.sh
sudo ./create_users.sh
```

---

### 🧠 How It Works:

- `IFS=',' read -r ...` parses each line into `username` and `password`.
- `useradd` creates the user.
- `chpasswd` sets the password using `echo "$username:$password"`.
- `chage -d 0` forces the user to reset their password on the first login.

> Summary:  
> This script reads a CSV file, creates users with the specified passwords, and ensures they are prompted to change their password at first login — a great example of secure onboarding automation.

---
