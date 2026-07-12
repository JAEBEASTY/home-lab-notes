# 🐧 Linux Lab: Bash Scripting — System Health Report

> **Lab Type:** Beginner — Guided  
> **OS:** Linux (any desktop distro) / macOS  
> **Completed:** June 2026  
> **Cert Relevance:** CompTIA Network+ (N10-009) · CompTIA Security+ (SY0-701)

---

## 📋 Table of Contents

1. [Lab Overview](#lab-overview)
2. [Prerequisites](#prerequisites)
3. [Key Concepts](#key-concepts)
4. [The Script](#the-script)
5. [Step-by-Step Breakdown](#step-by-step-breakdown)
6. [How to Run It](#how-to-run-it)
7. [Expected Output](#expected-output)
8. [Command Reference](#command-reference)
9. [Bonus Tips](#bonus-tips)
10. [Certification Mapping](#certification-mapping)
11. [Next Steps](#next-steps)

---

## Lab Overview

In this lab a bash script is written that automatically generates a system health report. Instead of running commands one at a time manually, the script runs them all at once and formats the output cleanly — the kind of tool a real sysadmin would write and schedule to run automatically.

**What was accomplished:**
- Learned the structure of a professional bash script
- Used `date`, `uptime`, and `df` to collect system information
- Used `echo` and `printf` to format readable output
- Used command substitution `$(...)` to embed command output into strings
- Created a reusable function to print section headers
- Made the script executable with `chmod` and ran it from the terminal

---

## Prerequisites

- A Linux desktop or macOS with a terminal open
- A text editor — nano, VSCode, or any editor
- No sudo required
- No additional software needed

---

## Key Concepts

### The Shebang Line
```bash
#!/bin/bash
```
Always the first line of any bash script. Tells the operating system to use the bash interpreter to run everything below it. Without it, the OS may use the wrong shell and behave unexpectedly.

### echo
Prints a line of text to the terminal. `echo ""` prints a blank line for spacing.

### Command Substitution `$(...)`
Runs a command inside the parentheses and drops its output directly into the string:
```bash
echo "Today is $(date)"
```
This is one of the most useful patterns in bash scripting.

### Functions
A reusable block of code you define once and call multiple times. Saves repetition:
```bash
print_header() {
    echo "============"
    echo "  $1"
    echo "============"
}
```
`$1` is a placeholder for whatever text you pass in when you call it.

---

## The Script

```bash
#!/bin/bash

# ============================================================
# system_health_report.sh
# Generates a simple, readable system health report.
# Usage: bash system_health_report.sh
# ============================================================

# --- Helper: prints a section header ---
print_header() {
    echo ""
    echo "============================================"
    echo "  $1"
    echo "============================================"
}

# --- Report Title ---
echo ""
echo "############################################"
echo "#       SYSTEM HEALTH REPORT               #"
echo "############################################"

# -----------------------------------------------------------
# SECTION 1: Current Date & Time
# -----------------------------------------------------------
print_header "1. DATE & TIME"
echo "  $(date '+%A, %B %d %Y  |  %I:%M:%S %p')"

# -----------------------------------------------------------
# SECTION 2: System Uptime
# -----------------------------------------------------------
print_header "2. SYSTEM UPTIME"
echo "  Server has been running: $(uptime -p)"

# -----------------------------------------------------------
# SECTION 3: Disk Space
# -----------------------------------------------------------
print_header "3. DISK SPACE USAGE"
echo ""
df -h

# --- Footer ---
echo ""
echo "############################################"
echo "#         END OF REPORT                    #"
echo "############################################"
echo ""
```

---

## Step-by-Step Breakdown

### Step 1 — Plan the Structure
Before writing any code, plan the four building blocks:
1. Shebang line — tells the OS to use bash
2. A reusable print-header function — avoids copy-pasting dividers
3. One block per section — date, uptime, disk
4. A footer — confirms the script ran to completion

### Step 2 — The print_header Function
Instead of repeating divider lines for every section, define them once:
```bash
print_header() {
    echo ""
    echo "============================================"
    echo "  $1"
    echo "============================================"
}
```
Call it with: `print_header "1. DATE & TIME"`

### Step 3 — Date & Time Section
```bash
echo "  $(date '+%A, %B %d %Y  |  %I:%M:%S %p')"
```

**date format codes:**

| Code | Meaning | Example |
|------|---------|---------|
| `%A` | Full weekday | Friday |
| `%B` | Full month name | June |
| `%d` | Day of month | 12 |
| `%Y` | 4-digit year | 2026 |
| `%I` | Hour (12-hour) | 10 |
| `%M` | Minutes | 45 |
| `%S` | Seconds | 30 |
| `%p` | AM/PM | AM |

### Step 4 — Uptime Section
```bash
echo "  Server has been running: $(uptime -p)"
```
The `-p` flag makes output human-readable: `up 3 hours, 42 minutes`  
Without it you get a dense raw line like: `10:45:03 up 3:42, 2 users, load average: 0.00`

### Step 5 — Disk Space Section
```bash
df -h
```
`df` = disk filesystem. `-h` = human-readable (converts bytes to GB/MB automatically). Outputs a table showing every mounted filesystem, its size, how much is used, and how much is available.

---

## How to Run It

Save the file, then make it executable and run it:

```bash
chmod +x system_health_report.sh
bash system_health_report.sh
```

---

## Expected Output

```
############################################
#       SYSTEM HEALTH REPORT               #
############################################

============================================
  1. DATE & TIME
============================================
  Friday, June 12 2026  |  10:32:15 AM

============================================
  2. SYSTEM UPTIME
============================================
  Server has been running: up 3 hours, 42 minutes

============================================
  3. DISK SPACE USAGE
============================================

Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   18G   30G  38% /
tmpfs           2.0G     0  2.0G   0% /dev/shm

############################################
#         END OF REPORT                    #
############################################
```

---

## Command Reference

| Command | What It Does |
|---------|-------------|
| `date '+format'` | Print current date/time with custom formatting |
| `uptime -p` | Human-readable system uptime |
| `df -h` | Disk usage in human-readable format |
| `echo` | Print text to the terminal |
| `echo ""` | Print a blank line for spacing |
| `$(...)` | Command substitution — embed output into a string |
| `chmod +x file` | Make a script executable |
| `bash script.sh` | Run a bash script |

---

## Bonus Tips

- **Schedule it with cron** to run automatically every morning:
```bash
crontab -e
# Add this line to run at 8am daily:
0 8 * * * bash /path/to/system_health_report.sh >> /var/log/health.log
```

- **Save output to a file** by appending when you run it:
```bash
bash system_health_report.sh >> report.txt
```

- **Add more sections** by following the same pattern:
```bash
# Memory usage
print_header "4. MEMORY USAGE"
free -h

# CPU load
print_header "5. CPU LOAD"
top -bn1 | grep "Cpu(s)"
```

---

## Certification Mapping

### CompTIA Network+ (N10-009)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 3 — Network Operations | Scripting for automation of monitoring tasks |
| Domain 3 — Network Operations | System health monitoring tools and output |

### CompTIA Security+ (SY0-701)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 2 — Threats, Vulnerabilities & Mitigations | Monitoring disk and system resources |
| Domain 4 — Identity & Access Management | Automating audit and reporting tasks |

---

## Next Steps

- Add a **memory usage section** using `free -h`
- Add a **CPU load section** using `top -bn1`
- Add **color-coded alerts** when disk usage goes above 80%
- Schedule the script with **cron** to run and log automatically
- Combine with the **System Monitoring & Logs** lab to add log review to the report

---

*Lab completed as part of CompTIA Network+ (N10-009) and Security+ (SY0-701) home lab study — June 2026*
