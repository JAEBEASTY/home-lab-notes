# 🔧 Cisco Lab: Catalyst 2960-X Full Factory Reset

> **Lab Type:** Beginner — Guided  
> **Device:** Cisco Catalyst 2960-X Series (WS-C2960X-48FPS-L)  
> **IOS Version:** 15.2  
> **Completed:** June 2026  
> **Cert Relevance:** CompTIA Network+ (N10-009) · CompTIA Security+ (SY0-701)

---

## 📋 Table of Contents

1. [Lab Overview](#lab-overview)
2. [Equipment Used](#equipment-used)
3. [Key Concepts](#key-concepts)
4. [Step 1 — Physical Connection](#step-1--physical-connection)
5. [Step 2 — Configure PuTTY](#step-2--configure-putty)
6. [Step 3 — Enter the Bootloader](#step-3--enter-the-bootloader)
7. [Step 4 — Initialize the Flash](#step-4--initialize-the-flash)
8. [Step 5 — List Files on Flash](#step-5--list-files-on-flash)
9. [Step 6 — Delete the Config Files](#step-6--delete-the-config-files)
10. [Step 7 — Boot the Switch](#step-7--boot-the-switch)
11. [Step 8 — Verify the Reset](#step-8--verify-the-reset)
12. [Step 9 — Save the Clean State](#step-9--save-the-clean-state)
13. [Quick Reference — All Commands](#quick-reference--all-commands)
14. [Troubleshooting](#troubleshooting)
15. [Important Security Note](#important-security-note)
16. [Certification Mapping](#certification-mapping)
17. [Next Steps](#next-steps)

---

## Lab Overview

In this lab a full factory reset is performed on a Cisco Catalyst 2960-X series switch. The switch had an existing configuration with an unknown password, so a physical password recovery was required before wiping the device.

This process covers how to interrupt the boot sequence, access the switch bootloader, delete all configuration files from flash memory, and verify a clean reset — exactly what you would do in a real job when inheriting a locked or unknown switch.

**What was accomplished:**
- Connected to the switch via console cable and PuTTY
- Used the MODE button to enter the switch bootloader
- Initialized the flash file system with `flash_init`
- Listed and identified all config files on flash
- Deleted `config.text`, `private-config.text`, `vlan.dat`, and renamed backups
- Booted the switch to a clean default state
- Verified the reset with `show running-config`
- Saved the clean config to startup-config

---

## Equipment Used

| Item | Details |
|------|---------|
| Switch | Cisco Catalyst 2960-X (WS-C2960X-48FPS-L) |
| IOS | Version 15.2 |
| Cable | Rollover console cable (RJ45 to DB9/USB Serial) |
| Adapter | USB to Serial adapter |
| Terminal | PuTTY on Windows |
| Connection | Serial — COM3, 9600 baud |

---

## Key Concepts

### Bootloader
The bootloader is a low-level environment that runs before the IOS operating system loads. It gives you direct access to the switch's flash file system. On Cisco switches it is accessed by interrupting the boot sequence using the MODE button. The prompt looks like:
```
switch:
```

### Flash Memory
Flash is the switch's internal storage. It holds the IOS image and all configuration files. When resetting a switch you delete the config files from flash — **not the IOS image**. The switch needs IOS to operate.

### Key Config Files

| File | What It Stores |
|------|---------------|
| `config.text` | The main saved configuration |
| `private-config.text` | Encrypted passwords |
| `vlan.dat` | The VLAN database |

### Console Port
A dedicated management port on Cisco devices that gives you direct access regardless of network configuration or passwords. Always accessed via a rollover cable. This is your lifeline when a device is locked or misconfigured.

### Baud Rate
The speed of the console connection. Cisco devices always use **9600 baud** by default. This never changes on Cisco gear.

---

## Step 1 — Physical Connection

1. Plug the **RJ45 end** of the rollover cable into the **CONSOLE** port on the switch
2. Plug the other end into your **USB to Serial adapter**, then into your laptop's USB port
3. Open **Device Manager** on Windows:
   - Right click Start → Device Manager
   - Expand **Ports (COM & LPT)**
   - Look for **USB Serial Port (COM3)**
   - Note your COM number — you'll need it in PuTTY

> Your COM number may differ — COM3, COM4, and COM5 are all common. Use whatever Device Manager shows you.

---

## Step 2 — Configure PuTTY

Open PuTTY and set it up exactly as follows:

```
Connection type:  Serial
Serial line:      COM3     ← use YOUR COM number
Speed:            9600
```

Click **Open**. A black terminal window will appear. Press **Enter** once or twice to get a response from the switch.

**Full serial settings for reference:**

| Setting | Value |
|---------|-------|
| Data bits | 8 |
| Stop bits | 1 |
| Parity | None |
| Flow control | None |
| Speed | 9600 |

---

## Step 3 — Enter the Bootloader

The switch had a password set so normal access was blocked. To bypass this, the physical MODE button is used to interrupt the boot process.

**Procedure:**

1. Locate the **MODE button** on the front panel — left side near the ports
2. **Unplug the power cable** from the switch
3. **Hold down the MODE button** (use a pen tip if needed)
4. While still holding MODE, **plug the power cable back in**
5. Keep holding and watch the PuTTY screen
6. When you see the message below, **release the button**:

```
The system has been interrupted prior to initializing the flash file system
```

You will land at the bootloader prompt:
```
switch:
```

> If you release too early the switch boots normally into IOS and asks for the password. Unplug and repeat if that happens.

---

## Step 4 — Initialize the Flash

At the `switch:` prompt run:

```
flash_init
```

This mounts the flash file system so you can read and delete files. Wait for it to complete and return you to `switch:` before moving on.

---

## Step 5 — List Files on Flash

```
dir flash:
```

**What was on this switch:**

```
Directory of flash:/
    2  -rwx  557        express_setup.debug
    3  -rwx  34         pnp-tech-time
    4  -rwx  11179      pnp-tech-discovery-summary
    5  -rwx  1156       vlan.dat.renamed
    6  -rwx  1396       vlan.dat
    7  drwx  512        c2960x-universalk9-mz.152-7.E7
  498  drwx  512        dc_profile_dir
  500  -rwx  34444      config.text.renamed
  501  -rwx  3580       private-config.text.renamed
  502  -rwx  6168       multiple-fs
  503  -rwx  34866      config.text
  504  -rwx  3577       private-config.text
```

**Files to delete:**

| File | Reason |
|------|--------|
| `config.text` | Main saved configuration |
| `private-config.text` | Encrypted passwords |
| `vlan.dat` | VLAN database |
| `config.text.renamed` | Leftover from previous recovery attempt |
| `private-config.text.renamed` | Leftover from previous recovery attempt |
| `vlan.dat.renamed` | Leftover from previous recovery attempt |

**File to keep:**

| File | Reason |
|------|--------|
| `c2960x-universalk9-mz.152-7.E7` | The IOS image — **NEVER delete this** |

---

## Step 6 — Delete the Config Files

Run each command one at a time. Press **Enter** to confirm each deletion when prompted.

```
del flash:config.text
```
```
del flash:private-config.text
```
```
del flash:vlan.dat
```
```
del flash:config.text.renamed
```
```
del flash:private-config.text.renamed
```
```
del flash:vlan.dat.renamed
```

After each command the switch asks:
```
Delete filename [config.text]?
```
Press **Enter** to confirm — do not type Y, just press Enter.

> The `.renamed` files are backups created during a previous password recovery attempt. Always delete them too so no old config is left behind.

---

## Step 7 — Boot the Switch

```
boot
```

The switch takes **2-3 minutes** to fully boot. You will see a lot of text scrolling in PuTTY — this is normal.

When it finishes:
```
Would you like to enter the initial configuration dialog? [yes/no]:
```

Type `no` and press **Enter**.

You will land at:
```
Switch>
```

---

## Step 8 — Verify the Reset

Confirm no password is set:
```
enable
```

You should reach `Switch#` immediately with no password prompt. If it asks for a password, a config file was not fully deleted — re-enter the bootloader and repeat Step 6.

View the running configuration:
```
show running-config
```

**A clean reset shows:**
- `hostname Switch` — default, not customized
- No enable password
- No console password
- No VTY passwords
- All interfaces empty with no configuration
- `Vlan1` with no IP address
- No VLANs, no ACLs, no routing configured

---

## Step 9 — Save the Clean State

Save the blank config so it survives a reboot:

```
copy running-config startup-config
```

Press **Enter** when asked:
```
Destination filename [startup-config]?
```

You will see:
```
Building configuration...
[OK]
```

The switch is fully reset and the clean state is saved.

---

## Quick Reference — All Commands

### Bootloader Commands (at `switch:` prompt)

| Command | What It Does |
|---------|-------------|
| `flash_init` | Mount the flash file system |
| `dir flash:` | List all files on flash |
| `del flash:config.text` | Delete the main configuration |
| `del flash:private-config.text` | Delete the encrypted password file |
| `del flash:vlan.dat` | Delete the VLAN database |
| `boot` | Boot into IOS |

### IOS Commands (after booting)

| Command | What It Does |
|---------|-------------|
| `enable` | Enter privileged mode |
| `show running-config` | View the current configuration |
| `copy running-config startup-config` | Save the config |

---

## Troubleshooting

### Switch boots into IOS instead of the bootloader
You released the MODE button too early. Unplug power and repeat Step 3 — hold MODE until you see the flash interrupt message.

### PuTTY shows a blank screen
Press Enter two or three times. If still nothing, check your COM port number in Device Manager and verify PuTTY speed is set to 9600.

### Delete command does nothing after pressing Enter
It is waiting for confirmation — just press **Enter**. Do not type Y.

### Switch still asks for a password after reset
One of the config files was not deleted. Re-enter the bootloader, run `dir flash:` and check for any remaining `config.text` or `private-config.text` files, then delete them.

### Cannot find the MODE button
It is on the front panel on the left side near port 1 — a small recessed button. Use a pen tip to press it.

---

## Important Security Note

The MODE button password recovery process works because you have **physical access** to the switch. This is exactly why physical security of network equipment is critical.

Anyone with physical access to a Cisco switch can perform this reset — no password required.

**In a real environment always:**
- Lock network equipment in a secured cabinet or server room
- Restrict physical access to authorized personnel only
- Log all physical access to network equipment
- Consider running `no service password-recovery` in IOS for high-security environments to disable this process

> This is a Security+ exam topic — physical security of network devices falls directly under access control and is tested on SY0-701.

---

## Certification Mapping

### CompTIA Network+ (N10-009)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 3 — Network Operations | Managing and configuring network switches |
| Domain 3 — Network Operations | Console access and out-of-band management |
| Domain 3 — Network Operations | Understanding flash memory and IOS on Cisco devices |
| Domain 5 — Network Troubleshooting | Recovering access to a locked network device |
| Domain 5 — Network Troubleshooting | Using console connections for device management |

### CompTIA Security+ (SY0-701)

| Exam Domain | Topic Covered |
|-------------|--------------|
| Domain 4 — Identity & Access Management | Physical access controls on network devices |
| Domain 4 — Identity & Access Management | Securing console ports |
| Domain 4 — Identity & Access Management | Password recovery as a physical attack vector |

---

## Next Steps

Now that the switch is clean and ready:

- **Set a hostname:**
```
hostname SW1
```

- **Set an enable secret password:**
```
enable secret YourPassword
```

- **Secure the console port:**
```
line con 0
password YourPassword
login
```

- **Configure VLANs** for your lab topology
- **Connect to other lab devices** and build out your full home lab network
- **Practice backing up configs:**
```
copy startup-config flash:backup-config
```

---

*Lab completed as part of CompTIA Network+ (N10-009) and Security+ (SY0-701) home lab study — June 2026*
