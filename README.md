# 🖥️ Home Lab Notes — Jaelan

> **Focus:** Network+ (N10-009) · Security+ (SY0-701) · IAM Career Transition  
> **Role:** Service Desk Analyst → Identity & Access Management  
> **Lab Environment:** Cisco physical hardware + Linux desktop  
> **Started:** June 2026

---

## 👋 About This Repo

This repository documents hands-on home lab work completed as part of my CompTIA Network+ and Security+ certification journey and career transition into Identity & Access Management (IAM).

Every lab here was done on real hardware and a real Linux desktop — not simulations. Each write-up includes the exact commands used, what the output looked like, what went wrong, and how it was fixed.

---

## 🗂️ Lab Index

---

### 🔧 Networking

> Hands-on labs using real Cisco hardware — switches, routers, and console access.

| Lab | Device | Topic | Level | Cert Relevance |
|-----|--------|-------|-------|---------------|
| [Catalyst 2960-X Factory Reset](./networking/cisco-lab-2960x-factory-reset.md) | WS-C2960X-48FPS-L | Bootloader, flash, password recovery | Beginner | Network+ Domain 3 & 5 |

**Coming soon:**
- R1 & R2 Interface IP Configuration
- Serial WAN Link (10.0.0.0/30)
- Static Routing Between Routers
- VLAN Configuration and Trunking
- End-to-End Connectivity Testing

---

### 🐧 Linux

> Hands-on labs on a Linux desktop covering sysadmin fundamentals, bash scripting, and log analysis.

| Lab | Topic | Level | Cert Relevance |
|-----|-------|-------|---------------|
| [User & Permission Management](./linux/linux-lab-user-permission-management.md) | useradd, groups, chmod, chown | Beginner | Security+ Domain 4 |
| [System Monitoring & Logs](./linux/linux-lab-system-monitoring-logs.md) | top, ps, auth.log, tail -f | Beginner | Security+ Domain 2 & 4 |
| [Bash Scripting — System Health Report](./linux/linux-lab-bash-system-health-report.md) | Bash scripting, date, df, uptime | Beginner | Network+ Domain 3 |

**Coming soon:**
- Bash Scripting — User Account Audit Script
- Firewall Rules with UFW
- SSH Key Setup and Hardening
- Networking & Connectivity

---

## 🗺️ Repo Structure

```
home-lab-notes/
├── README.md
├── networking/
│   └── cisco-lab-2960x-factory-reset.md
└── linux/
    ├── linux-lab-user-permission-management.md
    ├── linux-lab-system-monitoring-logs.md
    └── linux-lab-bash-system-health-report.md
```

---

## 🏠 Lab Environment

### Physical Hardware

| Device | Model | Role |
|--------|-------|------|
| Switch 1 | Cisco Catalyst 3560 (SW1-3560) | Layer 3 Core |
| Switch 2 | Cisco Catalyst 2960-X (SW2-2960X) | Access Layer |
| Switch 3 | Cisco Catalyst 2960-X (SW3-2960X) | Access Layer |
| Switch 4 | Cisco Catalyst 2960-X (SW4-2960X) | Access Layer |
| Router 1 | Cisco 2600 Series (R1) | Gateway / Home Network Connection |
| Router 2 | Cisco 2600 Series (R2) | WAN Link (Serial) |

### Software & Tools

| Tool | Purpose |
|------|---------|
| PuTTY | Console access to Cisco devices |
| Linux Desktop (Zorin OS) | Linux lab environment |
| VSCode | Script writing and editing |

---

## 🎯 Certification Targets

### CompTIA Network+ (N10-009)
| Domain | Weight |
|--------|--------|
| Domain 1 — Networking Concepts | 23% |
| Domain 2 — Network Implementation | 20% |
| Domain 3 — Network Operations | 19% |
| Domain 4 — Network Security | 14% |
| Domain 5 — Network Troubleshooting | 24% |

### CompTIA Security+ (SY0-701)
| Domain | Weight |
|--------|--------|
| Domain 1 — General Security Concepts | 12% |
| Domain 2 — Threats, Vulnerabilities & Mitigations | 22% |
| Domain 3 — Security Architecture | 18% |
| Domain 4 — Security Operations | 28% |
| Domain 5 — Security Program Management | 20% |

---

## 📈 Skills Being Built

**Linux & Sysadmin**
- User and group management
- File system permissions and access control
- Log analysis and real-time monitoring
- Bash scripting and automation

**Cisco Networking**
- Physical device recovery and console access
- IOS configuration and device hardening
- VLAN configuration and trunking
- Layer 2/3 switching and routing concepts

**Security & IAM**
- Least privilege access control
- Authentication log analysis
- Identity lifecycle management
- Physical security of network devices

---

*Actively updated as new labs are completed.*
