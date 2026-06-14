# Module 8: Virtualization & Home Lab Setup

> **Phase 1 — Absolute Foundations**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 2–3 hours | 📁 Folder: `01_FOUNDATIONS`

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
9. [Configure Host-Only Network](#9-configure-host-only-network)
10. [Verify Connectivity](#10-verify-connectivity)
11. [Take Snapshots](#11-take-snapshots)
12. [Hands-On Lab](#12-hands-on-lab)
13. [Summary & Key Takeaways](#13-summary--key-takeaways)
14. [Quiz](#14-quiz)
15. [Resources](#15-resources)

---

## 1. What is Virtualization?

**Virtualization** is technology that lets you run multiple operating systems on a single physical computer. Each virtual machine (VM) acts like a real computer with its own CPU, RAM, hard drive, and network card — but it's just software running on top of your real hardware.

**Why this matters for cybersecurity:**

Without virtualization, you would need three separate physical computers to practice attacking from Kali Linux to Windows and Linux targets. With virtualization, one computer runs all three.

**The setup you will build:**

```
Your Physical Computer (Windows/Linux/macOS)
└── VirtualBox (Hypervisor)
    ├── Kali Linux (Attacker VM)
    ├── Metasploitable 2 (Vulnerable Linux Target)
    └── (More target VMs will be added in later modules)
```

Each VM is isolated from your real computer. You can break, crash, or infect these VMs without any risk to your actual system.

---

## 2. Minimum Hardware Requirements

| Component | Minimum | Recommended | Why |
|-----------|---------|-------------|-----|
| **RAM** | 8 GB | 16 GB | Each VM needs 1-2 GB |
| **Storage** | 50 GB free | 100 GB+ SSD | VM files take space |
| **CPU** | 4 cores | 8 cores | VMs share your CPU |
| **Virtualization** | Enabled in BIOS | Enabled | Required for performance |

**Check if virtualization is enabled:**

- **Windows:** Task Manager → Performance → CPU → Virtualization: Enabled
- **Linux:** Run `grep -E 'vmx|svm' /proc/cpuinfo` → If you see output, it's enabled
- **macOS:** Built-in, no check needed

**If virtualization is disabled:** Restart your computer, enter BIOS (usually F2, F10, Del key), find "Intel VT-x" or "AMD-V", enable it, save and exit.

> ⚠️ Virtualization must be enabled or VMs will run extremely slowly.

---

## 3. Install VirtualBox

VirtualBox is a free, open-source hypervisor that runs on Windows, Linux, and macOS.

**Step 1: Download VirtualBox**

Go to: https://www.virtualbox.org/wiki/Downloads

Download:
- **VirtualBox platform package** for your operating system
- **VirtualBox Extension Pack** (same version)

**Step 2: Install VirtualBox**

**Windows:**
1. Run the installer
2. Click Next through all defaults
3. Click "Install"
4. Click "Finish"

**Linux (Ubuntu/Debian/Kali):**
```bash
sudo apt update
sudo apt install virtualbox -y
```

**macOS:**
1. Open the .dmg file
2. Double-click VirtualBox.pkg
3. Follow the installer

**Step 3: Install Extension Pack**

1. Double-click the downloaded Extension Pack file
2. VirtualBox will open
3. Click "Install"
4. Scroll through the license and click "I Agree"

**Step 4: Launch VirtualBox**

You should see the VirtualBox Manager window.

---

## 4. Download Kali Linux (Pre-built)

**Why pre-built?** Offensive Security provides ready-to-run Kali VM images. You don't need to install from ISO — just download, import, and start.

**Step 1: Download the OVA file**

Go to: https://www.kali.org/get-kali/#kali-virtual-machines

Under "Virtual Machines", click:
- **Kali Linux VirtualBox 64-bit** (approx 3.5 GB download)

**Step 2: Extract the downloaded file**

- The file downloads as a `.7z` archive
- Windows: Use 7-Zip (free from 7-zip.org)
- Linux: Run `tar -xf kali-linux-*.tar.xz`
- macOS: Use The Unarchiver or Keka

After extraction, you will have a `.ova` file.

---

## 5. Import Kali Linux

**Step 1: Import the OVA**

1. Open VirtualBox
2. Click **File → Import Appliance**
3. Click the folder icon and select your `.ova` file
4. Click **Next**

**Step 2: Review settings**

Default settings are fine:
- RAM: 2048 MB (2 GB)
- CPU: 1 or 2 cores

Click **Import**.

**Step 3: Wait for import**

This takes 5-10 minutes.

**Step 4: Verify Kali appears**

You should see "Kali Linux" in the left panel of VirtualBox Manager.

---

## 6. First Boot & Update Kali

**Step 1: Start Kali**

1. Select Kali Linux in VirtualBox Manager
2. Click the green **Start** arrow

**Step 2: Login**

```
Username: kali
Password: kali
```

**Step 3: Change the default password**

```bash
passwd
```
Type your new password twice.

> ⚠️ **Critical:** Kali comes with default credentials. Anyone on your network could access it. Change the password immediately.

**Step 4: Update Kali**

```bash
sudo apt update
sudo apt full-upgrade -y
```

This updates all tools to the latest versions. It may take 10-20 minutes depending on your internet speed.

**Step 5: Check your IP address**

```bash
ip a
```

Look for an IP address like `10.0.2.15` (NAT network). This is Kali's current network connection.

**Step 6: Shut down Kali for now**

```bash
sudo shutdown now
```

---

## 7. Download Metasploitable 2

**What is Metasploitable 2?** A purposely vulnerable Linux virtual machine. Every service on it has known vulnerabilities. It is your safe, legal target for practicing attacks.

**Download:**

Go to: https://sourceforge.net/projects/metasploitable/

Click **Download** (approx 800 MB). The file downloads as `metasploitable-linux-2.0.0.zip`.

**Extract the ZIP:**

After extraction, you will have a file named `Metasploitable.vmdk` (virtual disk file). This is the VM's hard drive.

> ⚠️ **WARNING:** Never expose Metasploitable to the internet or your home network. It is intentionally vulnerable. Keep it isolated in Host-Only mode.

---

## 8. Import Metasploitable 2

Metasploitable comes as a disk file (`.vmdk`), not an OVA. You will create a new VM and attach the existing disk.

**Step 1: Create a new VM**

1. In VirtualBox, click **Machine → New**
2. **Name:** `Metasploitable 2`
3. **Folder:** Keep default or choose location with enough space
4. **ISO Image:** Leave empty (we will attach disk manually)
5. **Type:** Linux
6. **Version:** Ubuntu (64-bit)
7. Click **Next**

**Step 2: Allocate RAM**

Set RAM to **512 MB** (Metasploitable needs very little). Click **Next**.

**Step 3: Attach existing hard disk**

1. Select **Use an existing virtual hard disk file**
2. Click the folder icon next to the dropdown
3. Click **Add**
4. Browse to your `Metasploitable.vmdk` file
5. Select it and click **Choose**
6. Click **Next** → **Finish**

**Step 4: Start Metasploitable**

Select Metasploitable 2 and click **Start**.

**Step 5: Login**

When the boot finishes, you will see a login prompt.

```
Username: msfadmin
Password: msfadmin
```

**Step 6: Check IP address**

```bash
ifconfig
```

You will see an IP address like `10.0.2.15` (NAT mode). Note this down.

**Step 7: Shut down Metasploitable**

```bash
sudo shutdown now
```

---

## 9. Configure Host-Only Network

**Why Host-Only?** This network mode creates an isolated virtual network between your host computer and VMs. VMs can talk to each other but NOT to the internet or your home network. This keeps your vulnerable VMs contained.

**Step 1: Create Host-Only network**

1. In VirtualBox, click **File → Host Network Manager**
2. Click **Create** (if no network exists)
3. You should see `vboxnet0` with IP `192.168.56.1`
4. Ensure **DHCP Server** is enabled (checkbox at bottom)
5. Click **Close**

**Step 2: Configure Kali for Host-Only**

1. Right-click **Kali Linux** → **Settings**
2. Go to **Network**
3. **Adapter 1:** Change "Attached to" to **Host-Only Adapter**
4. **Name:** `vboxnet0`
5. Click **OK**

**Step 3: Configure Metasploitable for Host-Only**

1. Right-click **Metasploitable 2** → **Settings**
2. Go to **Network**
3. **Adapter 1:** Change "Attached to" to **Host-Only Adapter**
4. **Name:** `vboxnet0`
5. Click **OK**

**Step 4: Start both VMs**

Start Kali Linux first, then Metasploitable 2.

**Step 5: Check new IP addresses**

**In Kali:**
```bash
ip a
```
Look for `eth0` or `enp0s3`. It should have an IP like `192.168.56.xxx`.

**In Metasploitable:**
```bash
ifconfig
```
Look for `eth0`. It should have an IP like `192.168.56.xxx` (different from Kali).

---

## 10. Verify Connectivity

**Step 1: Ping from Kali to Metasploitable**

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

**If ping fails:** 
- Make sure both VMs are on Host-Only network (check Settings → Network)
- Make sure you didn't type the wrong IP
- Run `ip a` in both VMs to confirm their addresses

**Step 2: Run a simple Nmap scan from Kali**

```bash
sudo nmap -sn 192.168.56.0/24
```

This discovers all devices on your lab network. You should see:
- Your host machine (192.168.56.1)
- Kali (its own IP)
- Metasploitable

---

## 11. Take Snapshots

**What is a snapshot?** A snapshot saves the complete state of a VM. You can break the VM, then restore it to the snapshot in seconds. This is critical for cybersecurity practice.

**Step 1: Snapshot Kali**

1. With Kali running, click **Machine → Take Snapshot**
2. **Snapshot Name:** `Clean - Updated`
3. **Description:** `Kali after apt update, Host-Only configured`
4. Click **OK**

**Step 2: Snapshot Metasploitable**

1. With Metasploitable running, click **Machine → Take Snapshot**
2. **Snapshot Name:** `Clean - Imported`
3. **Description:** `Fresh Metasploitable 2, Host-Only configured`
4. Click **OK**

**How to restore a snapshot:**

1. Select the VM in VirtualBox Manager
2. Click the **Snapshots** tab (next to Details)
3. Right-click the snapshot → **Restore**

> 💡 **Best practice:** Before every attack exercise, restore to your clean snapshots. This ensures you always start from a known good state.

---

## 12. Hands-On Lab

### Objective
Build a working home lab with Kali Linux and Metasploitable 2 on a Host-Only network.

### Instructions

**Step 1: Complete all sections above**

You should have:
- [ ] VirtualBox installed
- [ ] Kali Linux imported and updated
- [ ] Metasploitable 2 imported
- [ ] Both VMs on Host-Only network
- [ ] Successful ping from Kali to Metasploitable

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

## Connectivity Test
`ping [Metasploitable-IP]` → [paste output]

## Snapshots Taken
- Kali: Clean - Updated
- Metasploitable: Clean - Imported

## Issues Encountered
[What problems did you face? How did you fix them?]
```

**Step 3: Run a discovery scan**

```bash
sudo nmap -sn 192.168.56.0/24
```

Paste the output into your documentation.

**Step 4: Test a simple service scan**

```bash
sudo nmap -F [Metasploitable-IP]
```

The `-F` flag (fast scan) checks the 100 most common ports. You should see many open ports (21, 22, 23, 80, 139, 445, etc.).

### Deliverable

Save `lab_08_my_lab.md` — you will need this for future modules.

---

## 13. Summary & Key Takeaways

**What you built today:**
- A working cybersecurity home lab with two VMs
- Kali Linux (attacker) and Metasploitable 2 (vulnerable target)
- Isolated Host-Only network for safe practice

**Key concepts learned:**
- Virtualization lets you run multiple OSes on one computer
- Host-Only network isolates VMs from the internet
- Snapshots let you restore a clean state instantly

**Your lab is now ready** for the rest of this course. Every scanning, exploitation, and post-exploitation module will use this lab.

---

## 14. Quiz

1. What is virtualization and why does cybersecurity need it?

2. What are the default credentials for Kali Linux (pre-built OVA)?

3. What command updates Kali Linux?

4. What are the default credentials for Metasploitable 2?

5. Why should you use Host-Only network mode for vulnerable target VMs?

6. What command in Kali shows your IP address?

7. What is a snapshot and why are snapshots important?

8. What command pings another device on the network?

<details>
<summary>📋 Click to reveal answers</summary>

1. Virtualization runs multiple operating systems on one physical computer. Cybersecurity needs it to safely practice attacks without risking real systems.

2. `kali` / `kali`

3. `sudo apt update && sudo apt full-upgrade -y`

4. `msfadmin` / `msfadmin`

5. Host-Only keeps vulnerable VMs isolated from the internet and your home network, preventing accidental spread of attacks.

6. `ip a`

7. A snapshot saves the complete state of a VM. Snapshots let you restore to a clean state after breaking or infecting the VM.

8. `ping [IP-address]`

</details>

---

## 15. Resources

### Downloads
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Kali Linux Pre-built VMs](https://www.kali.org/get-kali/#kali-virtual-machines)
- [Metasploitable 2](https://sourceforge.net/projects/metasploitable/)

### Documentation
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [Kali Linux Documentation](https://www.kali.org/docs/)

### Next Steps
Your lab is ready. Proceed to **Module 9: OSINT Part 1**.

---

**Previous Module:** [Module 7 — Linux Security & Administration →](../Module_07_Linux_Security.md)

**Next Module:** [Module 9 — OSINT Part 1 →](../Module_09_OSINT_Part1.md)

---

*Module 8 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
