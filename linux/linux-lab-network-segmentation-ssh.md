# 🐧 Linux Lab: Network Segmentation & SSH Remote Access

> **Lab Type:** Beginner — Guided  
> **OS:** Zorin OS (Linux)  
> **Hardware:** NETGEAR Nighthawk Router  
> **Completed:** June 2026  
> **Cert Relevance:** CompTIA Network+ (N10-009) · CompTIA Security+ (SY0-701)

---

## 📋 Table of Contents

1. [Lab Overview](#lab-overview)
2. [Prerequisites](#prerequisites)
3. [Key Concepts](#key-concepts)
4. [Step 1 — Configure the NETGEAR Nighthawk Router](#step-1--configure-the-netgear-nighthawk-router)
5. [Step 2 — Install Zorin OS](#step-2--install-zorin-os)
6. [Step 3 — Install and Configure OpenSSH Server](#step-3--install-and-configure-openssh-server)
7. [Step 4 — Verify Network Connectivity](#step-4--verify-network-connectivity)
8. [Step 5 — Establish SSH Remote Access](#step-5--establish-ssh-remote-access)
9. [Step 6 — Troubleshooting](#step-6--troubleshooting)
10. [Command Reference](#command-reference)
11. [Key Takeaways](#key-takeaways)
12. [Certification Mapping](#certification-mapping)
13. [Next Steps](#next-steps)

---

## Lab Overview

In this lab a fully isolated Linux lab network was built using a NETGEAR Nighthawk router to create a dedicated subnet separated from the home network. Zorin OS was installed and configured as the Linux machine, OpenSSH Server was set up for remote access, and SSH administration was successfully established from another device on the network.

This lab simulates how enterprise environments segment networks and manage Linux servers remotely — a core real-world skill for sysadmins, network engineers, and security professionals.

**What was accomplished:**
- Configured a segmented network using a NETGEAR Nighthawk router
- Created a dedicated Linux subnet separated from the home network
- Installed and configured Zorin OS and OpenSSH Server
- Verified routing, DHCP, internet connectivity, and DNS functionality
- Successfully established remote SSH administration from another device
- Troubleshot SSH authentication, routing, and connectivity issues using Linux logs and networking tools

---

## Prerequisites

- A NETGEAR Nighthawk router (or similar)
- A machine to install Linux on (desktop, laptop, or VM)
- Zorin OS ISO downloaded from zorin.com
- A second device to SSH from (laptop, another desktop)
- Basic familiarity with a Linux terminal

---

## Key Concepts

### Network Segmentation
The practice of dividing a network into separate subnets. In this lab the Linux machine lives on a dedicated subnet isolated from the main home network. This limits the blast radius if something goes wrong and mirrors how enterprise environments separate servers from end-user devices.

### SSH (Secure Shell)
A protocol that lets you remotely access and manage a Linux machine securely over a network. All traffic is encrypted. SSH is the standard method for remote Linux administration in enterprise environments.

### DHCP
Dynamic Host Configuration Protocol — automatically assigns IP addresses to devices on the network. The Nighthawk router acts as the DHCP server for the Linux subnet, handing out IP addresses to connected devices automatically.

### NAT (Network Address Translation)
Allows devices on a private subnet to reach the internet through a single public IP. The Nighthawk handles NAT so the Linux machine can reach the internet even though it's on an isolated private subnet.

### OpenSSH Server
The software package that runs on the Linux machine and listens for incoming SSH connections. Without it installed and running, no remote access is possible.

---

## Step 1 — Configure the NETGEAR Nighthawk Router

Log into the Nighthawk admin panel (typically at `192.168.1.1` or `192.168.0.1`) and configure a dedicated subnet for the Linux lab:

- Set a new LAN IP range for the lab network (separate from your home router's range)
- Enable DHCP on the lab subnet so the Linux machine gets an IP automatically
- Verify NAT is enabled so the lab subnet can reach the internet
- Connect the Linux machine to the Nighthawk via ethernet or WiFi

**Key goal:** The Linux machine should be on a different subnet than your main home network devices — completely isolated.

---

## Step 2 — Install Zorin OS

1. Download the Zorin OS ISO from zorin.com
2. Flash it to a USB drive using Balena Etcher or Rufus
3. Boot the target machine from the USB
4. Follow the installation wizard — choose your timezone, create a user account, set a password
5. Complete the install and reboot into Zorin OS

---

## Step 3 — Install and Configure OpenSSH Server

Once booted into Zorin OS, open a terminal and install OpenSSH:

```bash
sudo apt update
sudo apt install openssh-server -y
```

**Start the SSH service:**
```bash
sudo systemctl start ssh
```

**Enable it to start automatically on boot:**
```bash
sudo systemctl enable ssh
```

**Verify it is running:**
```bash
sudo systemctl status ssh
```

You should see:
```
● ssh.service - OpenBSD Secure Shell server
   Active: active (running)
```

**Find the machine's IP address** so you can SSH into it from another device:
```bash
ip a
```

Look for the IP address on your ethernet or WiFi interface — something like `192.168.x.x`. This is what you'll use to connect from the other device.

---

## Step 4 — Verify Network Connectivity

Before attempting SSH, confirm the network is set up correctly.

**Check your IP address and interface:**
```bash
ip a
```

**Check your default route (gateway):**
```bash
ip route
```

You should see a default route pointing to the Nighthawk router's IP.

**Test internet connectivity:**
```bash
ping 8.8.8.8
```

**Test DNS resolution:**
```bash
ping google.com
```

If both pings work, routing, NAT, DHCP, and DNS are all functioning correctly.

---

## Step 5 — Establish SSH Remote Access

From a **second device** on the same network, open a terminal and connect:

```bash
ssh username@192.168.x.x
```

Replace `username` with your Zorin OS username and `192.168.x.x` with the IP address found in Step 3.

On first connection you will see:
```
The authenticity of host '192.168.x.x' can't be established.
Are you sure you want to continue connecting (yes/no)?
```

Type `yes` and press Enter. Enter your password when prompted.

**Verify you are logged in remotely:**
```bash
whoami
```

This should return your username — confirming you are now administering the Linux machine remotely over SSH.

---

## Step 6 — Troubleshooting

### SSH connection refused
The SSH service may not be running. On the Linux machine run:
```bash
sudo systemctl start ssh
sudo systemctl status ssh
```

### SSH times out / can't reach the host
Check that both devices are on the same subnet. Run `ip a` on both and compare the IP addresses — the first three octets should match.

### Wrong username error
Verify your exact username:
```bash
whoami
```
Use that exact username in your SSH command.

### Permission denied (password)
Make sure the password is correct. Reset it if needed:
```bash
passwd
```

### Checking logs for SSH errors
```bash
sudo journalctl -u ssh
```
This shows the full SSH service log — useful for diagnosing exactly why a connection is failing.

---

## Command Reference

| Command | What It Does |
|---------|-------------|
| `ip a` | Show all network interfaces and IP addresses |
| `ip route` | Show the routing table and default gateway |
| `ping 8.8.8.8` | Test internet connectivity |
| `ping google.com` | Test DNS resolution |
| `ssh user@ip` | Connect to a remote Linux machine via SSH |
| `whoami` | Display the current logged-in username |
| `passwd` | Change the current user's password |
| `sudo apt install openssh-server` | Install the OpenSSH server package |
| `sudo systemctl start ssh` | Start the SSH service |
| `sudo systemctl enable ssh` | Enable SSH to start on boot |
| `sudo systemctl status ssh` | Check if SSH is running |
| `sudo journalctl -u ssh` | View SSH service logs for troubleshooting |

---

## Key Takeaways

**Structured troubleshooting is everything.** Every issue in this lab had to be isolated step by step — DHCP first, then routing, then SSH authentication, then username validation. Jumping straight to the SSH problem without verifying the network layer first would have wasted a lot of time.

**The troubleshooting order that worked:**
```
1. Is the machine getting an IP?     → ip a
2. Is routing working?               → ip route + ping gateway
3. Is internet reachable?            → ping 8.8.8.8
4. Is DNS working?                   → ping google.com
5. Is SSH running on the server?     → systemctl status ssh
6. Can the client reach the server?  → ping the server IP
7. Can SSH connect?                  → ssh user@ip
8. Are credentials correct?          → whoami + passwd
```

This bottom-up troubleshooting approach — physical → network → transport → application — is the OSI model in action and directly mirrors how Network+ troubleshooting questions are structured.

---

## Certification Mapping

### CompTIA Network+ (N10-009)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 1 — Networking Concepts | Subnetting, DHCP, NAT, DNS, routing |
| Domain 2 — Network Implementation | Network segmentation and router configuration |
| Domain 3 — Network Operations | Remote access via SSH, system management |
| Domain 5 — Network Troubleshooting | Structured troubleshooting methodology, connectivity verification |

### CompTIA Security+ (SY0-701)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 2 — Threats, Vulnerabilities & Mitigations | Network segmentation as a security control |
| Domain 3 — Security Architecture | Isolating systems using dedicated subnets |
| Domain 4 — Security Operations | SSH as a secure remote access protocol |
| Domain 4 — Security Operations | Log analysis with journalctl for SSH troubleshooting |

---

## Next Steps

- **Harden SSH** — disable password authentication and switch to SSH key-based authentication only
- **Configure UFW firewall** — allow only port 22 and block everything else
- **Set up Docker** — run containerized services on the Linux lab machine
- **Add Active Directory integration** — join the Linux machine to a Windows domain
- **Deploy Security Onion** — forward logs from the lab into a SIEM for blue team monitoring
- **Virtualization** — run additional Linux VMs on the lab machine for more complex topologies

---

*Lab completed as part of CompTIA Network+ (N10-009) and Security+ (SY0-701) home lab study — June 2026*
