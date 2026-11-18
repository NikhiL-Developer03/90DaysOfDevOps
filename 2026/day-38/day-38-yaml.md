# Day 38 – YAML Basics

## Objective

Learn YAML syntax and understand how YAML is used to store configuration data before working with CI/CD pipelines.

## 1. What is YAML?

YAML stands for **YAML Ain't Markup Language**.

It is a human-readable data serialization format commonly used for configuration files.

YAML uses:
- Key-value pairs
- Lists
- Nested objects
- Indentation to define structure

## 2. `person.yaml`

Created a YAML file describing my DevOps learning profile.

```yaml
name: Nikhil
role: DevOps Learner
experience_years: 0
learning: true

tools:
  - Linux
  - Git
  - Docker
  - AWS
  - Jenkins

hobbies: [Learning DevOps, Coding, Building Projects]
```

### Understanding Key-Value Pairs

| Key | Value | Meaning |
|---|---|---|
| `name` | Nikhil | My name |
| `role` | DevOps Learner | Current learning role |
| `experience_years` | 0 | Years of experience |
| `learning` | true | Boolean value |

## 3. YAML Lists

YAML supports two common ways to write lists.

### Block Style

Each item is written on a separate line using a hyphen (`-`).

```yaml
tools:
  - Linux
  - Git
  - Docker
  - AWS
  - Jenkins
```

### Flow / Inline Style

Items are written inside square brackets and separated by commas.

```yaml
hobbies: [Learning DevOps, Coding, Building Projects]
```

**Difference:**

- Block style: Each item appears on a separate line.
- Flow style: All items appear on one line inside `[]`.

## 4. Nested Objects – `server.yaml`

Created a separate YAML file to describe a server and its database configuration.

```yaml
server:
  name: dev-server
  ip: 192.168.1.10
  port: 8080

database:
  host: db
  name: day38db
  credentials:
    user: appuser
    password: change-me
```

### Understanding Nested Objects

The `server` object contains:

- `name`
- `ip`
- `port`

The `database` object contains:

- `host`
- `name`
- `credentials`

The `credentials` object contains:

- `user`
- `password`

**Important:** Indentation defines the relationship between keys and nested objects.

## 5. Multi-Line Strings

YAML supports two common block styles for multi-line strings.

### Literal Block Style (`|`)

```yaml
startup_script_literal: |
  echo "Starting application"
  echo "Checking dependencies"
```

The `|` character preserves line breaks.

Use it for shell scripts and text where line breaks are important.

### Folded Block Style (`>`)

```yaml
startup_script_folded: >
  echo "Starting application"
  echo "Checking dependencies"
```

The `>` character folds normal line breaks into spaces.

Use it for longer paragraphs or text where line wrapping should not create separate lines.

### Difference Between `|` and `>`

| Symbol | Behaviour |
|---|---|
| `|` | Preserves line breaks |
| `>` | Folds normal line breaks into spaces |

## 6. Important YAML Rules

- Use spaces, never tabs for indentation.
- Indentation defines the structure.
- Two spaces per indentation level is a common convention.
- Use `key: value` for key-value pairs.
- Use `-` for block-style lists.
- Use `[]` for inline lists.
- Boolean values can be `true` or `false`.
- Quoting may be necessary when values contain special characters.

### Boolean Example

```yaml
learning: true
```

This is a Boolean value.

```yaml
learning: "true"
```

This is a string value.

## 7. YAML Validation

YAML files should be validated to identify syntax and indentation mistakes.

### Install yamllint

```bash
sudo apt update
sudo apt install yamllint -y
```

### Check installation

```bash
yamllint --version
```

### Validate YAML files

```bash
yamllint person.yaml
yamllint server.yaml
```

**Validation status:** Pending. The `yamllint` command was initially unavailable on my Ubuntu system. Installation and final validation still need to be completed.

### Why is validation important?

- Identifies indentation problems.
- Detects YAML syntax errors.
- Helps maintain clean configuration files.
- Prevents configuration mistakes in CI/CD pipelines.

## 8. Spot the Difference

### Correct YAML

```yaml
name: devops
tools:
  - docker
  - kubernetes
```

### Broken YAML

```yaml
name: devops
tools:
- docker
  - kubernetes
```

**What is wrong?**

The second block has inconsistent indentation for its list items.

YAML uses indentation to determine structure, so incorrect indentation can cause parsing errors or unintended structure.

## 9. Key Learnings

1. YAML is whitespace-sensitive, so indentation matters.
2. Lists can be written using block style or flow/inline style.
3. Literal (`|`) and folded (`>`) block styles handle line breaks differently.
4. Nested objects use indentation to define relationships.
5. YAML files should be validated before using them in configuration workflows.

## 10. Project Files

```text
day38-yaml/
├── person.yaml
└── server.yaml
```

### Task Progress

- [x] Task 1 – Key-value pairs
- [x] Task 2 – YAML lists
- [x] Task 3 – Nested objects and separate server file
- [ ] Task 4 – Multi-line strings in `server.yaml`
- [ ] Task 5 – Install yamllint and validate both files
- [ ] Task 6 – Test and explain the broken YAML indentation

**Next Step:** Complete multi-line strings, YAML validation, and the intentional indentation-error experiment.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham