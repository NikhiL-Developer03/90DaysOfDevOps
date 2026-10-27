# Day 16 – Shell Scripting Basics

## Objective

Learn the fundamentals of Bash shell scripting, including the shebang, variables, user input, and basic `if-else` conditions.

---

## Task 1 – My First Shell Script

### Create `hello.sh`

```bash
#!/bin/bash

echo "Hello, DevOps!"
```

### Make the script executable and run it

```bash
chmod +x hello.sh
./hello.sh
```

### Example output

```text
Hello, DevOps!
```

### What happens if the shebang is removed?

The shebang (`#!/bin/bash`) tells the system which interpreter should run the script when you execute it directly with `./hello.sh`. Without it, direct execution may fail with an execution-format error or behave differently depending on the shell and environment. Running the file explicitly with `bash hello.sh` uses Bash regardless of whether the shebang is present.

---

## Task 2 – Variables

### Create `variables.sh`

```bash
#!/bin/bash

NAME="Nikhil"
ROLE="DevOps Engineer"

echo "Hello, I am $NAME and I am a $ROLE"
```

### Make executable and run

```bash
chmod +x variables.sh
./variables.sh
```

### Example output

```text
Hello, I am Nikhil and I am a DevOps Engineer
```

### Single quotes vs double quotes

**Double quotes** allow variable expansion:

```bash
NAME="Nikhil"
echo "Hello $NAME"
```

Output:

```text
Hello Nikhil
```

**Single quotes** preserve the text literally:

```bash
echo 'Hello $NAME'
```

Output:

```text
Hello $NAME
```

### What I learned

- Variables store values that can be reused.
- Do not put spaces around `=` when assigning a variable, for example `NAME="Nikhil"`.
- Use `$NAME` to read the value stored in `NAME`.

---

## Task 3 – User Input with `read`

### Create `greet.sh`

```bash
#!/bin/bash

read -p "Enter your name: " NAME
read -p "Enter your favourite tool: " TOOL

echo "Hello $NAME, your favourite tool is $TOOL"
```

### Make executable and run

```bash
chmod +x greet.sh
./greet.sh
```

### Example output

```text
Enter your name: Nikhil
Enter your favourite tool: Docker
Hello Nikhil, your favourite tool is Docker
```

### What I learned

The `read` command accepts input from the user and stores it in a variable. The `-p` option displays a prompt before waiting for input.

---

## Task 4 – If-Else Conditions

### 4.1 Check whether a number is positive, negative, or zero

Create `check_number.sh`:

```bash
#!/bin/bash

read -p "Enter a number: " NUMBER

if [ "$NUMBER" -gt 0 ]; then
    echo "The number is positive"
elif [ "$NUMBER" -lt 0 ]; then
    echo "The number is negative"
else
    echo "The number is zero"
fi
```

### Make executable and run

```bash
chmod +x check_number.sh
./check_number.sh
```

### Example outputs

Input `10`:

```text
Enter a number: 10
The number is positive
```

Input `-5`:

```text
Enter a number: -5
The number is negative
```

Input `0`:

```text
Enter a number: 0
The number is zero
```

> This example expects an integer. Non-numeric input needs validation before using `-gt` or `-lt`.

### What I learned

- `if` checks the first condition.
- `elif` checks another condition if the earlier one is false.
- `else` runs when none of the earlier conditions matched.
- `fi` closes the conditional block.

### 4.2 Check whether a file exists

Create `file_check.sh`:

```bash
#!/bin/bash

read -p "Enter a filename: " FILE

if [ -f "$FILE" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

### Make executable and run

```bash
chmod +x file_check.sh
./file_check.sh
```

### Example output

```text
Enter a filename: notes.txt
File exists
```

If the file is missing, the script prints:

```text
Enter a filename: test.txt
File does not exist
```

The `-f` test returns true when the given path exists and is a regular file. Use quotes around `"$FILE"` so filenames containing spaces are handled properly.

---

## Task 5 – Combine It All: Check a Service

Create `server_check.sh`:

```bash
#!/bin/bash

SERVICE="nginx"

read -p "Do you want to check the status? (y/n): " CHOICE

if [ "$CHOICE" = "y" ]; then
    if systemctl is-active --quiet "$SERVICE"; then
        echo "$SERVICE is active"
    else
        echo "$SERVICE is not active"
    fi
elif [ "$CHOICE" = "n" ]; then
    echo "Skipped."
else
    echo "Invalid choice. Please enter y or n."
fi
```

### Make executable and run

```bash
chmod +x server_check.sh
./server_check.sh
```

### Example output – service active

```text
Do you want to check the status? (y/n): y
nginx is active
```

### Example output – skip

```text
Do you want to check the status? (y/n): n
Skipped.
```

The service name is stored in `SERVICE`. The command `systemctl is-active --quiet "$SERVICE"` checks the service state; its exit status determines which branch runs.

> If Nginx is not installed, or your environment does not use systemd (some WSL setups do not), the check may not work as shown. You can change `SERVICE="nginx"` to a service available on your Linux system. Check only services on systems you are authorized to administer.

---

## Scripts for Day 16

```text
hello.sh
variables.sh
greet.sh
check_number.sh
file_check.sh
server_check.sh
```

## Commands used

```bash
chmod +x hello.sh
./hello.sh

chmod +x variables.sh
./variables.sh

chmod +x greet.sh
./greet.sh

chmod +x check_number.sh
./check_number.sh

chmod +x file_check.sh
./file_check.sh

chmod +x server_check.sh
./server_check.sh
```

## What I learned – 3 key points

1. **Shebang:** `#!/bin/bash` specifies Bash as the intended interpreter for direct script execution.
2. **Variables and input:** Variables store reusable values, while `read` accepts input from the user.
3. **Conditional logic:** `if`, `elif`, and `else` let a script choose actions based on conditions.

## Key takeaway

Shell scripting helps turn repeated Linux commands into reusable tasks. The basic flow practiced today is:

```text
Input
  ↓
Variables
  ↓
Condition
  ↓
Action
  ↓
Output
```

## Submission checklist

- [ ] Create all six `.sh` files.
- [ ] Run each script and check the output.
- [ ] Replace example outputs in these notes with actual results where needed.
- [ ] Save this file as `day-16-shell-scripting.md`.
- [ ] Add the files to `2026/day-16/` in your repository.
- [ ] Commit and push your changes.
