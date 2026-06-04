## What are the different types of variables in Linux?

This is a common Linux interview question, especially for roles involving shell scripting. The interviewer is usually trying to assess your understanding of shell scripts and variable usage.

In Linux shell scripting, variables are broadly classified into three types:

1. **Local Variables**
2. **Environment Variables**
3. **Special Variables**

---

## 1. Local Variables

Local variables are variables whose scope is limited to the current script or shell session.

They are defined within a script and can only be accessed inside that script. If you try to access them outside the script, they will not be available.

### Example

```bash
#!/bin/bash

a=5
b=3

echo "Value of a: $a"
echo "Value of b: $b"
```

In this example:

* `a` and `b` are local variables.
* They exist only within the script.
* Once the script finishes execution, these variables are not accessible outside the script.

---

## 2. Environment Variables

Environment variables are global in nature.

They can be accessed:

* From the current terminal session
* From child processes
* From shell scripts executed from that session

Environment variables are typically created using the `export` command.

### Example

```bash
export NAME="Abhishek"
```

Now the variable can be accessed from:

```bash
echo $NAME
```

or from any shell script launched from the same session.

### Verify Environment Variables

```bash
env
```

or

```bash
printenv
```

These commands display all currently available environment variables.

---

## 3. Special Variables

Linux provides several built-in special variables that store useful information about scripts, arguments, and command execution status.

### `$?` – Exit Status of Previous Command

Displays the exit code of the last executed command.

```bash
ls
echo $?
```

Output:

```bash
0
```

A value of:

* `0` = Success
* Non-zero = Failure

Example:

```bash
ls /invalid_directory
echo $?
```

Output:

```bash
2
```

indicating the command failed.

---

### `$0` – Script Name

Returns the name of the currently executing script.

```bash
#!/bin/bash

echo $0
```

Output:

```bash
./myscript.sh
```

---

### `$1`, `$2`, `$3` ... – Command Line Arguments

Used to access arguments passed to a script.

### Example

```bash
#!/bin/bash

echo "First Argument: $1"
echo "Second Argument: $2"
```

Execution:

```bash
./script.sh Hello Linux
```

Output:

```bash
First Argument: Hello
Second Argument: Linux
```

---

### Other Common Special Variables

| Variable        | Description                      |
| --------------- | -------------------------------- |
| `$0`            | Script name                      |
| `$1`, `$2`, ... | Positional arguments             |
| `$#`            | Number of arguments passed       |
| `$*`            | All arguments as a single string |
| `$@`            | All arguments individually       |
| `$$`            | Current process ID (PID)         |
| `$?`            | Exit status of last command      |

---

## Interview Answer (Short Version)

In Linux shell scripting, there are three main types of variables:

1. **Local Variables** – Defined inside a script and accessible only within that script.
2. **Environment Variables** – Global variables created using `export` and accessible by child processes and scripts.
3. **Special Variables** – Built-in variables such as `$?`, `$0`, `$1`, `$#`, and `$$` that provide information about command execution, script names, arguments, and process IDs.

For example:

```bash
a=5                  # Local Variable
export NAME=John     # Environment Variable
echo $?              # Special Variable
```

This demonstrates the three different types of variables commonly used in Linux shell scripting.
