# Module 8: Virtualization & Home Lab Setup

## Phase 1 — Absolute Foundations 🟢 Difficulty: Beginner | ⏱ Estimated Time: 2–3 hours | 📁 Folder: 01_FOUNDATIONS

---

## Table of Contents
1. [What is Virtualization?](#1-what-is-virtualization)
2. [Minimum Hardware Requirements](#2-minimum-hardware-requirements)
3. [Install VirtualBox](#3-install-virtualbox)
4. [Download Kali Linux (Pre-built)](#4-download-kali-linux-pre-built)
5. [Import Kali Linux](#5-import-kali-linux)
6. [First Boot & Update Kali](#6-first-boot--update-kali)
7. [Download Metasploitable 2](#7-download-metasploitable-2)
8. [Import Metasploitable 2](#8-import-metasploitable-2)
9. [Download OWASP Broken Web Applications (BWA)](#9-download-owasp-broken-web-applications-bwa)
10. [Import OWASP BWA](#10-import-owasp-bwa)
11. [Configure Host-Only Network](#11-configure-host-only-network)
12. [Verify Connectivity](#12-verify-connectivity)
13. [Take Snapshots](#13-take-snapshots)
14. [Hands-On Lab](#14-hands-on-lab)
15. [Summary & Key Takeaways](#15-summary--key-takeaways)
16. [Quiz](#16-quiz)
17. [Resources](#17-resources)

---

## 1. What is Virtualization?
**Virtualization** is technology that lets you run multiple operating systems on a single physical computer. Each virtual machine (VM) acts like a real computer with its own CPU, RAM, hard drive, and network card — but it's just software running on top of your real hardware.

### Why this matters for cybersecurity:
Without virtualization, you would need separate physical computers to practice attacks. With virtualization, one computer runs all of them.

### The setup you will build:
```
Your Physical Computer (Windows/Linux/macOS)
└── VirtualBox (Hypervisor)
    ├── Kali Linux (Attacker VM)
    ├── Metasploitable 2 (Vulnerable Linux Target)
    ├── OWASP BWA (Vulnerable Web Application Target)
    └── (More target VMs will be added in later modules)
```

Each VM is isolated from your real computer. You can break, crash, or infect these VMs without any risk to your actual system.

---

## 2. Minimum Hardware Requirements

| Component | Minimum | Recommended | Why |
|-----------|---------|-------------|-----|
| RAM | 8 GB | 16 GB | Each VM needs 1-2 GB |
| Storage | 50 GB free | 100 GB+ SSD | VM files take space |
| CPU | 4 cores | 8 cores | VMs share your CPU |
| Virtualization | Enabled in BIOS | Enabled | Required for performance |

### Check if virtualization is enabled:
- **Windows**: Task Manager → Performance → CPU → Virtualization: Enabled
- **Linux**: Run `grep -E 'vmx|svm' /proc/cpuinfo` → If you see output, it's enabled
- **macOS**: Built-in, no check needed

⚠️ **Virtualization must be enabled** or VMs will run extremely slowly.

---

## 3. Install VirtualBox
VirtualBox is a free, open-source hypervisor that runs on Windows, Linux, and macOS.

### Step 1: Download VirtualBox
Go to: https://www.virtualbox.org/wiki/Downloads

Download:
- VirtualBox platform package for your operating system
- VirtualBox Extension Pack (same version)

### Step 2: Install VirtualBox

**Windows:**
- Run the installer
- Click Next through all defaults
- Click "Install"
- Click "Finish"

**Linux (Ubuntu/Debian/Kali):**
```bash
sudo apt update
sudo apt install virtualbox -y
```

**macOS:**
- Open the .dmg file
- Double-click VirtualBox.pkg
- Follow the installer

### Step 3: Install Extension Pack
- Double-click the downloaded Extension Pack file
- VirtualBox will open
- Click "Install"
- Scroll through the license and click "I Agree"

### Step 4: Launch VirtualBox
You should see the VirtualBox Manager window.

---

## 4. Download Kali Linux (Pre-built)
Why pre-built? Offensive Security provides ready-to-run Kali VM images. You don't need to install from ISO — just download, import, and start.

### Step 1: Download the OVA file
Go to: https://www.kali.org/get-kali/#kali-virtual-machines

Under "Virtual Machines", click:
**Kali Linux VirtualBox 64-bit** (approx 3.5 GB download)

### Step 2: Extract the downloaded file
The file downloads as a .7z archive:
- **Windows**: Use 7-Zip (free from 7-zip.org)
- **Linux**: Run `tar -xf kali-linux-*.tar.xz`
- **macOS**: Use The Unarchiver or Keka

After extraction, you will have a **.ova** file.

---

## 5. Import Kali Linux

### Step 1: Import the OVA
- Open VirtualBox
- Click **File → Import Appliance**
- Click the folder icon and select your .ova file
- Click **Next**

### Step 2: Review settings
Default settings are fine:
- RAM: 2048 MB (2 GB)
- CPU: 1 or 2 cores

Click **Import**.

### Step 3: Wait for import
This takes 5-10 minutes.

### Step 4: Verify Kali appears
You should see "Kali Linux" in the left panel of VirtualBox Manager.

---

## 6. First Boot & Update Kali

### Step 1: Start Kali
- Select Kali Linux in VirtualBox Manager
- Click the green **Start** arrow

### Step 2: Login
- **Username**: `kali`
- **Password**: `kali`

### Step 3: Change the default password
```bash
passwd
```
Type your new password twice.

⚠️ **Critical**: Kali comes with default credentials. Anyone on your network could access it. Change the password immediately.

### Step 4: Update Kali
```bash
sudo apt update
sudo apt full-upgrade -y
```
This updates all tools to the latest versions. It may take 10-20 minutes depending on your internet speed.

### Step 5: Check your IP address
```bash
ip a
```
Look for an IP address like `10.0.2.15` (NAT network). This is Kali's current network connection.

### Step 6: Shut down Kali for now
```bash
sudo shutdown now
```

---

## 7. Download Metasploitable 2
**What is Metasploitable 2?** A purposely vulnerable Linux virtual machine. Every service on it has known vulnerabilities. It is your safe, legal target for practicing attacks.

### Download:
Go to: https://sourceforge.net/projects/metasploitable/

Click **Download** (approx 800 MB). The file downloads as `metasploitable-linux-2.0.0.zip`.

### Extract the ZIP:
After extraction, you will have a file named **Metasploitable.vmdk** (virtual disk file). This is the VM's hard drive.

⚠️ **WARNING**: Never expose Metasploitable to the internet or your home network. It is intentionally vulnerable. Keep it isolated in Host-Only mode.

---

## 8. Import Metasploitable 2
Metasploitable comes as a disk file (.vmdk), not an OVA. You will create a new VM and attach the existing disk.

### Step 1: Create a new VM
- In VirtualBox, click **Machine → New**
- **Name**: Metasploitable 2
- **Folder**: Keep default or choose location with enough space
- **ISO Image**: Leave empty (we will attach disk manually)
- **Type**: Linux
- **Version**: Ubuntu (64-bit)
- Click **Next**

### Step 2: Allocate RAM
Set RAM to **512 MB** (Metasploitable needs very little). Click **Next**.

### Step 3: Attach existing hard disk
- Select **Use an existing virtual hard disk file**
- Click the folder icon next to the dropdown
- Click **Add**
- Browse to your `Metasploitable.vmdk` file
- Select it and click **Choose**
- Click **Next → Finish**

### Step 4: Start Metasploitable
Select Metasploitable 2 and click **Start**.

### Step 5: Login
When the boot finishes, you will see a login prompt.
- **Username**: `msfadmin`
- **Password**: `msfadmin`

### Step 6: Check IP address
```bash
ifconfig
```
You will see an IP address like `10.0.2.15` (NAT mode). Note this down.

### Step 7: Shut down Metasploitable
```bash
sudo shutdown now
```

---

## 9. Download OWASP Broken Web Applications (BWA)

**What is OWASP BWA?** The Open Web Application Security Project (OWASP) Broken Web Applications project provides a VM containing dozens of intentionally vulnerable web applications. It's a "target-rich environment" for practicing web application security testing.

The VM includes:
- **Damn Vulnerable Web Application (DVWA)**
- **WebGoat**
- **Mutillidae**
- **ZAP-WAVE**
- Vulnerable versions of WordPress and Joomla
- And many more!

⚠️ **WARNING**: This VM has many serious security issues. Run it ONLY on a Host-Only network and NEVER expose it to the internet.

### Download:
Go to: https://sourceforge.net/projects/owaspbwa/

Click **Download Latest Version** (approx 1.2 GB). The file downloads as `OWASP_Broken_Web_Apps_VM_1.2.7z`.

### Extract the downloaded file:
- **Windows**: Use 7-Zip to extract
- **Linux**: Install p7zip: `sudo apt install p7zip-full -y` then `7z x OWASP_Broken_Web_Apps_VM_1.2.7z`
- **macOS**: Use The Unarchiver or Keka

After extraction, you will see several files. The key file is **OWASP Broken Web Apps-cl1.vmdk**.

---

## 10. Import OWASP BWA

Like Metasploitable, OWASP BWA comes as a disk file (.vmdk). You will create a new VM and attach the existing disk.

### Step 1: Create a new VM
- In VirtualBox, click **Machine → New**
- **Name**: OWASP BWA
- **Folder**: Keep default or choose location with enough space
- **ISO Image**: Leave empty
- **Type**: Linux
- **Version**: Ubuntu (64-bit)
- Click **Next**

### Step 2: Allocate RAM
Set RAM to **1024 MB (1 GB)**. The default 512 MB works, but 1 GB provides better performance.

### Step 3: Attach existing hard disk
- Select **Use an existing virtual hard disk file**
- Click the folder icon next to the dropdown
- Click **Add**
- Browse to your extracted OWASP BWA folder
- Select **OWASP Broken Web Apps-cl1.vmdk** (DO NOT select files ending in `-s001` or similar)
- Click **Choose**
- Click **Create**

### Step 4: Configure network for Host-Only
- Right-click **OWASP BWA** → **Settings**
- Go to **Network**
- **Adapter 1**: Change "Attached to" to **Host-Only Adapter**
- **Name**: `vboxnet0`
- Click **OK**

### Step 5: Start OWASP BWA
Select OWASP BWA and click **Start**.

### Step 6: Wait for boot
The VM may run `fsck` (filesystem check) on first boot due to the age of the image. This is normal and takes a few minutes.

### Step 7: Login
When the boot finishes, you will see a login prompt.
- **Username**: `root`
- **Password**: `owaspbwa`

### Step 8: Check IP address
```bash
ifconfig eth0
```
You will see an IP address like `192.168.56.xxx`. Note this down.

### Step 9: Verify web applications are accessible
On your **host machine** (not inside the VM), open a web browser and navigate to:
```
http://[OWASP-BWA-IP-Address]/
```
For example: `http://192.168.56.102/`

You should see the OWASP Broken Web Applications Project splash page with links to vulnerable applications.

### Step 10: Shut down OWASP BWA for now
```bash
shutdown now
```

---

## 11. Configure Host-Only Network
**Why Host-Only?** This network mode creates an isolated virtual network between your host computer and VMs. VMs can talk to each other but NOT to the internet or your home network. This keeps your vulnerable VMs contained.

### Step 1: Create Host-Only network
- In VirtualBox, click **File → Host Network Manager**
- Click **Create** (if no network exists)
- You should see `vboxnet0` with IP `192.168.56.1`
- Ensure **DHCP Server** is enabled (checkbox at bottom)
- Click **Close**

### Step 2: Configure Kali for Host-Only
- Right-click **Kali Linux** → **Settings**
- Go to **Network**
- **Adapter 1**: Change "Attached to" to **Host-Only Adapter**
- **Name**: `vboxnet0`
- Click **OK**

### Step 3: Configure Metasploitable for Host-Only
- Right-click **Metasploitable 2** → **Settings**
- Go to **Network**
- **Adapter 1**: Change "Attached to" to **Host-Only Adapter**
- **Name**: `vboxnet0`
- Click **OK**

### Step 4: Configure OWASP BWA for Host-Only
- Right-click **OWASP BWA** → **Settings**
- Go to **Network**
- **Adapter 1**: Change "Attached to" to **Host-Only Adapter**
- **Name**: `vboxnet0`
- Click **OK**

### Step 5: Start all VMs
Start Kali Linux, then Metasploitable 2, then OWASP BWA.

### Step 6: Check new IP addresses
In **Kali**:
```bash
ip a
```
Look for `eth0` or `enp0s3`. It should have an IP like `192.168.56.xxx`.

In **Metasploitable**:
```bash
ifconfig
```
Look for `eth0`. It should have an IP like `192.168.56.xxx` (different from Kali).

In **OWASP BWA**:
```bash
ifconfig eth0
```
Look for an IP like `192.168.56.xxx` (different from both).

---

## 12. Verify Connectivity

### Step 1: Ping from Kali to Metasploitable
In Kali terminal:
```bash
ping 192.168.56.[Metasploitable-IP]
```
Example:
```bash
ping 192.168.56.101
```
You should see:
```
64 bytes from 192.168.56.101: icmp_seq=1 ttl=64 time=0.5 ms
64 bytes from 192.168.56.101: icmp_seq=2 ttl=64 time=0.4 ms
```
Press `Ctrl + C` to stop.

### Step 2: Ping from Kali to OWASP BWA
In Kali terminal:
```bash
ping 192.168.56.[OWASP-BWA-IP]
```

### Step 3: Verify OWASP BWA web interface
On your **host machine**, open a browser and navigate to:
```
http://[OWASP-BWA-IP-Address]/
```

### Step 4: Run a simple Nmap scan from Kali
```bash
sudo nmap -sn 192.168.56.0/24
```
This discovers all devices on your lab network. You should see:
- Your host machine (`192.168.56.1`)
- Kali (its own IP)
- Metasploitable
- OWASP BWA

### Step 5: Run a port scan on OWASP BWA
```bash
sudo nmap -F [OWASP-BWA-IP]
```
The `-F` flag (fast scan) checks the 100 most common ports. You should see port **80** (HTTP) open.

### Step 6: Verify web applications through Kali
In Kali, open the Firefox browser and navigate to:
```
http://[OWASP-BWA-IP-Address]/
```
You can now browse the vulnerable web applications directly from Kali's browser.

---

## 13. Take Snapshots
**What is a snapshot?** A snapshot saves the complete state of a VM. You can break the VM, then restore it to the snapshot in seconds. This is critical for cybersecurity practice.

### Step 1: Snapshot Kali
With Kali running:
- Click **Machine → Take Snapshot**
- **Snapshot Name**: Clean - Updated
- **Description**: Kali after apt update, Host-Only configured
- Click **OK**

### Step 2: Snapshot Metasploitable
With Metasploitable running:
- Click **Machine → Take Snapshot**
- **Snapshot Name**: Clean - Imported
- **Description**: Fresh Metasploitable 2, Host-Only configured
- Click **OK**

### Step 3: Snapshot OWASP BWA
With OWASP BWA running:
- Click **Machine → Take Snapshot**
- **Snapshot Name**: Clean - Imported
- **Description**: Fresh OWASP BWA, Host-Only configured
- Click **OK**

### How to restore a snapshot:
- Select the VM in VirtualBox Manager
- Click the **Snapshots** tab (next to Details)
- Right-click the snapshot → **Restore**

💡 **Best practice**: Before every attack exercise, restore to your clean snapshots. This ensures you always start from a known good state.

---

## 14. Hands-On Lab

### Objective
Build a working home lab with Kali Linux, Metasploitable 2, and OWASP BWA on a Host-Only network.

### Instructions

**Step 1: Complete all sections above**
You should have:
- ✅ VirtualBox installed
- ✅ Kali Linux imported and updated
- ✅ Metasploitable 2 imported
- ✅ OWASP BWA imported
- ✅ All VMs on Host-Only network
- ✅ Successful ping from Kali to Metasploitable
- ✅ Successful ping from Kali to OWASP BWA
- ✅ OWASP BWA web interface accessible

**Step 2: Document your lab**
Create a file `lab_08_my_lab.md` with:

```markdown
# My Home Lab Documentation

## Host Machine
- OS: [Windows/Linux/macOS]
- RAM: [GB]
- Storage: [GB free]

## VirtualBox Version
[Run: VBoxManage --version]

## VM Inventory
| VM | IP Address | Status |
|----|------------|--------|
| Kali Linux | [IP] | Running |
| Metasploitable 2 | [IP] | Running |
| OWASP BWA | [IP] | Running |

## Connectivity Test
`ping [Metasploitable-IP]` → [paste output]
`ping [OWASP-BWA-IP]` → [paste output]

## Web Applications Test
OWASP BWA splash page accessible at: http://[OWASP-BWA-IP]/

## Snapshots Taken
- Kali: Clean - Updated
- Metasploitable: Clean - Imported
- OWASP BWA: Clean - Imported

## Issues Encountered
[What problems did you face? How did you fix them?]
```

**Step 3: Run a discovery scan**
```bash
sudo nmap -sn 192.168.56.0/24
```
Paste the output into your documentation.

**Step 4: Test a service scan on Metasploitable**
```bash
sudo nmap -F [Metasploitable-IP]
```
Note the open ports (21, 22, 23, 80, 139, 445, etc.).

**Step 5: Test a service scan on OWASP BWA**
```bash
sudo nmap -F [OWASP-BWA-IP]
```
Note that port 80 is open.

**Step 6: Browse OWASP BWA applications**
From Kali's browser, navigate to `http://[OWASP-BWA-IP]/` and explore the available applications:
- **DVWA** - Damn Vulnerable Web Application
- **WebGoat** - Web security training
- **Mutillidae** - Web security practice
- And many more!

### Deliverable
Save `lab_08_my_lab.md` — you will need this for future modules.

---

## 15. Summary & Key Takeaways

### What you built today:
A working cybersecurity home lab with three VMs:
- ✅ **Kali Linux** (attacker)
- ✅ **Metasploitable 2** (vulnerable Linux target with network services)
- ✅ **OWASP BWA** (vulnerable web application target)

### Isolated Host-Only network for safe practice.

### Key concepts learned:
- Virtualization lets you run multiple OSes on one computer
- Host-Only network isolates VMs from the internet
- Snapshots let you restore a clean state instantly
- OWASP BWA provides a target-rich environment for web application security testing

### Your lab is now ready for the rest of this course:
- **Network scanning** (Nmap)
- **Web application testing** (against OWASP BWA)
- **Exploitation** (Metasploit)
- **Post-exploitation**
- Every future module will use this lab

### Lab Architecture:
```
┌─────────────────────────────────────────────────────────────┐
│                Host-Only Network (192.168.56.0/24)          │
│                                                             │
│  ┌─────────────┐    ┌─────────────────┐    ┌─────────────┐ │
│  │     Kali    │    │  Metasploitable │    │  OWASP BWA  │ │
│  │  (Attacker) │◄──►│  (Linux Target) │    │   (Web App  │ │
│  │             │    │                 │    │   Target)   │ │
│  └─────────────┘    └─────────────────┘    └─────────────┘ │
│         │                   │                    │         │
│         └───────────────────┼────────────────────┘         │
│                             │                              │
└─────────────────────────────┼──────────────────────────────┘
                              │
                    ┌─────────┴─────────┐
                    │   Host Machine    │
                    │  (192.168.56.1)   │
                    └───────────────────┘
```

---

## 16. Quiz

1. **What is virtualization and why does cybersecurity need it?**

2. **What are the default credentials for Kali Linux (pre-built OVA)?**

3. **What command updates Kali Linux?**

4. **What are the default credentials for Metasploitable 2?**

5. **What are the default credentials for OWASP BWA?**

6. **Why should you use Host-Only network mode for vulnerable target VMs?**

7. **What command in Kali shows your IP address?**

8. **What is a snapshot and why are snapshots important?**

9. **What command pings another device on the network?**

10. **What port does OWASP BWA serve its web applications on?**

---

## 17. Resources

### Downloads
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Kali Linux Pre-built VMs](https://www.kali.org/get-kali/#kali-virtual-machines)
- [Metasploitable 2](https://sourceforge.net/projects/metasploitable/)
- [OWASP BWA](https://sourceforge.net/projects/owaspbwa/)

### Documentation
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [Kali Linux Documentation](https://www.kali.org/docs/)
- [OWASP BWA Project Page](https://sourceforge.net/p/owaspbwa/)

### Next Steps
Your lab is ready. Proceed to **Module 9: Linux Basic Commands**.

---

**Previous Module:** [Module 7 — Linux Security & Administration](Module_07_Linux_Security_&_Administration.md)

**Next Module:** [Module 9: Linux Basic Commands](Module_09_Linux_Basic_Commands.md)

---

*Module 8 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
