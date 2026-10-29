# Day 18 – Shell Scripting: Functions & Intermediate Concepts

## Objective

Learn to write cleaner and reusable Bash scripts using functions, arguments, local variables, command output, and strict mode.

## Task 1 – Basic Functions

### Create `functions.sh`

```bash
#!/bin/bash

greet() {
    local name="$1"
    echo "Hello, $name!"
}

add() {
    local num1="$1"
    local num2="$2"
    echo $((num1 + num2))
}

greet "Nick"

result=$(add 10 20)
echo "Sum: $result"
```

### Run

```bash
chmod +x functions.sh
./functions.sh
```

### Expected output

```text
Hello, Nick!
Sum: 30
```

### Explanation

- `greet()` receives a name as its first argument (`$1`).
- `add()` receives two numbers (`$1` and `$2`) and prints their sum.
- `local` keeps each variable scoped to its function.
- `result=$(add 10 20)` captures the output printed by `add` into a variable. In Bash, this is command substitution; it is not the same as a function's numeric exit status.

---

## Task 2 – Disk and Memory Checks

### Create `disk_check.sh`

```bash
#!/bin/bash

check_disk() {
    echo "Disk Usage:"
    df -h /
}

check_memory() {
    echo "Memory Usage:"
    free -h
}

main() {
    echo "===== System Resource Check ====="
    check_disk
    echo
    check_memory
}

main
```

### Run

```bash
chmod +x disk_check.sh
./disk_check.sh
```

### Example output

```text
===== System Resource Check =====
Disk Usage:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G    8G   12G  40% /

Memory Usage:
               total        used        free      shared  buff/cache   available
Mem:            3.8Gi       1.2Gi       1.5Gi       100Mi       1.1Gi       2.4Gi
Swap:           2.0Gi          0B       2.0Gi
```

> The values and device name will vary by system. `df -h /` reports the filesystem containing `/`, and `free -h` reports memory in human-readable units.

### Explanation: output vs return status

These functions print command output. They do not return a disk or memory value as a Bash function exit status. A Bash function's `return` status is an integer from 0 to 255, commonly used to indicate success (`0`) or failure (non-zero). To capture text output, use command substitution such as `result=$(some_function)`.

---

## Task 3 – Strict Mode: `set -euo pipefail`

### Create `strict_demo.sh`

This safe demonstration shows the flags separately. The deliberately failing examples are commented out so the main script can finish successfully. Uncomment and test each example separately to observe its behavior.

```bash
#!/bin/bash
set -euo pipefail

echo "Starting strict mode demonstration"

NAME="Nick"
echo "Defined variable: $NAME"

echo "Strict mode is enabled."
echo "To test each failure, follow the examples in the notes."
```

### Run

```bash
chmod +x strict_demo.sh
./strict_demo.sh
```

### Expected output

```text
Starting strict mode demonstration
Defined variable: Nick
Strict mode is enabled.
To test each failure, follow the examples in the notes.
```

### What each flag does

- `set -e`: exits when a command returns a non-zero status in situations where Bash applies the `errexit` rule. There are exceptions, especially in conditions and some command lists.
- `set -u`: treats an unset variable expansion as an error.
- `set -o pipefail`: makes a pipeline return a failure status if a command in the pipeline fails, rather than considering only the last command's status.
- `set -euo pipefail`: enables all three behaviors together.

### Test `set -u` in isolation

Create a temporary test file named `test_unset.sh`:

```bash
#!/bin/bash
set -u

echo "Before unset variable"
echo "$UNDEFINED_VARIABLE"
echo "This line normally will not run"
```

Run it:

```bash
bash test_unset.sh
```

Expected behavior: Bash reports an unbound variable error at `UNDEFINED_VARIABLE` and exits before the last line.

### Test `set -e` in isolation

Create `test_errexit.sh`:

```bash
#!/bin/bash
set -e

echo "Before failing command"
false
echo "This line normally will not run"
```

Run it:

```bash
bash test_errexit.sh
```

Expected output:

```text
Before failing command
```

The `false` command returns a non-zero status, so the final `echo` is skipped.

### Test `pipefail` in isolation

Create `test_pipefail.sh`:

```bash
#!/bin/bash
set -o pipefail

false | true
echo "Pipeline exit status: $?"
```

Run it:

```bash
bash test_pipefail.sh
```

Expected output:

```text
Pipeline exit status: 1
```

`false` fails and `true` succeeds. Without `pipefail`, the pipeline status would normally be the status of the final command (`true`, which is `0`). With `pipefail`, the pipeline reports failure.

> In a script that has `set -e` enabled, a failing pipeline may stop the script immediately. That is why the pipeline test above uses `set -o pipefail` alone.

---

## Task 4 – Local Variables

### Create `local_demo.sh`

```bash
#!/bin/bash

show_local() {
    local LOCAL_VAR="I am local"
    echo "Inside show_local: $LOCAL_VAR"
}

show_regular() {
    REGULAR_VAR="I am a regular variable"
    echo "Inside show_regular: $REGULAR_VAR"
}

show_local
echo "Outside function, LOCAL_VAR: ${LOCAL_VAR:-not available}"

show_regular
echo "Outside function, REGULAR_VAR: $REGULAR_VAR"
```

### Run

```bash
chmod +x local_demo.sh
./local_demo.sh
```

### Expected output

```text
Inside show_local: I am local
Outside function, LOCAL_VAR: not available
Inside show_regular: I am a regular variable
Outside function, REGULAR_VAR: I am a regular variable
```

### Explanation

- `local LOCAL_VAR=...` limits the variable to the function where it is declared.
- A regular variable assigned inside a Bash function is generally still available after the function finishes.
- `${LOCAL_VAR:-not available}` prints `not available` if `LOCAL_VAR` is unset or empty. This avoids an unset-variable error if strict mode is enabled.

---

## Task 5 – System Information Reporter

### Create `system_info.sh`

```bash
#!/bin/bash
set -euo pipefail

print_header() {
    echo
    echo "========================================"
    echo "$1"
    echo "========================================"
}

hostname_os() {
    print_header "HOSTNAME & OS"
    echo "Hostname: $(hostname)"

    if [[ -r /etc/os-release ]]; then
        # Load OS details from the standard release file.
        . /etc/os-release
        echo "OS: ${PRETTY_NAME:-Unknown Linux distribution}"
    else
        echo "OS information not available"
    fi
}

show_uptime() {
    print_header "UPTIME"
    uptime
}

disk_usage() {
    print_header "DISK USAGE"
    echo "Largest top-level directories on the root filesystem:"
    # -x stays on the same filesystem; permission/read errors are suppressed.
    # Some directories may be omitted if access is denied.
    du -xhd 1 / 2>/dev/null | sort -hr | head -5
}

memory_usage() {
    print_header "MEMORY USAGE"
    free -h
}

top_cpu_processes() {
    print_header "TOP 5 CPU-CONSUMING PROCESSES"
    ps aux --sort=-%cpu | head -6
}

main() {
    hostname_os
    show_uptime
    disk_usage
    memory_usage
    top_cpu_processes
}

main "$@"
```

### Run

```bash
chmod +x system_info.sh
./system_info.sh
```

### Example output

```text
========================================
HOSTNAME & OS
========================================
Hostname: Nick
OS: Ubuntu 24.04 LTS

========================================
UPTIME
========================================
23:10:00 up 2 days, 4:15, 1 user, load average: 0.20, 0.18, 0.15

========================================
DISK USAGE
========================================
Largest top-level directories on the root filesystem:
8.5G    /
3.2G    /usr
1.8G    /var
1.2G    /home
500M    /opt

========================================
MEMORY USAGE
========================================
               total        used        free      shared  buff/cache   available
Mem:            3.8Gi       1.2Gi       1.5Gi       100Mi       1.1Gi       2.4Gi
Swap:           2.0Gi          0B       2.0Gi

========================================
TOP 5 CPU-CONSUMING PROCESSES
========================================
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      1234 12.5  2.1  ...   ...  ?        S    10:00   0:10 application
ubuntu    2345  8.2  1.5  ...   ...  ?        S    10:01   0:05 python
```

> Example values are illustrative; your hostname, OS, disk sizes, memory values, processes, and CPU usage will differ. `head -6` includes one header row plus up to five process rows. The disk command reports up to five entries and may show fewer if some directories cannot be read.

### Function summary

| Function | Purpose |
|---|---|
| `print_header()` | Prints a readable section heading |
| `hostname_os()` | Displays hostname and OS |
| `show_uptime()` | Displays system uptime |
| `disk_usage()` | Lists the largest top-level directory entries it can read |
| `memory_usage()` | Displays memory usage |
| `top_cpu_processes()` | Displays the five highest CPU-consuming processes, plus the header |
| `main()` | Calls the reporting functions in order |

---

## Commands Used

```bash
chmod +x functions.sh
./functions.sh

chmod +x disk_check.sh
./disk_check.sh

chmod +x strict_demo.sh
./strict_demo.sh

chmod +x local_demo.sh
./local_demo.sh

chmod +x system_info.sh
./system_info.sh
```

Optional: run Bash syntax checks before running each script:

```bash
bash -n functions.sh
bash -n disk_check.sh
bash -n strict_demo.sh
bash -n local_demo.sh
bash -n system_info.sh
```

No output from `bash -n` normally means Bash did not detect a syntax error. It does not prove the script will behave correctly at runtime.

## Scripts Created

```text
functions.sh
disk_check.sh
strict_demo.sh
local_demo.sh
system_info.sh
```

The three separate strict-mode experiments are optional practice files:

```text
test_unset.sh
test_errexit.sh
test_pipefail.sh
```

## What I Learned

### 1. Functions make scripts reusable

Functions group commands into named blocks. Arguments such as `$1` and `$2` let the same function work with different values.

### 2. Strict mode helps detect common mistakes

`set -euo pipefail` helps catch command failures, unset variables, and failed commands inside pipelines. It is useful, but it does not replace testing or explicit error handling.

### 3. Local variables reduce accidental side effects

Using `local` inside functions keeps temporary values scoped to the function and helps prevent them from changing other parts of the script.

## Key Takeaway

A maintainable Bash script can use this structure:

```text
Shebang
  ↓
Strict Mode
  ↓
Functions
  ↓
Local Variables and Arguments
  ↓
Main Function
  ↓
Readable Output
```

These techniques help make shell scripts easier to read, test, maintain, and reuse in DevOps automation.

