# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## Day 17 Goals

In this practice, I learned how to:
- Use `for` loops to repeat commands.
- Use a `while` loop for a countdown.
- Pass command-line arguments to a Bash script.
- Handle missing arguments.
- Check package installation status in Ubuntu.
- Use `set -e` and `||` for basic error handling.
- Check Bash syntax with `bash -n`.

> **Environment:** Ubuntu 24.04 running in WSL  
> **Working directory:** `2026/day-17/`

---

## Task 1 – `for` Loops

### 1.1 Print a list of fruits

**File:** `for_loop.sh`

```bash
#!/bin/bash

fruits=("Apple" "Banana" "Mango" "Orange" "Grapes")

for fruit in "${fruits[@]}"
do
    echo "$fruit"
done
```

Run:

```bash
chmod +x for_loop.sh
./for_loop.sh
```

Output:

```text
Apple
Banana
Mango
Orange
Grapes
```

**Explanation:**
- `fruits=(...)` creates a Bash array.
- `for fruit in "${fruits[@]}"` processes each item in the array.
- `echo "$fruit"` prints the current fruit.
- `done` marks the end of the loop.

### 1.2 Print numbers from 1 to 10

**File:** `count.sh`

```bash
#!/bin/bash

for i in {1..10}
do
    echo "$i"
done
```

Run:

```bash
chmod +x count.sh
./count.sh
```

Output:

```text
1
2
3
4
5
6
7
8
9
10
```

`{1..10}` expands to the numbers from 1 through 10 in Bash. Each number is assigned to `i` and printed.

---

## Task 2 – `while` Loop Countdown

**File:** `countdown.sh`

```bash
#!/bin/bash

read -p "Enter a number: " NUMBER

while [ "$NUMBER" -ge 0 ]
do
    echo "$NUMBER"
    NUMBER=$((NUMBER - 1))
done

echo "Done!"
```

Run:

```bash
chmod +x countdown.sh
./countdown.sh
```

Example input: `5`

Output:

```text
Enter a number: 5
5
4
3
2
1
0
Done!
```

**Explanation:**
- `read -p` displays a prompt and stores the input in `NUMBER`.
- `while [ "$NUMBER" -ge 0 ]` repeats while the number is greater than or equal to zero.
- `NUMBER=$((NUMBER - 1))` subtracts one each time.
- When the number becomes `-1`, the condition is false and the loop stops.
- `echo "Done!"` runs after the loop.

**Note:** This beginner version expects a valid integer and does not validate non-numeric input.

---

## Task 3 – Command-Line Arguments

### 3.1 Greet a user

**File:** `greet.sh`

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: ./greet.sh <name>"
    exit 1
fi

echo "Hello, $1!"
```

Run with a name:

```bash
chmod +x greet.sh
./greet.sh Nick
```

Output:

```text
Hello, Nick!
```

Run without a name:

```bash
./greet.sh
```

Output:

```text
Usage: ./greet.sh <name>
```

**Explanation:**
- `$1` is the first command-line argument.
- `-z "$1"` checks whether the first argument is empty.
- `exit 1` stops the script with a non-zero status, conventionally indicating an error.
- If a name is supplied, the script prints a greeting.

### 3.2 Display script arguments

**File:** `args_demo.sh`

```bash
#!/bin/bash

echo "Script name: $0"
echo "Number of arguments: $#"
echo "All arguments: $@"
```

Run:

```bash
chmod +x args_demo.sh
./args_demo.sh Apple Banana Mango
```

Output:

```text
Script name: ./args_demo.sh
Number of arguments: 3
All arguments: Apple Banana Mango
```

| Variable | Meaning |
|---|---|
| `$0` | Script name or invocation path |
| `$1` | First argument |
| `$2` | Second argument |
| `$#` | Number of arguments |
| `$@` | All positional arguments |

Arguments let a script receive values such as a filename, server name, or environment name when it runs.

---

## Task 4 – Check and Install Packages

**File:** `install_packages.sh`

```bash
#!/bin/bash

# Check whether the script is running as root
if [[ "$EUID" -ne 0 ]]; then
    echo "Please run this script with sudo."
    exit 1
fi

packages=("nginx" "curl" "wget")

apt update

for package in "${packages[@]}"
do
    if dpkg -s "$package" >/dev/null 2>&1; then
        echo "$package is already installed. Skipping."
    else
        echo "Installing $package..."
        if apt install -y "$package"; then
            echo "$package installed successfully."
        else
            echo "Failed to install $package."
            exit 1
        fi
    fi
done

echo "Package check completed."
```

Make it executable and check syntax:

```bash
chmod +x install_packages.sh
bash -n install_packages.sh
```

**Observed result:** Bash displayed no output or error, so it found no syntax errors.

**Status:** This script was **not run** during the practice. Package installation and `apt update` have not been verified.

**Explanation:**
- `$EUID` contains the effective user ID; `0` means root.
- `packages=(...)` stores package names in an array.
- `apt update` refreshes package lists.
- `dpkg -s "$package"` checks package status on Debian/Ubuntu.
- `>/dev/null 2>&1` hides normal output and error output from the status check.
- `apt install -y "$package"` installs a package and automatically answers yes to prompts.
- The `if ... then ... else ... fi` block handles success or failure.

**Safety:** Running this script with `sudo` can change the system by updating package lists and installing software. Review it first and run it only when you intend to make those changes. It is fine to leave it syntax-checked but unexecuted.

---

## Task 5 – Basic Error Handling

**File:** `safe_script.sh`

```bash
#!/bin/bash

set -e

mkdir /tmp/devops-test || echo "Directory already exists"

cd /tmp/devops-test || {
    echo "Failed to enter directory"
    exit 1
}

touch test.txt || {
    echo "Failed to create file"
    exit 1
}

echo "File created successfully"
```

Run:

```bash
chmod +x safe_script.sh
bash -n safe_script.sh
./safe_script.sh
```

Actual output from the practice:

```text
File created successfully
```

**Explanation:**
- `set -e` makes Bash exit when a command fails in many situations, with exceptions for commands used in conditions or handled with operators such as `||`.
- `mkdir /tmp/devops-test` creates a directory under `/tmp`.
- `|| echo "Directory already exists"` runs the fallback command if `mkdir` fails. The command can fail for other reasons too, so the message is a simplification.
- `cd` changes into the directory. The following block prints an error and exits if changing directories fails.
- `touch test.txt` creates an empty file if it does not exist.
- The final `echo` prints a success message.

`set -e` is useful, but it is not a replacement for checking important command results explicitly.

---

## Useful Commands Learned

| Command | Purpose |
|---|---|
| `vim filename.sh` | Create or edit a script with Vim |
| `chmod +x filename.sh` | Add execute permission |
| `ls -l filename.sh` | View permissions and file metadata |
| `cat filename.sh` | Display a file's contents |
| `bash -n filename.sh` | Check Bash syntax without running the script |
| `./filename.sh` | Run an executable script from the current directory |
| `bash filename.sh` | Run a script with Bash without requiring execute permission |

### Why use `chmod +x`?

```bash
chmod +x for_loop.sh
./for_loop.sh
```

`chmod +x` adds execute permission; it does not run the script. Without execute permission, launching the file directly can produce `Permission denied`. You can also run a Bash script with `bash for_loop.sh`.

---

## Day 17 Completion Summary

| Task | Files | Status |
|---|---|---|
| `for` loops | `for_loop.sh`, `count.sh` | Run successfully |
| `while` loop | `countdown.sh` | Run successfully with input `5` |
| Command-line arguments | `greet.sh`, `args_demo.sh` | Tested successfully |
| Package checking/installation | `install_packages.sh` | Syntax checked; not executed |
| Error handling | `safe_script.sh` | Syntax checked and run successfully |

## Key Takeaways

1. Use `for` loops to process a list or a known range of values.
2. Use `while` loops when repetition depends on a condition.
3. Use `$1`, `$#`, and `$@` to work with command-line arguments.
4. Check for required input and return a non-zero status when it is missing.
5. Use `bash -n` to check syntax without executing the script.
6. Be careful with scripts that use `sudo`, update packages, or install software.
7. Use error checks and meaningful messages to make scripts easier to troubleshoot.

