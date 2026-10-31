# Day 20 – Bash Scripting Challenge: Log Analyzer and Report Generator

## Objective

Build a Bash script that analyzes a log file, counts errors and failures, lists critical events with line numbers, identifies the five most common error messages, and generates a dated summary report.

## Project Files

```text
2026/day-20/
├── log_analyzer.sh
├── log_generator.sh
├── sample_log.log
├── reports/
│   └── log_report_YYYY-MM-DD.txt
└── day-20-solution.md
```

The report filename uses the date when the script runs, for example `log_report_2026-10-31.txt`.

## Concepts Practiced

- Bash command-line arguments and input validation
- `grep`, `awk`, `sort`, `uniq`, `head`, and `wc`
- Command substitution and pipes
- Line-number searching
- Report generation using output redirection
- Safe testing with generated sample logs

---

## Task 1 – Generate a Sample Log File

If `sample_log.log` was not supplied, create it using the generator below. It creates a new file at the requested path.

### `log_generator.sh`

```bash
#!/bin/bash

# Usage: ./log_generator.sh <log_file_path> <num_lines>

if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <log_file_path> <num_lines>"
    exit 1
fi

LOG_FILE="$1"
NUM_LINES="$2"

if [ -e "$LOG_FILE" ]; then
    echo "Error: File already exists: $LOG_FILE"
    exit 1
fi

if ! [[ "$NUM_LINES" =~ ^[0-9]+$ ]] || [ "$NUM_LINES" -lt 1 ]; then
    echo "Error: Number of lines must be a positive integer."
    exit 1
fi

mkdir -p "$(dirname -- "$LOG_FILE")" || exit 1

log_levels=("INFO" "DEBUG" "ERROR" "WARNING" "CRITICAL")
error_messages=(
    "Connection timed out"
    "File not found"
    "Permission denied"
    "Disk I/O error"
    "Out of memory"
)

generate_log_line() {
    local timestamp level message
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    level="${log_levels[$((RANDOM % ${#log_levels[@]}))]}"
    message="Routine event"

    case "$level" in
        ERROR)    message="${error_messages[$((RANDOM % ${#error_messages[@]}))]}" ;;
        CRITICAL) message="Service requires immediate attention" ;;
        WARNING)  message="Resource usage warning" ;;
        DEBUG)    message="Diagnostic information" ;;
        INFO)     message="Service running normally" ;;
    esac

    printf '%s [%s] %s\n' "$timestamp" "$level" "$message"
}

for ((i = 0; i < NUM_LINES; i++)); do
    generate_log_line >> "$LOG_FILE"
done

echo "Log file created at: $LOG_FILE with $NUM_LINES lines."
```

Make the generator executable and create a sample file:

```bash
chmod +x log_generator.sh
./log_generator.sh sample_log.log 100
wc -l sample_log.log
```

The file will contain 100 lines. Because entries are randomly generated, the exact counts vary.

---

## Task 2 – Create the Log Analyzer

### `log_analyzer.sh`

```bash
#!/bin/bash

# Task 1: Validate input
if [ "$#" -ne 1 ]; then
    echo "Usage: $0 <log-file>"
    exit 1
fi

LOG_FILE="$1"

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: Log file does not exist: $LOG_FILE"
    exit 1
fi

if [ ! -r "$LOG_FILE" ]; then
    echo "Error: Log file is not readable: $LOG_FILE"
    exit 1
fi

SCRIPT_DIR="$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd)"
REPORT_DIR="$SCRIPT_DIR/reports"

if ! mkdir -p "$REPORT_DIR"; then
    echo "Error: Could not create report directory."
    exit 1
fi

DATE=$(date '+%Y-%m-%d')
REPORT="$REPORT_DIR/log_report_${DATE}.txt"
LOG_NAME=$(basename -- "$LOG_FILE")

# Task 2: Count lines containing ERROR or Failed (case-insensitive)
ERROR_COUNT=$(grep -Ei -c 'ERROR|Failed' "$LOG_FILE")
TOTAL_LINES=$(wc -l < "$LOG_FILE")

# Task 3: Find critical events with line numbers
CRITICAL_EVENTS=$(grep -n 'CRITICAL' "$LOG_FILE" || true)

# Task 4: Extract the message after the [ERROR] marker and count occurrences
TOP_ERRORS=$(
    awk '
        /\[ERROR\]/ {
            sub(/^.*\[ERROR\][[:space:]]*/, "")
            print
        }
    ' "$LOG_FILE" |
    sort |
    uniq -c |
    sort -rn |
    head -5
)

# Display results
echo "Log Analysis Report"
echo "==================="
echo "Date of Analysis: $DATE"
echo "Log File: $LOG_NAME"
echo "Total Lines Processed: $TOTAL_LINES"
echo "Total Error Count (ERROR or Failed): $ERROR_COUNT"

echo
echo "--- Critical Events ---"
if [ -n "$CRITICAL_EVENTS" ]; then
    printf '%s\n' "$CRITICAL_EVENTS"
else
    echo "No critical events found."
fi

echo
echo "--- Top 5 Error Messages ---"
if [ -n "$TOP_ERRORS" ]; then
    printf '%s\n' "$TOP_ERRORS"
else
    echo "No ERROR messages found."
fi

# Task 5: Write the report
{
    echo "Log Analysis Report"
    echo "==================="
    echo "Date of Analysis: $DATE"
    echo "Log File: $LOG_NAME"
    echo "Total Lines Processed: $TOTAL_LINES"
    echo "Total Error Count (ERROR or Failed): $ERROR_COUNT"
    echo
    echo "--- Top 5 Error Messages ---"
    if [ -n "$TOP_ERRORS" ]; then
        printf '%s\n' "$TOP_ERRORS"
    else
        echo "No ERROR messages found."
    fi
    echo
    echo "--- Critical Events ---"
    if [ -n "$CRITICAL_EVENTS" ]; then
        printf '%s\n' "$CRITICAL_EVENTS"
    else
        echo "No critical events found."
    fi
} > "$REPORT" || {
    echo "Error: Could not write report: $REPORT"
    exit 1
}

echo
echo "Report generated: $REPORT"
```

### Important implementation details

- `grep -Ei -c 'ERROR|Failed'` counts lines matching either term, case-insensitively. A line containing both terms is counted once.
- `grep -n 'CRITICAL'` prints matching lines with line numbers.
- The top-five pipeline removes the timestamp and `[ERROR]` prefix before counting messages. It matches the sample format generated above.
- `sort | uniq -c` groups identical messages and counts them; `sort -rn | head -5` selects the five highest counts.
- If no critical or error messages exist, the script displays a helpful message.
- The optional archive feature is not enabled because moving an input log changes its location. Add it only after verifying the analyzer and report.

---

## Task 3 – Run and Test the Scripts

Make the scripts executable and check their syntax:

```bash
chmod +x log_analyzer.sh log_generator.sh
bash -n log_analyzer.sh
bash -n log_generator.sh
```

No output from `bash -n` means no syntax errors were detected.

Create a sample log if needed:

```bash
./log_generator.sh sample_log.log 100
```

Run the analyzer:

```bash
./log_analyzer.sh sample_log.log
```

Inspect the report:

```bash
ls -lh reports/
cat "reports/log_report_$(date +%Y-%m-%d).txt"
```

Check the number of lines:

```bash
wc -l sample_log.log
```

### Test invalid input

No argument:

```bash
./log_analyzer.sh
```

Expected: a usage message and a non-zero exit status.

Missing file:

```bash
./log_analyzer.sh missing.log
```

Expected: `Error: Log file does not exist: missing.log`

### Example output format

Actual counts vary depending on the generated sample log. Do not submit placeholder counts as real test results.

```text
Log Analysis Report
===================
Date of Analysis: YYYY-MM-DD
Log File: sample_log.log
Total Lines Processed: 100
Total Error Count (ERROR or Failed): <actual count>

--- Critical Events ---
<matching lines with line numbers, or a no-events message>

--- Top 5 Error Messages ---
<count> Connection timed out
<count> File not found
<count> Permission denied
<count> Disk I/O error
<count> Out of memory

Report generated: .../reports/log_report_YYYY-MM-DD.txt
```

---

## Commands and Tools Used

### `grep`

Searches for text patterns.

```bash
grep -n 'CRITICAL' sample_log.log
grep -Ei 'ERROR|Failed' sample_log.log
```

### `wc -l`

Counts lines.

```bash
wc -l sample_log.log
```

### `awk`

Processes text and extracts the message after `[ERROR]`.

```bash
awk '/\[ERROR\]/ { sub(/^.*\[ERROR\][[:space:]]*/, ""); print }' sample_log.log
```

### `sort`

Sorts messages so identical messages appear together.

```bash
sort
```

### `uniq -c`

Counts adjacent duplicate lines; it is commonly used after `sort`.

```bash
sort | uniq -c
```

### `sort -rn` and `head -5`

Sorts counts in descending numeric order and displays the top five.

```bash
sort -rn | head -5
```

### `date`

Creates a date string for the report filename.

```bash
date '+%Y-%m-%d'
```

### `chmod +x` and `bash -n`

Makes scripts executable and checks syntax without executing them.

```bash
chmod +x log_analyzer.sh
bash -n log_analyzer.sh
```

---

## Log Analysis Flow

```text
Input log file
      |
      v
Validate argument and file
      |
      v
Count total lines and errors
      |
      v
Find CRITICAL events with line numbers
      |
      v
Extract ERROR messages
      |
      v
sort -> uniq -c -> sort -rn -> head -5
      |
      v
Display results and write dated report
```

## Troubleshooting

1. **Usage message appears:** pass exactly one log-file path.
2. **File does not exist:** check the path with `ls -l sample_log.log`.
3. **Permission denied:** check permissions with `ls -l`; use `chmod +x` for your own scripts.
4. **No critical events:** the sample log may not contain any `CRITICAL` lines; this is a valid result.
5. **Report not found:** check the `reports/` directory and use the current date in the filename.
6. **Top messages differ:** random log generation produces different counts each time.

## What I Learned – 3 Key Points

1. `grep` can filter logs for errors, failures, and critical events, including line-number searches.
2. Pipes combine commands such as `awk`, `sort`, `uniq`, and `head` to turn raw logs into useful summaries.
3. Bash can automate repeatable log analysis and save results in a dated report for easier troubleshooting.

## Day 20 Takeaway

This project demonstrates how shell scripting can reduce manual log inspection by validating input, identifying important events, summarizing recurring errors, and generating a report. These are useful fundamentals for Linux administration and DevOps troubleshooting.

## Submission Checklist

- [ ] `log_analyzer.sh`
- [ ] `log_generator.sh`
- [ ] `sample_log.log`
- [ ] Generated report in `reports/log_report_<date>.txt`
- [ ] `day-20-solution.md`
- [ ] Run syntax checks and test the analyzer
- [ ] Confirm report contents and actual counts
- [ ] Commit and push to the GitHub fork

## Learn in Public

Today I practiced Bash scripting by building a log analyzer that searches for errors, identifies critical events, finds common error messages, and generates a dated report.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
