# Day 06 – Linux File I/O Practice

## Objective

Practise creating, writing, appending, and reading a text file using basic Linux commands.

## 1. Create a File

```bash
touch notes.txt
```

Creates an empty file named `notes.txt`.

## 2. Write the First Line

```bash
echo "Linux File I/O Practice" > notes.txt
```

Writes text to the file. `>` replaces the file's existing content.

## 3. Append the Second Line

```bash
echo "Learning Linux file commands." >> notes.txt
```

Adds a line at the end without deleting existing content.

## 4. Append the Third Line with `tee`

```bash
echo "Practising commands for DevOps." | tee -a notes.txt
```

Displays the text in the terminal and appends it to `notes.txt`.

## 5. Read the File

```bash
cat notes.txt
```

Displays the complete file.

```bash
head -n 2 notes.txt
```

Displays the first two lines.

```bash
tail -n 2 notes.txt
```

Displays the last two lines.

## 6. Expected File Content

After running the commands above, `notes.txt` should contain:

```text
Linux File I/O Practice
Learning Linux file commands.
Practising commands for DevOps.
```

## Key Learnings

- `touch` — creates an empty file if it does not exist.
- `>` — writes to a file and overwrites existing content.
- `>>` — appends text without removing existing content.
- `tee -a` — displays text and appends it to a file.
- `cat` — displays the whole file.
- `head` — displays the beginning of a file.
- `tail` — displays the end of a file.
