# 🐧 Linux Lab: User & Permission Management

> **Lab Type:** Beginner — Guided  
> **OS:** Linux (Ubuntu / Debian-based desktop)  
> **Completed:** June 2026  
> **Cert Relevance:** CompTIA Security+ (SY0-701) · CompTIA Network+ (N10-009)

---

## 📋 Table of Contents

1. [Lab Overview](#lab-overview)
2. [Prerequisites](#prerequisites)
3. [Key Concepts](#key-concepts)
4. [Step 1 — Create User Accounts](#step-1--create-user-accounts)
5. [Step 2 — Set Passwords](#step-2--set-passwords)
6. [Step 3 — Create a Group & Add Users](#step-3--create-a-group--add-users)
7. [Step 4 — Create a Shared Directory](#step-4--create-a-shared-directory)
8. [Step 5 — Set Ownership & Permissions](#step-5--set-ownership--permissions)
9. [Step 6 — Test Access as a User](#step-6--test-access-as-a-user)
10. [Audit Commands — How to Check Everything](#audit-commands--how-to-check-everything)
11. [Understanding chmod Notation](#understanding-chmod-notation)
12. [Understanding getent Output](#understanding-getent-output)
13. [Troubleshooting](#troubleshooting)
14. [Certification Mapping](#certification-mapping)
15. [Next Steps](#next-steps)

---

## Lab Overview

In this lab, you take on the role of a junior sysadmin setting up accounts for two new employees at a company. You will:

- Create two Linux user accounts (`alice` and `bob`)
- Set secure passwords for each
- Create a department group (`staffteam`) and add both users to it
- Build a shared directory that **only** members of `staffteam` can access
- Lock it down using Linux file permissions
- Verify and audit everything using built-in Linux tools
- Test the setup by logging in as a real user

This lab directly mirrors how access control works in real enterprise Linux environments and maps to **least privilege** principles tested on the Security+ exam.

---

## Prerequisites

- A Linux desktop (Ubuntu, Debian, Linux Mint, or similar)
- A terminal open and ready
- `sudo` access (admin privileges)
- No additional software required — all commands are built into Linux

> **What is sudo?**  
> `sudo` stands for "superuser do." It temporarily elevates your privileges to run a command as the system administrator. Without it, Linux blocks changes to system-level files and accounts.

---

## Key Concepts

### Users
Every person (or service) on a Linux system has a **user account**. Each account has:
- A **username** (e.g. `alice`)
- A **User ID (UID)** — a unique number Linux uses internally
- A **home directory** (e.g. `/home/alice`) — their personal space

### Groups
A **group** is a collection of users. Instead of setting permissions individually for each person, you set them once for the group. Every member inherits those permissions automatically.

### File Permissions
Linux controls who can read, write, or execute any file or directory using a 3-part permission system:
- **Owner** — the person who created the file
- **Group** — a group of users with shared permissions
- **Others** — everyone else on the system

### Least Privilege
A core security principle: give users only the access they need to do their job — nothing more. This limits damage if an account is compromised.

---

## Step 1 — Create User Accounts

Create two new user accounts: `alice` and `bob`.

```bash
sudo useradd -m alice
sudo useradd -m bob
```

**Flag breakdown:**

| Flag | Meaning |
|------|---------|
| `sudo` | Run as administrator |
| `useradd` | Command to create a new user |
| `-m` | Create a home directory for the user (e.g. `/home/alice`) |
| `alice` | The username being created |

> Without `-m`, the user is created but has no home directory — they would have nowhere to store personal files or configs.

### Verify the users were created

```bash
getent passwd alice
getent passwd bob
```

**Expected output:**
```
alice:x:1001:1001::/home/alice:/bin/sh
bob:x:1002:1002::/home/bob:/bin/sh
```

See [Understanding getent Output](#understanding-getent-output) below for a full breakdown of what each field means.

---

## Step 2 — Set Passwords

The accounts exist but have no password yet. Set one for each:

```bash
sudo passwd alice
```

Linux will prompt:
```
New password:
Retype new password:
passwd: password updated successfully
```

Then do the same for bob:

```bash
sudo passwd bob
```

> **Important:** You will not see any characters appear as you type the password. This is intentional — Linux hides password input completely, not even showing asterisks. The keystrokes are still registering.

**Expected confirmation after each:**
```
passwd: password updated successfully
```

---

## Step 3 — Create a Group & Add Users

### Create the group

```bash
sudo groupadd staffteam
```

This creates a new group called `staffteam`. Linux automatically assigns it a **Group ID (GID)**.

### Add alice to the group

```bash
sudo usermod -aG staffteam alice
```

### Add bob to the group

```bash
sudo usermod -aG staffteam bob
```

**Flag breakdown:**

| Flag | Meaning |
|------|---------|
| `usermod` | Modify an existing user account |
| `-a` | **Append** — add to the group without removing existing group memberships |
| `-G` | Specify the group(s) to add the user to |

> **Critical:** The `-a` flag is essential. If you run `usermod -G staffteam alice` without `-a`, Linux replaces all of alice's current group memberships with just `staffteam`. Combined, `-aG` safely adds her to the new group while keeping everything else intact.

### Verify both users are in the group

```bash
getent group staffteam
```

**Expected output:**
```
staffteam:x:1003:alice,bob
```

The number (`1003`) is the GID Linux assigned — it may differ on your system depending on what groups already exist. What matters is seeing `alice,bob` at the end.

---

## Step 4 — Create a Shared Directory

Create the folder structure for the shared team directory:

```bash
sudo mkdir -p /shared/staffteam
```

**Flag breakdown:**

| Flag | Meaning |
|------|---------|
| `mkdir` | Make directory |
| `-p` | Create **parent** directories too if they don't exist |
| `/shared/staffteam` | The full path being created |

> **Why `-p`?**  
> `/shared` doesn't exist by default. Without `-p`, Linux throws an error: `cannot create directory '/shared/staffteam': No such file or directory`. The `-p` flag tells Linux to create the entire path in one shot.

### Verify the directory was created

```bash
ls -ld /shared/staffteam
```

At this point you'll see something like:
```
drwxr-xr-x 2 root root 4096 Jun 12 18:46 /shared/staffteam
```

The group is still `root` — we fix that in the next step.

---

## Step 5 — Set Ownership & Permissions

### Assign staffteam as the group owner

```bash
sudo chown :staffteam /shared/staffteam
```

**Breakdown:**
- `chown` = change ownership
- `:staffteam` = the colon means we're setting the **group** owner (not the user owner)
- Leaving the user side blank keeps `root` as the owner

### Set permissions so only staffteam members can access it

```bash
sudo chmod 770 /shared/staffteam
```

See [Understanding chmod Notation](#understanding-chmod-notation) for the full breakdown of what `770` means.

### Verify the final setup

```bash
ls -ld /shared/staffteam
```

**Expected output:**
```
drwxrwx--- 2 root staffteam 4096 Jun 12 18:46 /shared/staffteam
```

Reading that output left to right:

| Part | Meaning |
|------|---------|
| `d` | It's a directory |
| `rwx` | Owner (root) has read, write, execute |
| `rwx` | Group (staffteam) has read, write, execute |
| `---` | Everyone else has no access at all |
| `root` | The user owner |
| `staffteam` | The group owner |

---

## Step 6 — Test Access as a User

The real test — log in as alice and confirm she can use the shared folder.

### Switch to alice's account

```bash
su - alice
```

Enter alice's password when prompted. Your terminal prompt will change to show you're now operating as alice.

### Create a test file in the shared directory

```bash
touch /shared/staffteam/alicefile.txt
```

`touch` creates an empty file. If alice has proper access, this will succeed silently.

### Confirm the file exists

```bash
ls /shared/staffteam
```

**Expected output:**
```
alicefile.txt
```

### Return to your original user

```bash
exit
```

**What this test proves:**  
Alice (a member of `staffteam`) can read and write to the directory. If you repeated this test with a user who is NOT in `staffteam`, the `touch` command would fail with `Permission denied` — exactly the behavior least privilege is designed to produce.

---

## Audit Commands — How to Check Everything

These are the commands a sysadmin would run to audit user access and permissions. All are read-only — safe to run any time.

### Check who belongs to a group

```bash
getent group staffteam
```

### Check all groups a specific user belongs to

```bash
groups alice
groups bob
```

### Get full user identity info (UID, GID, all groups)

```bash
id alice
id bob
```

**Example output:**
```
uid=1001(alice) gid=1001(alice) groups=1001(alice),1003(staffteam)
```

### Check folder permissions

```bash
ls -ld /shared/staffteam
```

### Check all files inside a folder with permissions

```bash
ls -la /shared/staffteam
```

### Full audit — run all four at once

```bash
getent group staffteam
id alice
id bob
ls -ld /shared/staffteam
```

---

## Understanding chmod Notation

`chmod` uses a 3-digit **octal** number to represent permissions. Each digit controls one level of access:

```
  7    7    0
  |    |    |
Owner Group Others
```

Each digit is the **sum** of three permission values:

| Value | Permission | Symbol |
|-------|-----------|--------|
| 4 | Read | r |
| 2 | Write | w |
| 1 | Execute | x |
| 0 | No access | - |

**Common combinations:**

| Octal | Permissions | Meaning |
|-------|------------|---------|
| `7` | rwx | Full access |
| `6` | rw- | Read and write, no execute |
| `5` | r-x | Read and execute, no write |
| `4` | r-- | Read only |
| `0` | --- | No access at all |

**So `chmod 770` means:**
- `7` → Owner gets rwx (full access)
- `7` → Group gets rwx (full access)
- `0` → Everyone else gets nothing

**Other common permission sets:**

| chmod | Use case |
|-------|---------|
| `755` | Public directory — owner full, others can read/navigate |
| `700` | Private directory — only the owner can access |
| `644` | Standard file — owner can edit, others can read |
| `600` | Private file — only the owner can read or edit |
| `770` | Shared team directory — group has full access, others locked out |

---

## Understanding getent Output

When you run `getent passwd alice`, the output looks like:

```
alice:x:1001:1001::/home/alice:/bin/sh
```

Each field separated by `:` means:

| Field | Value | Meaning |
|-------|-------|---------|
| 1 | `alice` | Username |
| 2 | `x` | Password placeholder (stored securely in `/etc/shadow`) |
| 3 | `1001` | User ID (UID) — Linux's internal number for this user |
| 4 | `1001` | Primary Group ID (GID) — auto-created group for this user |
| 5 | *(blank)* | GECOS field — optional description/comment |
| 6 | `/home/alice` | Home directory |
| 7 | `/bin/sh` | Default shell |

---

## Troubleshooting

### "Cannot create directory" error
The parent directory doesn't exist. Use `-p`:
```bash
sudo mkdir -p /shared/staffteam
```

### "Permission denied" when running useradd/groupadd
You forgot `sudo`. All account management commands require admin privileges:
```bash
sudo useradd -m alice
```

### getent group staffteam shows users missing
You may have forgotten `-a` in `usermod`. Re-run with the correct flag:
```bash
sudo usermod -aG staffteam alice
```

### ls -ld still shows root as group owner
Run the `chown` command again — make sure the colon is there:
```bash
sudo chown :staffteam /shared/staffteam
```

### su - alice asks for a password you forgot
Reset it:
```bash
sudo passwd alice
```

---

## Certification Mapping

### CompTIA Security+ (SY0-701)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 4 — Identity & Access Management | User account creation and lifecycle |
| Domain 4 — Identity & Access Management | Group-based access control |
| Domain 4 — Identity & Access Management | Least privilege principle |
| Domain 4 — Identity & Access Management | File system permissions |

### CompTIA Network+ (N10-009)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 4 — Network Security | OS-level access control |
| Domain 4 — Network Security | User and group management on network-attached systems |

---

## Next Steps

Ready to go further? Here are natural follow-on labs:

- **Test denied access** — Create a third user not in `staffteam`, try to access `/shared/staffteam`, and observe the `Permission denied` error
- **Private files** — Create a file inside the shared folder with `chmod 600` so only alice can read it, even though bob is in the same group
- **Remove group access** — Use `gpasswd -d bob staffteam` to remove bob from the group and verify his access is immediately revoked
- **Delete a user** — Use `sudo userdel -r alice` and observe what happens to her home directory and files
- **sudo access** — Add a user to the `sudo` group and explore privilege escalation concepts
- **Password policies** — Use `chage` to set password expiration and aging rules

---

*Lab completed as part of CompTIA Network+ (N10-009) and Security+ (SY0-701) home lab study — June 2026*
