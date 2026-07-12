# 🐧 Linux Lab: System Monitoring & Logs

> **Lab Type:** Beginner — Guided  
> **OS:** Linux (Ubuntu / Debian-based desktop)  
> **Completed:** June 2026  
> **Cert Relevance:** CompTIA Security+ (SY0-701) · CompTIA Network+ (N10-009)

---

## 📋 Table of Contents

1. [Lab Overview](#lab-overview)
2. [Prerequisites](#prerequisites)
3. [Key Concepts](#key-concepts)
4. [Step 1 — Check Running Processes with top](#step-1--check-running-processes-with-top)
5. [Step 2 — Cleaner Process Snapshot with ps](#step-2--cleaner-process-snapshot-with-ps)
6. [Step 3 — Check Who Is Logged In](#step-3--check-who-is-logged-in)
7. [Step 4 — Check Login History](#step-4--check-login-history)
8. [Step 5 — Read the Auth Log](#step-5--read-the-auth-log)
9. [Step 6 — Watch a Log Update in Real Time](#step-6--watch-a-log-update-in-real-time)
10. [Quick Reference — All Commands](#quick-reference--all-commands)
11. [Reading Log Timestamps](#reading-log-timestamps)
12. [Troubleshooting](#troubleshooting)
13. [Certification Mapping](#certification-mapping)
14. [Next Steps](#next-steps)

---

## Lab Overview

In this lab you take on the role of a junior sysadmin investigating a server that has been acting slow. You check running processes, review system logs, audit login history, and watch the auth log update live in real time.

This lab covers the tools a sysadmin reaches for first when something is wrong — and maps directly to log analysis and monitoring objectives on the Security+ exam.

**What you did:**
- Used `top` and `ps aux` to check running processes and resource usage
- Used `who` and `w` to see who is currently logged into the machine
- Used `last` to review the full login history
- Read `auth.log` to see recent login and sudo activity
- Used `tail -f` to watch a log file update live in real time
- Triggered a real log entry by running a sudo command and watching it appear instantly

---

## Prerequisites

- A Linux desktop — Ubuntu, Debian, Linux Mint, or any similar distribution
- A terminal window open and ready
- `sudo` access for reading protected log files
- No additional software needed — every command is built into Linux

---

## Key Concepts

### Log Files
Linux records almost everything that happens on the system in plain text files stored in `/var/log`. Think of it as the system's black box. Events like logins, sudo usage, errors, and service starts/stops are all captured with exact timestamps.

Key log files:

| File | What It Records |
|------|----------------|
| `/var/log/auth.log` | Login attempts, sudo usage, authentication events |
| `/var/log/syslog` | General system events |
| `/var/log/messages` | General system events (used on some distros instead of syslog) |
| `/var/log/wtmp` | Binary login history (read with the `last` command) |

### Processes
Every program running on Linux is a process. Each one has a Process ID (PID), an owner, and uses some amount of CPU and memory. Monitoring processes helps you spot what is consuming resources or running unexpectedly.

### Why Log Monitoring Matters for Security+
Log analysis is a core Security+ skill. Logs give you a timestamped record of exactly what happened, who did it, and when — making them essential for detecting intrusions, auditing user activity, and building incident timelines.

---

## Step 1 — Check Running Processes with top

```bash
top
```

`top` is a live, updating dashboard of everything running on your machine. It refreshes every few seconds.

**What to look at:**

| Column | Meaning |
|--------|---------|
| `PID` | Process ID — Linux's unique number for each running process |
| `USER` | The user account that owns the process |
| `%CPU` | How much CPU the process is currently using |
| `%MEM` | How much memory the process is using |
| `COMMAND` | The name of the process |

The top section shows overall system stats — total CPU load, total memory used, and how many processes are running.

To exit `top`, press `q`.

---

## Step 2 — Cleaner Process Snapshot with ps

```bash
ps aux --sort=-%cpu | head -10
```

Shows the top 10 processes sorted by CPU usage, highest first. Unlike `top`, this is a one-time snapshot rather than a live view.

**Flag breakdown:**

| Flag | Meaning |
|------|---------|
| `ps` | Process status — lists running processes |
| `a` | Show processes from all users, not just your own |
| `u` | Show the user who owns each process |
| `x` | Include processes not attached to a terminal |
| `--sort=-%cpu` | Sort by CPU usage, highest first (- means descending) |
| `head -10` | Only show the first 10 results |

> **tip:** Use `ps` when you want to capture a moment in time. Use `top` when you want to watch things change live.

---

## Step 3 — Check Who Is Logged In

### Basic view

```bash
who
```

Shows every user currently logged into the machine, which terminal they are on, and when they logged in.

**Example output:**
```
jaelan   pts/0   2026-06-12 10:30
alice    pts/1   2026-06-12 10:45
```

### Detailed view

```bash
w
```

Same as `who` but also shows what command each user is currently running — useful for spotting unusual activity.

**Example output:**
```
USER     TTY    FROM   LOGIN@  IDLE  COMMAND
jaelan   pts/0  -      10:30   0.00s  w
alice    pts/1  -      10:45   1:20   bash
```

---

## Step 4 — Check Login History

```bash
last | head -10
```

Shows the last 10 login and logout events on the machine — username, terminal, login source, date, and time.

**Example output:**
```
jaelan  pts/0   Fri Jun 12 10:30   still logged in
alice   pts/1   Fri Jun 12 10:45   still logged in
bob     pts/0   Thu Jun 11 09:15 - 09:45  (00:30)
```

> `last` reads from `/var/log/wtmp` — a binary file Linux uses to track all login sessions. You cannot `cat` this file directly; `last` is the tool that reads and formats it for you.

---

## Step 5 — Read the Auth Log

```bash
sudo cat /var/log/auth.log | tail -20
```

Shows the last 20 lines of the authentication log — the most recent login attempts, sudo usage, and authentication events.

> If your system uses `messages` instead of `auth.log`: `sudo cat /var/log/messages | tail -20`

### Reading an auth.log Entry

**Example line:**
```
Jun 12 10:45:03 hostname sudo: jaelan : TTY=pts/0 ; PWD=/home/jaelan ; USER=root ; COMMAND=/bin/ls
```

**Breaking it down:**

| Part | Meaning |
|------|---------|
| `Jun 12 10:45:03` | Date and time the event happened |
| `hostname` | The name of the machine |
| `sudo:` | The service that logged this event |
| `jaelan` | The user who ran the command |
| `TTY=pts/0` | Which terminal they were using |
| `PWD=/home/jaelan` | What directory they were in |
| `USER=root` | What user they escalated to |
| `COMMAND=/bin/ls` | The exact command that was run |

### Common Entries You Will See

| Entry | What It Means |
|-------|--------------|
| `session opened for user root` | A sudo command was executed — Linux opened a temporary root session |
| `session closed for user root` | The sudo command finished — root session ended |
| `authentication failure` | Someone entered the wrong password |
| `accepted password for [user]` | A successful login occurred |

> `session opened` and `session closed` always appear as a pair and are completely normal. Seeing them at 3am from an unexpected user is a red flag.

---

## Step 6 — Watch a Log Update in Real Time

```bash
sudo tail -f /var/log/auth.log
```

`tail -f` follows the log file live and prints new lines the moment they are written. The terminal will pause and wait — that is normal. It is listening.

### How to Test It

1. Run the `tail -f` command in **terminal 1**
2. Open a **second terminal**
3. Run any sudo command in terminal 2:
```bash
sudo ls
```
4. Switch back to terminal 1
5. Watch the new entries appear in real time

### What You Will See

```
Jun 12 10:52:01 hostname sudo: jaelan : TTY=pts/1 ; USER=root ; COMMAND=/bin/ls
Jun 12 10:52:01 hostname systemd-logind: session opened for user root
Jun 12 10:52:01 hostname systemd-logind: session closed for user root
```

To stop watching, press `Ctrl+C`.

### Why This Matters

In a real environment this is how you catch suspicious activity as it happens. If you saw `session opened for user root` at 3am from a user who should not be working — you have the exact timestamp, the username, and the command they ran sitting right there in the log. That is a Security+ exam scenario.

---

## Quick Reference — All Commands

| Command | What It Does |
|---------|-------------|
| `top` | Live process dashboard — CPU, memory, all running processes |
| `ps aux --sort=-%cpu \| head -10` | Top 10 processes by CPU usage, one-time snapshot |
| `who` | Who is currently logged into the machine |
| `w` | Who is logged in and what they are running |
| `last \| head -10` | Last 10 login and logout events |
| `sudo cat /var/log/auth.log \| tail -20` | Last 20 authentication log entries |
| `sudo tail -f /var/log/auth.log` | Watch auth log update live |
| `ls /var/log` | List all log files on the system |
| `Ctrl+C` | Stop a running command |

---

## Reading Log Timestamps

Every log entry starts with a timestamp:

```
Jun 12 10:45:03
```

| Part | Value | Meaning |
|------|-------|---------|
| Month | `Jun` | June |
| Day | `12` | 12th |
| Time | `10:45:03` | 10:45 AM and 3 seconds (24-hour format) |

Linux logs use 24-hour time: `00:00` = midnight, `12:00` = noon, `23:59` = one minute before midnight.

---

## Troubleshooting

### "Permission denied" reading auth.log
Add `sudo` before the command:
```bash
sudo cat /var/log/auth.log | tail -20
```

### auth.log does not exist on your system
Your distro may use a different filename. Try:
```bash
sudo cat /var/log/syslog | tail -20
sudo cat /var/log/messages | tail -20
```

### top looks overwhelming
Press `q` to exit, then use `ps aux` for a cleaner one-time snapshot.

### tail -f not showing new entries
Make sure you ran a sudo command in the second terminal *after* starting `tail -f`, not before.

---

## Certification Mapping

### CompTIA Security+ (SY0-701)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 4 — Identity & Access Management | Reviewing authentication logs and login history |
| Domain 4 — Identity & Access Management | Identifying suspicious access patterns in logs |
| Domain 4 — Identity & Access Management | Understanding session-based log entries |
| Domain 2 — Threats, Vulnerabilities & Mitigations | Using logs to detect unauthorized access attempts |
| Domain 2 — Threats, Vulnerabilities & Mitigations | Real-time monitoring for suspicious activity |

### CompTIA Network+ (N10-009)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 3 — Network Operations | System monitoring tools and techniques |
| Domain 3 — Network Operations | Log review as part of normal operations |

---

## Next Steps

- **Filter logs by username** — search auth.log for a specific user:
```bash
sudo cat /var/log/auth.log | grep "alice"
```

- **Search for failed logins** — find authentication failures specifically:
```bash
sudo cat /var/log/auth.log | grep "failure"
```

- **Check disk and memory** — combine with today's skills:
```bash
df -h
free -h
```

- **Build a monitoring script** — combine `ps`, `who`, `last`, and `tail` into a single bash script that generates a full system report automatically

- **Learn journalctl** — a more powerful log reader on systemd-based systems:
```bash
journalctl -n 20
journalctl -f
```

---

*Lab completed as part of CompTIA Network+ (N10-009) and Security+ (SY0-701) home lab study — June 2026*
