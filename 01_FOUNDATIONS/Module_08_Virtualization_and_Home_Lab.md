# Module 8: Virtualization & Home Lab Setup

> **Phase 1 — Absolute Foundations**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 10–12 hours | 📁 Folder: `01_FOUNDATIONS`

---

## Table of Contents

1. [Why You Need a Home Lab](#1-why-you-need-a-home-lab)
2. [Virtualization Fundamentals](#2-virtualization-fundamentals)
3. [Hypervisor Comparison](#3-hypervisor-comparison)
4. [Setting Up VirtualBox (Primary Platform)](#4-setting-up-virtualbox-primary-platform)
5. [Setting Up Kali Linux (Attacker VM)](#5-setting-up-kali-linux-attacker-vm)
6. [Setting Up Windows 10/11 (Target VM)](#6-setting-up-windows-1011-target-vm)
7. [Setting Up Ubuntu Server (Target VM)](#7-setting-up-ubuntu-server-target-vm)
8. [Vulnerable Target Machines](#8-vulnerable-target-machines)
9. [VM Networking Deep Dive](#9-vm-networking-deep-dive)
10. [Snapshots, Cloning, Templates & Vagrant](#10-snapshots-cloning-templates--vagrant)
11. [Docker for Security](#11-docker-for-security)
12. [Building Your Complete Home Lab](#12-building-your-complete-home-lab)
13. [Hands-On Lab: Full Home Lab Build](#13-hands-on-lab-full-home-lab-build)
14. [Summary & Key Takeaways](#14-summary--key-takeaways)
15. [Quiz: Test Your Knowledge](#15-quiz-test-your-knowledge)
16. [Resources](#16-resources)

---

## 1. Why You Need a Home Lab

### 1.1 The Problem with Learning Cybersecurity Without a Lab

Cybersecurity is fundamentally a **hands-on discipline**. You cannot learn offensive or defensive techniques by reading alone.

Consider the gap:

```
Reading about SQL injection:     You understand the concept
Actually exploiting it in a lab: You understand HOW it works, WHY it works,
                                 what the error messages look like, what goes
                                 wrong, how to detect it, how to fix it
```

Every major cybersecurity certification recognizes this:
- **OSCP** — entirely practical exam (24 hours of live hacking)
- **PNPT** — practical exam against a real network
- **GPEN, GWAPT** — include practical components
- **HTB, TryHackMe** — entirely lab-based learning

**Without a lab you cannot:**
- Practice attacks safely and legally
- Break things without consequences (then fix them and understand why they broke)
- Test defensive tools and configurations
- Build muscle memory with tools
- Create portfolio evidence of your skills

---

### 1.2 What a Home Lab Gives You

| Benefit | Detail |
|---|---|
| **Legal environment** | You own it — no authorization issues |
| **Safe to break** | Crash VMs, corrupt configs, start over with no real harm |
| **Reproducibility** | Snapshots let you reset to any state instantly |
| **Isolation** | Attacks stay inside your lab network |
| **Cost** | Most software is free; hardware is a one-time cost |
| **Always available** | Practice at any time, any pace |
| **Resume evidence** | Document your builds and projects |

---

### 1.3 Minimum Hardware Requirements

| Component | Minimum | Recommended | Notes |
|---|---|---|---|
| **RAM** | 8 GB | 16–32 GB | Each VM uses 1–4 GB; 16 GB runs 3–4 VMs comfortably |
| **CPU** | 4 cores, VT-x/AMD-V enabled | 8+ cores | Virtualization support MUST be enabled in BIOS |
| **Storage** | 256 GB SSD | 512 GB–1 TB SSD | VMs eat storage; SSD dramatically improves performance |
| **Network** | Any | Wired preferred | Wi-Fi works; wired is more stable for lab networking |
| **OS** | Windows 10/11 or Linux | Either | Host OS where hypervisor runs |

**Check if your CPU supports virtualization:**
```bash
# Linux
grep -E 'vmx|svm' /proc/cpuinfo | head -5
# vmx = Intel VT-x supported
# svm = AMD-V supported

# Windows (PowerShell)
Get-ComputerInfo -Property HyperVRequirementVirtualizationFirmwareEnabled
# Or check Task Manager → Performance → CPU → Virtualization: Enabled
```

**Enable virtualization in BIOS:**
- Restart → Enter BIOS/UEFI (usually F2, F10, F12, or Del key)
- Look for: Intel Virtualization Technology, VT-x, AMD-V, SVM
- Enable → Save and exit

> ⚠️ **If virtualization is disabled in BIOS, VMs will run extremely slowly or not at all.**

---

### 1.4 Cloud Lab Alternatives

If you lack hardware, cloud platforms provide virtual lab environments:

| Platform | Cost | Notes |
|---|---|---|
| **TryHackMe** | Free + paid tiers | Browser-based; pre-configured rooms |
| **HackTheBox** | Free + VIP | More advanced; real-world style |
| **AWS Free Tier** | Free (limits) | Run your own VMs in the cloud |
| **Azure Free Tier** | Free (limits) | Same |
| **Linode/Vultr/DigitalOcean** | ~$5–10/month | Cheap VPS for practice |

**Recommendation:** Start with TryHackMe/HTB while building your local lab. Long-term, a local lab is more powerful and has no internet dependency.

---

## 2. Virtualization Fundamentals

### 2.1 What is Virtualization?

**Virtualization** is technology that allows you to run multiple **virtual machines (VMs)** — each with their own OS, filesystem, and network stack — on a single physical computer.

Each VM is completely isolated from others (unless you configure networking between them).

```
Physical Machine (Host)
├── Host OS (Windows 11 / Ubuntu)
│   └── Hypervisor (VirtualBox / VMware)
│       ├── VM 1: Kali Linux (attacker)
│       ├── VM 2: Windows 10 (target)
│       ├── VM 3: Ubuntu Server (target)
│       └── VM 4: Metasploitable (vulnerable target)
```

Each VM has:
- Virtual CPU (vCPU) — shares host CPU
- Virtual RAM — allocated from host RAM
- Virtual disk (VMDK/VDI/QCOW2 file on host filesystem)
- Virtual network interfaces
- Virtual peripherals (USB, display, audio)

---

### 2.2 Type 1 vs Type 2 Hypervisors

This is a fundamental concept that appears in certifications and interviews.

#### Type 1 — Bare Metal Hypervisor

Runs **directly on the hardware** — there is no host OS underneath.

```
Physical Hardware
└── Hypervisor (Type 1) — runs directly on hardware
    ├── VM 1 (OS + Apps)
    ├── VM 2 (OS + Apps)
    └── VM 3 (OS + Apps)
```

**Characteristics:**
- Better performance — no host OS overhead
- Used in enterprise and data centers
- Each VM called a "guest"
- Requires dedicated hardware

**Examples:**
- **VMware ESXi** — industry standard enterprise hypervisor
- **Microsoft Hyper-V** (in server mode) — built into Windows Server
- **Xen** — open source, used by AWS
- **KVM** (Kernel-based Virtual Machine) — Linux kernel module; technically Type 1 when Linux IS the host
- **Proxmox VE** — open source, built on KVM + LXC

---

#### Type 2 — Hosted Hypervisor

Runs **as an application on top of a host OS**.

```
Physical Hardware
└── Host OS (Windows/macOS/Linux)
    └── Hypervisor (Type 2) — runs as application
        ├── VM 1 (Guest OS + Apps)
        ├── VM 2 (Guest OS + Apps)
        └── VM 3 (Guest OS + Apps)
```

**Characteristics:**
- Easier to install and use
- Slightly lower performance (host OS layer)
- Perfect for home labs and development
- Can run alongside other applications

**Examples:**
- **VirtualBox** — free, cross-platform (Oracle)
- **VMware Workstation Pro/Player** — better performance than VirtualBox
- **VMware Fusion** (macOS)
- **Parallels Desktop** (macOS)
- **QEMU** — can run as either Type 1 or Type 2

---

#### Comparison Table

| | Type 1 | Type 2 |
|---|---|---|
| Runs on | Hardware directly | Host OS |
| Performance | Higher | Slightly lower |
| Use case | Enterprise, data centers | Home lab, development |
| Cost | Often expensive | Usually free/cheap |
| Setup complexity | Higher | Lower |
| Examples | ESXi, Hyper-V Server, Proxmox | VirtualBox, VMware Workstation |

---

### 2.3 Key Virtualization Concepts

| Term | Definition |
|---|---|
| **Host** | The physical machine running the hypervisor |
| **Guest** | The VM running inside the hypervisor |
| **vCPU** | Virtual CPU assigned to a VM |
| **Virtual Disk** | A file on the host representing the VM's hard drive (.vmdk, .vdi, .qcow2) |
| **Snapshot** | A saved state of a VM at a specific point in time |
| **Clone** | An exact copy of a VM |
| **Template** | A clean VM image used as a base for creating multiple VMs |
| **NAT** | Network Address Translation — VM shares host's IP |
| **Bridged** | VM gets its own IP on the physical network |
| **Host-Only** | VMs can communicate with each other and host, but not internet |
| **Internal** | VMs can only communicate with each other — completely isolated |
| **OVA/OVF** | Open Virtualization Format — portable VM package format |

---

## 3. Hypervisor Comparison

### 3.1 VirtualBox

**Developer:** Oracle (originally InnoTek, then Sun Microsystems)
**Cost:** Free, open source (GPL)
**Platforms:** Windows, macOS, Linux

**Strengths:**
- Completely free — no feature limits
- Cross-platform (same VMs work on Windows, macOS, Linux host)
- Large community and excellent documentation
- Good OVA/OVF support — easy to import pre-built labs
- Supports all major guest OS types
- Built-in VM groups, snapshots, cloning
- Guest Additions improve performance and integration

**Weaknesses:**
- Performance inferior to VMware (especially 3D graphics, USB 3.0)
- Can be unstable on macOS (especially Apple Silicon — limited support)
- USB 3.0 and some advanced features require VirtualBox Extension Pack (free but separate PUEL license)
- Less polished UI compared to VMware

**Best for:** Home labs, learners on a budget, cross-platform environments

**Download:** [https://www.virtualbox.org/](https://www.virtualbox.org/)

---

### 3.2 VMware Workstation Pro / Player

**Developer:** VMware (now owned by Broadcom)
**Cost:**
- **Workstation Player:** Free for personal/non-commercial use (limited features)
- **Workstation Pro:** Previously paid (~$200); **made free for personal use in 2024** after Broadcom acquisition

**Platforms:** Windows, Linux (Workstation); macOS (Fusion)

**Strengths:**
- Better performance than VirtualBox (especially 3D, USB)
- More stable — enterprise-grade reliability
- Better snapshot management
- Excellent Unity Mode (run Windows apps on Linux host seamlessly)
- Supports advanced networking (multiple virtual networks)
- Better hardware compatibility

**Weaknesses:**
- Broadcom acquisition created licensing uncertainty
- VMware Fusion (macOS) has had Apple Silicon issues
- Heavier resource footprint than VirtualBox

**Best for:** Users who want better performance and stability; professional environments

**Download:** [https://www.vmware.com/products/workstation-player.html](https://www.vmware.com/products/workstation-player.html)

---

### 3.3 Proxmox VE

**Developer:** Proxmox Server Solutions GmbH
**Cost:** Free (open source), paid support subscription available
**Platform:** Bare metal only (Type 1)

**What it is:** An enterprise-grade open source virtualization platform combining:
- **KVM** for full virtual machines
- **LXC** for lightweight Linux containers
- **Web UI** for management
- **Ceph / ZFS** storage support
- **Clustering** support

**Strengths:**
- Type 1 — excellent performance
- Free and open source
- Powerful web UI — manage everything from browser
- Supports both VMs and containers
- Excellent for dedicated home lab servers
- Built-in backup, snapshot, and restore
- VLAN support, software-defined networking
- Can cluster multiple physical machines

**Weaknesses:**
- Requires dedicated hardware (can't run alongside other apps)
- Steeper learning curve
- Overkill for basic labs

**Best for:** Dedicated home lab servers (old desktop/laptop repurposed); advanced users; enterprise simulation

**Download:** [https://www.proxmox.com/en/downloads](https://www.proxmox.com/en/downloads)

**Proxmox Home Lab Setup (brief):**
```bash
# Download ISO from proxmox.com
# Flash to USB with Balena Etcher or Rufus
# Boot target machine from USB
# Follow installer — configure IP, hostname, password
# Access web UI: https://[IP]:8006
# Default login: root / [password you set]
```

---

### 3.4 QEMU / KVM

**QEMU:** Open source machine emulator and virtualizer
**KVM:** Kernel-based Virtual Machine — Linux kernel module for hardware virtualization

These two are often used together: **QEMU/KVM**.

**How it works:**
- KVM turns Linux into a Type 1 hypervisor (uses CPU hardware virtualization)
- QEMU provides device emulation and management
- Together: near-native performance with full hardware emulation

**Management frontends:**
- **virt-manager** — GUI (like VirtualBox, but on Linux)
- **virsh** — Command-line management
- **Cockpit** — Web UI
- **oVirt** — Enterprise-grade management platform

**Strengths:**
- Type 1 performance (when on Linux host)
- Native Linux — best integration on Linux hosts
- Used by all major cloud providers (AWS uses Xen/KVM, GCP uses KVM)
- Extremely flexible — emulate almost any hardware

**Weaknesses:**
- Linux-only host
- More complex setup
- Less beginner-friendly

**Install on Ubuntu:**
```bash
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager -y
sudo systemctl enable --now libvirtd
sudo usermod -aG libvirt,kvm $USER
# Log out and back in
virt-manager  # Launch GUI
```

---

### 3.5 Hypervisor Recommendation for This Course

```
Beginner (Windows/Linux host):     → VirtualBox (free, well-documented)
Better performance needed:         → VMware Workstation Pro (now free)
macOS Apple Silicon:               → VMware Fusion or UTM
Dedicated lab machine available:   → Proxmox VE
Linux power user:                  → QEMU/KVM with virt-manager
```

**For this course, we use VirtualBox as the primary platform** — it's free, runs on all hosts, and all instructions will be VirtualBox-based unless noted.

---

## 4. Setting Up VirtualBox (Primary Platform)

### 4.1 Installation

**Windows:**
```
1. Download VirtualBox installer: https://www.virtualbox.org/wiki/Downloads
2. Download VirtualBox Extension Pack (same page — same version!)
3. Run VirtualBox installer → Next → Next → Install
4. Double-click Extension Pack → Install → Accept license
5. Launch VirtualBox
```

**Ubuntu/Debian Linux:**
```bash
# Method 1: Official Oracle repository (recommended — gets latest version)
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | \
    sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
    https://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | \
    sudo tee /etc/apt/sources.list.d/virtualbox.list

sudo apt update
sudo apt install virtualbox-7.0 -y

# Install Extension Pack
VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-7.x.x.vbox-extpack
```

**macOS:**
```
1. Download .dmg from virtualbox.org
2. Open .dmg → Double-click VirtualBox.pkg
3. Follow installer
4. System Preferences → Security → Allow Oracle America
5. Restart if required
```

---

### 4.2 VirtualBox Interface Overview

```
VirtualBox Manager
├── Menu Bar
│   ├── Machine → New, Add, Settings, Start, Stop
│   ├── File → Import/Export Appliance, Preferences
│   └── Help → Documentation
├── Left Panel — VM List
│   ├── VM Name
│   ├── VM State (Running/Powered Off/Saved)
│   └── Preview thumbnail
└── Right Panel — VM Details
    ├── General (Name, OS type)
    ├── System (RAM, CPU, boot order)
    ├── Display (Video RAM, 3D acceleration)
    ├── Storage (Virtual disks, ISOs attached)
    ├── Network (Adapter type and mode)
    └── Shared Folders
```

---

### 4.3 VirtualBox Global Settings

Before creating VMs, configure global settings:
```
File → Preferences:
├── General
│   └── Default Machine Folder: [Set to SSD drive with most space]
├── Input
│   └── Host Key: Right Ctrl (default) — releases mouse from VM
└── Network
    └── Host-Only Networks: We'll configure these later
```

---

### 4.4 VirtualBox Guest Additions

**Guest Additions** is a software package installed *inside the VM* that dramatically improves performance and integration:

**What it adds:**
- Better display performance (hardware acceleration)
- Dynamic window resizing
- Seamless mouse integration (no "capture" needed)
- Shared clipboard (copy/paste between host and VM)
- Shared folders
- Drag and drop
- Improved time synchronization

**Installing Guest Additions (inside a running VM):**
```bash
# In VirtualBox menu while VM is running:
Devices → Insert Guest Additions CD image...

# Inside Ubuntu/Kali VM:
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run
sudo reboot

# Inside Windows VM:
# AutoPlay should launch. If not:
# Open D:\ → Run VBoxWindowsAdditions.exe → Follow installer → Reboot
```

---

## 5. Setting Up Kali Linux (Attacker VM)

### 5.1 What is Kali Linux?

**Kali Linux** is a Debian-based Linux distribution specifically designed for **penetration testing and digital forensics**, maintained by Offensive Security.

**Key features:**
- 600+ pre-installed security tools (Nmap, Metasploit, Burp Suite, Wireshark, Aircrack-ng, etc.)
- Regular updates to tools
- Multiple desktop environments (XFCE default, GNOME, KDE, etc.)
- Live boot capability (run without installing)
- ARM/cloud/WSL variants available
- Documented tool database at [tools.kali.org](https://www.kali.org/tools/)

> ⚠️ **Kali Linux is a tool for security professionals in authorized testing environments. Never use it against systems you don't own or have explicit written permission to test.**

---

### 5.2 Method 1: Download Pre-Built VirtualBox VMA (Recommended for Beginners)

Offensive Security provides **pre-built VM images** — the fastest way to get started.

```
1. Go to: https://www.kali.org/get-kali/#kali-virtual-machines
2. Download: Kali Linux VirtualBox (64-bit) — approx 3.5 GB
3. Extract the downloaded archive (.7z or .zip)
   - Windows: Use 7-Zip (7-zip.org)
   - Linux: tar -xf kali-*.tar.xz
4. In VirtualBox: File → Import Appliance → Select the .ova file
5. Review settings → Import (takes 5–10 minutes)
6. Start the VM
```

**Default credentials for pre-built Kali image:**
```
Username: kali
Password: kali
```

> ⚠️ **Change the default password immediately after first login!**
> ```bash
> passwd
> ```

---

### 5.3 Method 2: Manual Installation from ISO

For deeper understanding of the Linux installation process:

```
Step 1: Download Kali ISO
  https://www.kali.org/get-kali/#kali-installer-images
  → Kali Linux 64-bit (Installer) — approx 4 GB

Step 2: Create New VM in VirtualBox
  Machine → New
  ├── Name: Kali Linux
  ├── Type: Linux
  ├── Version: Debian (64-bit)
  ├── RAM: 2048 MB minimum (4096 MB recommended)
  ├── Virtual Hard Disk: Create new → VDI → Dynamically allocated → 80 GB

Step 3: Configure VM Settings (before first boot)
  Settings → System
    ├── Motherboard: RAM 4096 MB, boot order: Optical → Hard Disk
    └── Processor: 2 CPUs, enable PAE/NX
  Settings → Display
    └── Video Memory: 128 MB; Enable 3D Acceleration
  Settings → Storage
    └── IDE Controller → Empty → Click DVD icon → Choose ISO file
  Settings → Network
    └── Adapter 1: NAT (for now — change later)
  Settings → USB
    └── USB 3.0 Controller (requires Extension Pack)

Step 4: Start VM → Kali installer launches
  ├── Select: Graphical Install
  ├── Language: English
  ├── Location: Your country
  ├── Keyboard: Your layout
  ├── Hostname: kali (or custom name)
  ├── Domain: (leave blank)
  ├── Full name: (your name)
  ├── Username: kali (or custom)
  ├── Password: [choose strong password]
  ├── Timezone: Your timezone
  ├── Partitioning: Guided — use entire disk → All files in one partition
  ├── Mirror: Yes → your nearest mirror
  ├── Desktop: Xfce (recommended — lightweight) or GNOME
  └── Install GRUB → /dev/sda

Step 5: Remove ISO after installation
  Settings → Storage → Remove the ISO from IDE controller
  Reboot VM
```

---

### 5.4 Post-Installation Kali Configuration

Run these immediately after installing/importing Kali:

```bash
# 1. Update everything
sudo apt update && sudo apt full-upgrade -y

# 2. Install all Kali tools (optional — takes time and space)
sudo apt install kali-linux-everything -y
# Or specific categories:
sudo apt install kali-tools-top10 -y      # Top 10 tools
sudo apt install kali-tools-web -y        # Web application testing
sudo apt install kali-tools-exploitation -y

# 3. Change default password if using pre-built image
passwd

# 4. Enable/start SSH if needed
sudo systemctl enable ssh
sudo systemctl start ssh
# Edit SSH config for security
sudo nano /etc/ssh/sshd_config
# Uncomment: Port 22
# Set: PermitRootLogin no  (for security)

# 5. Install VirtualBox Guest Additions
sudo apt install virtualbox-guest-x11 -y
sudo reboot

# 6. Check tools are present
nmap --version
msfconsole --version
burpsuite &
wireshark --version

# 7. Configure tmux (useful for multiple terminals)
sudo apt install tmux -y

# 8. Set up aliases for common tools
echo "alias update='sudo apt update && sudo apt full-upgrade -y'" >> ~/.bashrc
source ~/.bashrc
```

---

### 5.5 Kali Linux Desktop Environments

| Desktop | Default | RAM Usage | Best For |
|---|---|---|---|
| **Xfce** | ✅ Yes | ~400 MB | Most users — lightweight, clean |
| **GNOME** | No | ~800 MB | Users who prefer modern UI |
| **KDE Plasma** | No | ~600 MB | Highly customizable |
| **i3/i3-gaps** | No | ~200 MB | Power users, keyboard-driven |
| Headless | No | ~200 MB | SSH-only, server use |

Switch desktop environment:
```bash
sudo apt install kali-desktop-gnome -y
# Or:
sudo apt install kali-desktop-kde -y
sudo update-alternatives --config x-session-manager
```

---

### 5.6 Kali Linux Tool Categories

| Category | Key Tools |
|---|---|
| Information Gathering | nmap, recon-ng, maltego, theHarvester, shodan |
| Vulnerability Analysis | openvas, nikto, legion, nessus |
| Web Application | burpsuite, zaproxy, sqlmap, wfuzz, gobuster |
| Password Attacks | hashcat, john, hydra, medusa, crunch |
| Wireless | aircrack-ng, reaver, wifite, kismet |
| Exploitation | metasploit, exploitdb, beef-xss |
| Sniffing & Spoofing | wireshark, tcpdump, ettercap, bettercap, responder |
| Post Exploitation | mimikatz, powersploit, bloodhound, impacket |
| Forensics | autopsy, sleuthkit, volatility, binwalk |
| Reverse Engineering | ghidra, radare2, gdb, pwndbg |
| Reporting | dradis, piperka, maltego |

---

## 6. Setting Up Windows 10/11 (Target VM)

### 6.1 Why Windows as a Target?

- Most corporate environments run Windows
- Active Directory attacks require Windows targets
- Many real-world vulnerabilities affect Windows
- Learning Windows security from the attacker's perspective

---

### 6.2 Getting a Windows ISO (Free and Legal)

Microsoft provides free evaluation ISOs and developer images:

**Option 1: Windows 10/11 Evaluation (180-day trial)**
```
URL: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise
URL: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise
→ Download 64-bit ISO (approx 5–6 GB)
Note: 180-day evaluation; after expiry, reminders appear but VM still works
```

**Option 2: Windows 11 Developer VM (Pre-built)**
```
URL: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/
→ Download VirtualBox image directly (.zip, approx 20 GB)
→ Import OVA into VirtualBox
Note: Expires after 90 days — use snapshots to reset
```

**Option 3: Windows Media Creation Tool**
```
URL: https://www.microsoft.com/en-us/software-download/windows11
→ For creating ISO from Media Creation Tool
Note: Requires a license key for permanent use (evaluation doesn't)
```

---

### 6.3 Creating Windows VM in VirtualBox

```
Machine → New
├── Name: Windows 10 Target
├── Type: Microsoft Windows
├── Version: Windows 10 (64-bit)
├── RAM: 4096 MB (minimum 2048 MB)
└── Hard Disk: Create new → VDI → Dynamically allocated → 60 GB

Settings → System
├── Processor: 2 CPUs
└── Enable PAE/NX, VT-x/AMD-V

Settings → Display
└── Video Memory: 128 MB; 3D Acceleration: Enable

Settings → Storage
└── Attach Windows ISO to IDE Controller

Settings → Network
└── Adapter 1: Host-Only (for isolated testing) or NAT (for internet)
```

---

### 6.4 Windows Installation Process

```
1. Start VM → Boot from ISO
2. Install Now → Custom: Install Windows only
3. Select disk → New → Apply → Next → Installing...
4. Out-of-box experience (OOBE):
   - Region: Your region
   - Keyboard layout: Your layout
   - Network: Click "I don't have internet" → "Continue with limited setup"
     (Creates local account — no Microsoft account needed)
   - Name: labuser (or any name)
   - Password: [set a password you'll remember]
   - Security questions: [answer them]
   - Privacy settings: Disable everything (lab use)
5. Desktop appears → Installation complete
```

---

### 6.5 Post-Installation Windows Configuration for Lab

```powershell
# Run PowerShell as Administrator for all commands below

# 1. Disable Windows Defender (for lab — allows testing malware safely)
Set-MpPreference -DisableRealtimeMonitoring $true
# Or via GUI: Windows Security → Virus & threat protection → Manage settings → Off

# 2. Disable Windows Firewall (for lab connectivity)
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False

# 3. Enable Remote Desktop
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

# 4. Enable SMB (target for EternalBlue lab)
Enable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
Set-SmbServerConfiguration -EnableSMB1Protocol $true -Force
Set-SmbServerConfiguration -EnableSMB2Protocol $true -Force

# 5. Create additional test users
net user testuser Password123! /add
net localgroup administrators testuser /add

# 6. Disable UAC (for lab — simplifies testing)
Set-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name EnableLUA -Value 0

# 7. Install common lab tools (via Chocolatey)
# First install Chocolatey:
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Then install tools:
choco install wireshark sysinternals notepadplusplus 7zip googlechrome -y
```

> ⚠️ **All these configurations DISABLE security features. This is intentional for a lab target. NEVER apply these settings to a real production machine.**

---

### 6.6 Making Windows 10 Vulnerable (For Practice)

For practicing specific exploits:

```powershell
# Disable automatic updates (keep the system at a specific patch level)
# Via registry:
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" -Name "NoAutoUpdate" -Value 1 -Force

# Or Services → Windows Update → Disabled

# For MS17-010 (EternalBlue) practice:
# Need Windows 7/Server 2008 unpatched, OR
# Use Metasploitable3 (has vulnerable SMB)
# Note: Windows 10 is NOT vulnerable to EternalBlue in default state

# For basic exploitation practice, keep the system unpatched:
# Don't install Windows updates in the lab VM
```

---

## 7. Setting Up Ubuntu Server (Target VM)

### 7.1 Why Ubuntu Server?

- Most web servers, cloud instances, and enterprise Linux run Ubuntu/Debian
- Practice Linux privilege escalation
- Host web applications, databases, SSH services for practice
- Lightweight — can run with 1 GB RAM

---

### 7.2 Download Ubuntu Server ISO

```
URL: https://ubuntu.com/download/server
→ Ubuntu Server 22.04 LTS (Long Term Support) — approx 1.5 GB
LTS = 5 years of security updates
```

---

### 7.3 Create Ubuntu Server VM

```
Machine → New
├── Name: Ubuntu Server
├── Type: Linux
├── Version: Ubuntu (64-bit)
├── RAM: 1024–2048 MB
└── Hard Disk: VDI → Dynamic → 30 GB

Settings → Network
└── Adapter 1: Host-Only (for lab isolation)
   Adapter 2: NAT (for internet access during setup)
```

---

### 7.4 Ubuntu Server Installation

```
1. Start VM → Boot from ISO
2. Select: Install Ubuntu Server (not Minimal)
3. Language: English
4. Network: Configure both adapters (DHCP usually works)
5. Storage: Use entire disk → Default LVM setup → Done → Confirm
6. Profile setup:
   ├── Your name: labadmin
   ├── Server name: ubuntu-target
   ├── Username: labadmin
   └── Password: [set password]
7. Ubuntu Pro: Skip
8. OpenSSH: ✅ Install OpenSSH server (IMPORTANT for remote access)
9. Featured snaps: Skip
10. Installation completes → Reboot
```

---

### 7.5 Post-Installation Ubuntu Server Configuration

```bash
# Log in after reboot

# 1. Update system
sudo apt update && sudo apt upgrade -y

# 2. Note the IP addresses
ip a
hostname -I

# 3. Install useful services for practice

# Web server (Apache)
sudo apt install apache2 -y
sudo systemctl enable apache2
# Access from browser: http://[VM-IP]

# PHP (for vulnerable web apps)
sudo apt install php libapache2-mod-php php-mysql -y

# MySQL database
sudo apt install mysql-server -y
sudo mysql_secure_installation

# FTP server
sudo apt install vsftpd -y
sudo systemctl enable vsftpd

# Netcat (for shell practice)
sudo apt install netcat-traditional -y

# 4. Create intentionally weak configurations (for lab)

# Create weak SSH user
sudo useradd -m -s /bin/bash weakuser
echo "weakuser:password123" | sudo chpasswd

# Configure weak SSH (allow root, password auth)
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# 5. Install DVWA (Damn Vulnerable Web Application)
sudo apt install php php-mysqli php-gd -y
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chown -R www-data:www-data DVWA
# Navigate to http://[IP]/DVWA and complete setup
```

---

### 7.6 Getting Ubuntu Server IP and Connecting via SSH

```bash
# On Ubuntu Server VM, check IPs:
ip a | grep 'inet '

# From Kali Linux, connect via SSH:
ssh labadmin@[ubuntu-server-ip]

# This is your primary workflow for most lab exercises:
# Terminal in Kali → SSH to Ubuntu target
```

---

## 8. Vulnerable Target Machines

### 8.1 Why Use Purpose-Built Vulnerable Machines?

Real-world systems are complex and unpredictable. Purpose-built vulnerable machines:
- Are designed to be compromised (legal and ethical)
- Have known vulnerabilities (you can verify your techniques work)
- Are documented — community walkthroughs help you learn
- Cover specific vulnerability types systematically

---

### 8.2 Metasploitable 2

**What it is:** A purposely vulnerable Ubuntu 8.04 server VM with dozens of exploitable services.

**Download:**
```
URL: https://sourceforge.net/projects/metasploitable/
File: metasploitable-linux-2.0.0.zip (approx 800 MB)
```

**Import:**
```
1. Extract ZIP → You get a .vmdk file
2. VirtualBox → New VM:
   ├── Name: Metasploitable2
   ├── Type: Linux, Version: Ubuntu (64-bit)
   ├── RAM: 512–1024 MB
   └── Hard Disk: Use existing → select the .vmdk file
3. Settings → Network: Host-Only (NEVER put on internet)
```

**Default credentials:**
```
Username: msfadmin
Password: msfadmin
```

**What's vulnerable in Metasploitable 2:**

| Service | Port | Vulnerability |
|---|---|---|
| vsftpd 2.3.4 | 21/tcp | Backdoor — shell on port 6200 |
| OpenSSH 4.7p1 | 22/tcp | Username enumeration |
| Telnet | 23/tcp | Unencrypted — credentials in cleartext |
| SMTP (Postfix) | 25/tcp | Open relay |
| Samba 3.0.20 | 139,445/tcp | username map script (CVE-2007-2447) |
| Apache 2.2.8 | 80/tcp | PHP CGI argument injection |
| MySQL 5.0.51a | 3306/tcp | Default/empty root password |
| PostgreSQL 8.3 | 5432/tcp | Default credentials |
| VNC | 5900/tcp | Password: password |
| Tomcat 5.5 | 8180/tcp | Default credentials |
| IRC (UnrealIRCd) | 6667/tcp | Backdoor |
| PHP 5.2.4 | — | Various vulnerabilities |
| distcc | 3632/tcp | Remote code execution |
| DVWA | 80/tcp | Multiple web vulns |
| Mutillidae | 80/tcp | OWASP Top 10 |

---

### 8.3 Metasploitable 3

**What it is:** More modern and realistic vulnerable environment. Two versions:
- **Ubuntu 14.04** — Linux target
- **Windows Server 2008** — Windows target (excellent for Windows exploitation practice)

**Requirements:** Vagrant + VirtualBox

**Setup:**
```bash
# Install Vagrant
# https://www.vagrantup.com/downloads

# Install VirtualBox (already done)

# Clone and build Metasploitable 3
git clone https://github.com/rapid7/metasploitable3.git
cd metasploitable3

# Windows version:
vagrant up win2k8  # Takes 20-45 minutes

# Ubuntu version:
vagrant up ub1404

# Access:
# Windows: vagrant rdp win2k8 OR RDP to [IP]:3389
# Ubuntu: vagrant ssh ub1404 OR ssh vagrant@[IP]
```

---

### 8.4 DVWA — Damn Vulnerable Web Application

**What it is:** A PHP/MySQL web application with intentional vulnerabilities covering OWASP Top 10.

**Vulnerabilities included:**
- Brute Force (login forms)
- Command Injection
- CSRF
- File Inclusion (LFI/RFI)
- File Upload
- Insecure CAPTCHA
- SQL Injection (Error-based, Blind)
- SQL Injection (Blind)
- Weak Session IDs
- XSS (DOM, Reflected, Stored)
- Content Security Policy bypass
- JavaScript vulnerabilities

**Security Levels:**
- **Low** — No security controls (for learning the attack)
- **Medium** — Some defenses (for learning bypasses)
- **High** — Stronger defenses (for advanced bypasses)
- **Impossible** — Secure code (for learning what good code looks like)

**Installation (on Ubuntu Server VM already configured):**
```bash
# Clone DVWA
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chown -R www-data:www-data DVWA/

# Configure database
sudo cp DVWA/config/config.inc.php.dist DVWA/config/config.inc.php
sudo nano DVWA/config/config.inc.php
# Set: $_DVWA['db_password'] = '';  (empty password for lab)
# Set: $_DVWA['default_security_level'] = 'low';

# Set PHP settings for DVWA
sudo nano /etc/php/*/apache2/php.ini
# allow_url_include = On
# allow_url_fopen = On

sudo systemctl restart apache2

# Access: http://[Ubuntu-VM-IP]/DVWA
# Setup: Click "Create / Reset Database"
# Login: admin / password
```

---

### 8.5 OWASP Juice Shop

**What it is:** A modern, realistic vulnerable web application built on Node.js. More sophisticated than DVWA.

**Challenges:** 100+ hacking challenges across OWASP Top 10 categories, with a built-in scoreboard.

**Best feature:** Reflects modern web technologies (single-page app, REST API, JWT, Angular).

**Run with Docker (easiest):**
```bash
# On any machine with Docker:
docker pull bkimminich/juice-shop
docker run -d -p 3000:3000 bkimminich/juice-shop

# Access: http://localhost:3000
```

**Or run directly on Ubuntu Server:**
```bash
sudo apt install nodejs npm -y
git clone https://github.com/juice-shop/juice-shop.git
cd juice-shop
npm install
npm start
# Access: http://[IP]:3000
```

---

### 8.6 VulnHub Machines

**VulnHub** (vulnhub.com) is a collection of **downloadable vulnerable VMs** designed for practice.

Each machine is:
- A complete VM (OVA file)
- Has a specific difficulty level
- Has CTF-style flags to capture
- Has community walkthroughs available

**Getting started:**
```
1. Browse: https://www.vulnhub.com/
2. Filter by difficulty: Easy → Medium → Hard
3. Download .ova file
4. VirtualBox → File → Import Appliance → Select .ova
5. Set networking to Host-Only
6. Start VM → Find its IP → Attack!
```

**Recommended beginner machines:**

| Machine | Difficulty | Skills Practiced |
|---|---|---|
| Kioptrix Level 1 | Very Easy | First exploits, SMB |
| Mr. Robot | Easy-Medium | Web, privilege escalation |
| Basic Pentesting 1 | Easy | Recon, web, Linux privesc |
| SickOs 1.1 | Easy-Medium | Web proxy, SUID |
| HackLAB: Vulnix | Easy-Medium | NFS, SSH, privilege escalation |
| DC: 1 | Easy | Drupal, Linux privesc |

---

### 8.7 Quick Comparison of Vulnerable Targets

| Target | Type | Focus | Difficulty | Internet Needed |
|---|---|---|---|---|
| Metasploitable 2 | VM | Network services, web | Beginner | No |
| Metasploitable 3 | VM | Windows + Linux | Beginner-Int. | Build time only |
| DVWA | Web App | OWASP Top 10 | Beginner | No |
| Juice Shop | Web App | Modern web, API | Beginner-Int. | No |
| VulnHub VMs | VM | Varied — CTF style | Varied | No |
| HackTheBox | Online | Real-world style | Int.-Advanced | Yes |
| TryHackMe | Online | Guided learning | Beginner-Int. | Yes |

---

## 9. VM Networking Deep Dive

### 9.1 Why Networking Matters in a Home Lab

Getting the networking right is critical:
- **Too open** — your vulnerable VMs are exposed to your real network
- **Too closed** — your attacker VM can't reach target VMs
- **Wrong config** — tools don't work, you troubleshoot for hours

You need to understand each network mode to architect your lab correctly.

---

### 9.2 NAT (Network Address Translation)

```
Internet ← NAT ← Host ← Hypervisor ← VM
```

**How it works:**
- VM gets a private IP from the hypervisor's built-in DHCP (usually 10.0.2.x)
- VM can access the internet through the host's IP (translated)
- **No one on the external network can reach the VM directly**
- **VMs in NAT mode cannot reach each other** (each has its own isolated NAT)

**Default gateway for NAT:** 10.0.2.2 (the host)
**Default DNS for NAT:** 10.0.2.3

**Use cases:**
- VM needs internet access (downloading updates, tools)
- Simple setup when no inter-VM communication needed
- Safe — VM is hidden from external network

**Limitation:** Two VMs both in NAT cannot communicate with each other.

---

### 9.3 NAT Network (Different from NAT!)

```
Internet ← NAT ← Host ← Hypervisor ← [VM1, VM2, VM3 — same subnet]
```

**How it works:**
- Create a virtual network: VirtualBox → File → Preferences → Network → NAT Networks
- Multiple VMs join the same NAT Network → can reach each other
- All VMs share the host's internet connection
- Still not reachable from external network

**Configuration:**
```
File → Preferences → Network → NAT Networks → Add
├── Network name: LabNetwork
├── Network CIDR: 10.0.2.0/24
└── Enable DHCP: ✅

VM Settings → Network → Adapter 1
├── Attached to: NAT Network
└── Name: LabNetwork
```

**Use cases:**
- Multiple VMs that need internet AND need to talk to each other
- Good for isolated attack labs where internet access is needed

---

### 9.4 Bridged Networking

```
Physical Network ←→ Host ←→ VM (gets its own IP on physical network)
```

**How it works:**
- VM's virtual NIC connects directly to the physical network adapter
- VM gets an IP from your router's DHCP (just like a real machine on your network)
- VM appears as a separate device on your home network
- Other devices on your network CAN see and reach the VM

**Use cases:**
- VM needs to be accessible from other devices on your physical network
- Network scanning practice against a reachable target
- When you want the VM to be treated as a real network device

**Risk:** Your vulnerable VMs are now reachable by everything on your home network. Fine for practice, but keep vulnerable VMs in Host-Only/Internal when possible.

**Configuration:**
```
VM Settings → Network → Adapter 1
├── Attached to: Bridged Adapter
└── Name: [select your physical network adapter]
```

---

### 9.5 Host-Only Networking

```
Host ←→ [Virtual Host-Only Network] ←→ VM1, VM2, VM3
(No internet — completely isolated from physical network)
```

**How it works:**
- Creates a virtual network that exists only between the host and VMs
- VMs get IPs in a private range (default: 192.168.56.x)
- VMs can communicate with each other
- VMs can communicate with the host
- **No internet access** — completely isolated

**Create a Host-Only network:**
```
VirtualBox → File → Host Network Manager → Create
├── IPv4 Address: 192.168.56.1 (host's IP on this network)
├── IPv4 Network Mask: 255.255.255.0
├── DHCP Server: Enable
│   ├── Server Address: 192.168.56.100
│   ├── Server Mask: 255.255.255.0
│   ├── Lower Address Bound: 192.168.56.101
│   └── Upper Address Bound: 192.168.56.254
```

**VM Configuration:**
```
VM Settings → Network → Adapter 1
├── Attached to: Host-Only Adapter
└── Name: vboxnet0 (or your created network)
```

**Use cases:**
- **Recommended for attack labs** — vulnerable VMs are isolated from internet and home network
- Attacker VM (Kali) + Target VMs all on same Host-Only network

---

### 9.6 Internal Network

```
VM1 ←→ [Internal Network] ←→ VM2, VM3
(Host CANNOT reach VMs — completely isolated)
```

**How it works:**
- VMs can only communicate with each other
- **Host cannot communicate with these VMs**
- No internet, no host access
- Most isolated option

**Use cases:**
- Simulating an internal corporate network
- Malware analysis — completely air-gapped
- C2 communication testing

**Configuration:**
```
VM Settings → Network → Adapter 1
├── Attached to: Internal Network
└── Name: [give it a name, e.g., "labinternal"]
```

> All VMs using the same Internal Network name can communicate with each other.

---

### 9.7 Recommended Lab Network Architecture

For a complete attack lab, use **multiple adapters per VM**:

```
Kali Linux (Attacker):
├── Adapter 1: NAT (internet access for updating tools)
└── Adapter 2: Host-Only (192.168.56.x) — attack network

Metasploitable 2 (Target):
└── Adapter 1: Host-Only (192.168.56.x) — attack network only

Windows 10 (Target):
└── Adapter 1: Host-Only (192.168.56.x) — attack network only

Ubuntu Server (Target):
├── Adapter 1: NAT (for initial package installation)
└── Adapter 2: Host-Only (192.168.56.x) — attack network
```

**Why Kali gets NAT + Host-Only:**
- NAT: Update tools, install new packages, browse security resources
- Host-Only: Attack target VMs

**Why targets only get Host-Only:**
- They cannot reach the internet (safer — malware contained)
- Still reachable by Kali for practice

---

### 9.8 Networking Cheat Sheet

| Mode | Internet | Reach Host | Reach Other VMs | External can reach VM |
|---|---|---|---|---|
| NAT | ✅ | ✅ (via port forward) | ❌ | ❌ |
| NAT Network | ✅ | ✅ (via port forward) | ✅ | ❌ |
| Bridged | ✅ | ✅ | ✅ (same subnet) | ✅ |
| Host-Only | ❌ | ✅ | ✅ | ❌ |
| Internal | ❌ | ❌ | ✅ | ❌ |

---

### 9.9 Troubleshooting Network Connectivity

```bash
# Check if VM has an IP
ip a

# Check connectivity to another VM
ping 192.168.56.101

# Check if a port is open on a target
nmap -p 22,80,445 192.168.56.101

# Check route table
ip route

# Check DNS
cat /etc/resolv.conf
nslookup google.com

# From host to VM (Host-Only)
ping 192.168.56.101  # Host can always reach Host-Only VMs
```

---

## 10. Snapshots, Cloning, Templates & Vagrant

### 10.1 Snapshots

A **snapshot** captures the **complete state** of a VM at a specific point in time — including RAM contents (if taken while running), disk state, and VM configuration.

**Why snapshots are essential in a home lab:**
- Take a snapshot **before exploiting** a target → restore instantly if something goes wrong
- Take a snapshot **after a clean installation** → always have a clean base to return to
- Take a snapshot **before installing malware** for analysis → restore after
- Chain snapshots to create a timeline of VM states

---

#### Taking a Snapshot (VirtualBox)

```
Method 1: GUI
Machine → Take Snapshot
├── Snapshot Name: "Clean Install - Pre-Attack"
├── Snapshot Description: "Fresh Metasploitable2, no modifications"
└── OK

Method 2: While VM is running
Machine menu → Take Snapshot (takes live snapshot including RAM)
→ VM continues running without interruption

Method 3: Command line (VBoxManage)
VBoxManage snapshot "Kali Linux" take "After-Update-2024" --description "Fully updated Kali"
```

---

#### Restoring a Snapshot

```
Method 1: GUI
In VM list → Right-click VM → Snapshots
Select snapshot → Restore
☑ Create a snapshot of the current machine state (save current state before restoring)
→ Restore

Method 2: Command line
VBoxManage snapshot "Metasploitable2" restore "Clean Install"
```

---

#### Snapshot Best Practices

```
Recommended snapshot strategy for each VM:

Kali Linux:
├── "01 - Fresh Install"          (right after OS install, before tools)
├── "02 - Fully Updated + Tools"  (after apt upgrade + all tools)
└── "03 - Lab Ready"              (configured networking, aliases, etc.)

Metasploitable2:
└── "01 - Clean Import"           (right after import, before any exploitation)

Windows 10:
├── "01 - Clean Install"
├── "02 - Configured for Lab"     (firewall off, RDP enabled, etc.)
└── "03 - Pre-Exercise [name]"    (before specific exercises)
```

---

### 10.2 Cloning

**Cloning** creates a complete copy of a VM.

**Two types:**
- **Full Clone** — Complete independent copy (takes same disk space as original)
- **Linked Clone** — References original's disk; only stores differences (saves space but depends on original)

**Use cases:**
- Create multiple identical targets (e.g., 3 Windows machines for lateral movement practice)
- Create a "template" VM and clone it for each student in a training environment
- Keep one clean copy and always work on a clone

```
Right-click VM → Clone
├── Clone type: Full Clone (recommended for independence)
├── Snapshots: Clone all snapshots OR Current machine state only
└── New VM name: "Windows10-Clone-1"
```

---

### 10.3 Templates (OVA Export/Import)

An **OVA (Open Virtualization Appliance)** is a portable package containing:
- VM configuration
- Virtual disk(s)
- Metadata

**Export a VM as OVA:**
```
File → Export Appliance
├── Select VM(s)
├── Format: OVF 2.0 (or Open Virtualization Format 1.0)
├── Output file: /path/to/KaliTemplate.ova
└── Product name, description (optional)
→ Export
```

**Import an OVA:**
```
File → Import Appliance → Select .ova file
→ Review settings (change MAC addresses!) → Import
```

**Use cases:**
- Share lab environments with teammates
- Import pre-built vulnerable machines from VulnHub, Offensive Security
- Backup your lab configuration

---

### 10.4 Vagrant

**Vagrant** is a tool for **building and managing virtual machine environments** in a single workflow.

Instead of clicking through a GUI, you define your entire VM in a **Vagrantfile** (text file) and run one command.

**Install Vagrant:**
```
Download: https://www.vagrantup.com/downloads
# Windows: .msi installer
# Linux:
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant -y
```

**Basic Vagrantfile example:**
```ruby
# Vagrantfile
Vagrant.configure("2") do |config|

  # Ubuntu target VM
  config.vm.define "target" do |target|
    target.vm.box = "ubuntu/jammy64"
    target.vm.hostname = "ubuntu-target"
    target.vm.network "private_network", ip: "192.168.56.101"
    target.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
    end
    target.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 openssh-server
    SHELL
  end

  # Kali attacker VM (requires vagrant box)
  config.vm.define "attacker" do |attacker|
    attacker.vm.box = "kalilinux/rolling"
    attacker.vm.hostname = "kali-attacker"
    attacker.vm.network "private_network", ip: "192.168.56.10"
    attacker.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

end
```

**Vagrant commands:**
```bash
vagrant up              # Create and start all VMs
vagrant up target       # Start only 'target' VM
vagrant ssh target      # SSH into 'target' VM
vagrant halt            # Stop all VMs
vagrant destroy         # Delete all VMs
vagrant status          # Show VM states
vagrant snapshot save target "clean"    # Take snapshot
vagrant snapshot restore target "clean" # Restore snapshot
```

**Why use Vagrant?**
- Infrastructure as code — lab is reproducible from text file
- Fast provisioning — `vagrant up` builds your whole lab automatically
- Version control your lab config in Git
- Community boxes — thousands of pre-built boxes at vagrantcloud.com
- Used by Metasploitable 3 and many security lab projects

---

## 11. Docker for Security

### 11.1 Containers vs VMs — Core Difference

```
Virtual Machines:                    Containers:
┌──────────────┐                     ┌──────────────┐
│  App         │                     │  App         │
├──────────────┤                     ├──────────────┤
│  Guest OS    │  ← Full OS          │  Libs/Deps   │  ← No OS
├──────────────┤     (GBs)           ├──────────────┤     (MBs)
│  Hypervisor  │                     │  Container   │
├──────────────┤                     │  Runtime     │
│  Host OS     │                     ├──────────────┤
├──────────────┤                     │  Host OS     │
│  Hardware    │                     ├──────────────┤
└──────────────┘                     │  Hardware    │
                                     └──────────────┘
```

| Feature | VMs | Containers |
|---|---|---|
| Isolation | Full (separate kernel) | Process-level (shared kernel) |
| Startup time | 30 seconds – 5 minutes | Milliseconds – seconds |
| Size | GBs | MBs |
| Performance | Near-native with overhead | Near-native |
| Security isolation | Strong | Weaker (shared kernel) |
| Use case | Full OS needed, strong isolation | App deployment, microservices |

**Key point for security:** Containers provide weaker isolation than VMs. A container escape vulnerability can give an attacker access to the host. This is why **you should NEVER run untrusted malware in a container** — use a full VM.

---

### 11.2 Docker Fundamentals

**Docker** is the most widely used container platform.

**Core concepts:**

| Concept | Definition |
|---|---|
| **Image** | A read-only template used to create containers (like a VM template) |
| **Container** | A running instance of an image (like a running VM) |
| **Dockerfile** | Instructions to build an image |
| **Registry** | Repository of images (Docker Hub = public registry) |
| **Volume** | Persistent storage that survives container deletion |
| **Network** | Virtual network connecting containers |

---

### 11.3 Installing Docker

```bash
# Ubuntu/Debian/Kali:
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker

# Add your user to docker group (no sudo needed):
sudo usermod -aG docker $USER
newgrp docker  # Apply immediately without logout

# Verify:
docker --version
docker run hello-world

# Docker Compose (for multi-container apps):
sudo apt install docker-compose -y
```

---

### 11.4 Essential Docker Commands

```bash
# === IMAGES ===
docker images                          # List local images
docker pull ubuntu:22.04               # Download image
docker pull kalilinux/kali-rolling     # Pull Kali image
docker rmi ubuntu:22.04                # Remove image
docker search nginx                    # Search Docker Hub

# === CONTAINERS ===
docker ps                              # List running containers
docker ps -a                           # List all containers (including stopped)
docker run ubuntu:22.04                # Create + start container (exits immediately)
docker run -it ubuntu:22.04 bash       # Interactive terminal
docker run -d nginx                    # Run in background (detached)
docker run -d -p 8080:80 nginx         # Map host port 8080 → container port 80
docker run --name mycontainer nginx    # Give container a name
docker stop mycontainer                # Stop container
docker start mycontainer               # Start stopped container
docker rm mycontainer                  # Delete container
docker logs mycontainer                # View container logs
docker exec -it mycontainer bash       # Shell into running container

# === VOLUMES ===
docker run -v /host/path:/container/path ubuntu  # Mount host directory

# === NETWORKING ===
docker network ls                      # List networks
docker network create labnet           # Create network
docker run --network labnet ubuntu     # Connect container to network

# === CLEANUP ===
docker system prune                    # Remove all stopped containers + unused images
docker system prune -a                 # Remove everything unused
```

---

### 11.5 Docker for Security — Key Use Cases

#### Running Vulnerable Web Apps

```bash
# DVWA
docker pull vulnerables/web-dvwa
docker run -d -p 80:80 vulnerables/web-dvwa
# Access: http://localhost/
# Login: admin / password

# OWASP Juice Shop
docker pull bkimminich/juice-shop
docker run -d -p 3000:3000 bkimminich/juice-shop
# Access: http://localhost:3000

# WebGoat (OWASP learning app)
docker pull webgoat/goat-and-wolf
docker run -d -p 8080:8080 -p 9090:9090 webgoat/goat-and-wolf
# Access: http://localhost:8080/WebGoat

# OWASP NodeGoat
docker-compose -f https://raw.githubusercontent.com/OWASP/NodeGoat/master/docker-compose.yml up -d
```

---

#### Running Security Tools in Docker

```bash
# Metasploit Framework (without full Kali)
docker pull metasploitframework/metasploit-framework
docker run -it metasploitframework/metasploit-framework

# Nmap
docker pull instrumentisto/nmap
docker run --rm instrumentisto/nmap -sV 192.168.56.101

# Nuclei (vulnerability scanner)
docker pull projectdiscovery/nuclei
docker run -it projectdiscovery/nuclei -u https://target.com

# OWASP ZAP
docker pull zaproxy/zap-stable
docker run -u zap -p 8080:8080 zaproxy/zap-stable zap.sh -daemon -port 8080 -host 0.0.0.0

# Ghidra (reverse engineering)
docker pull fedoraproject/ghidra
```

---

#### Docker Compose for Multi-Container Labs

A `docker-compose.yml` file defines a full multi-container lab:

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Web application
  webapp:
    image: vulnerables/web-dvwa
    ports:
      - "80:80"
    networks:
      - labnet

  # Database
  database:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: toor
      MYSQL_DATABASE: dvwa
    networks:
      - labnet

  # Attacker tools
  attacker:
    image: kalilinux/kali-rolling
    stdin_open: true
    tty: true
    networks:
      - labnet
    command: /bin/bash

networks:
  labnet:
    driver: bridge
```

```bash
# Start everything:
docker-compose up -d

# Stop everything:
docker-compose down
```

---

### 11.6 Docker Security Concerns

Understanding Docker's security model is important for both offensive and defensive security.

**Container Escape Vulnerabilities:**
- **CVE-2019-5736 (runc)** — Container process could overwrite runc binary → host takeover
- **Dirty COW + Docker** — Privilege escalation from container to host
- **Kubernetes privilege escalation** — Misconfigured K8s can expose host

**Common Docker Misconfigurations (attack perspective):**

```bash
# Dangerous: Running as root (default if not configured)
docker run ubuntu whoami  # → root

# Dangerous: Privileged container (full host capabilities)
docker run --privileged ubuntu  # Nearly unrestricted host access

# Dangerous: Mounting Docker socket inside container
docker run -v /var/run/docker.sock:/var/run/docker.sock ubuntu
# → Container can control ALL other containers AND the host

# Dangerous: Mounting entire host filesystem
docker run -v /:/host ubuntu  # Container sees entire host

# Find exposed Docker APIs (for red teamers):
nmap -p 2375,2376 [target]  # Docker API ports
```

**Docker Hardening (defensive):**
```bash
# Run as non-root user
docker run --user 1000:1000 ubuntu

# Read-only filesystem
docker run --read-only ubuntu

# Drop all capabilities, add only what's needed
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx

# No new privileges
docker run --security-opt no-new-privileges ubuntu

# Limit resources
docker run --memory="256m" --cpus="0.5" ubuntu

# Use non-root in Dockerfile:
# FROM ubuntu
# RUN useradd -u 1000 appuser
# USER appuser
```

---

## 12. Building Your Complete Home Lab

### 12.1 Final Lab Architecture

Here is the complete recommended home lab setup for this course:

```
┌─────────────────────────────────────────────────────────┐
│                    HOST MACHINE                          │
│              (Your physical computer)                    │
│                                                         │
│  ┌──────────────────────────────────────────────────┐   │
│  │              VirtualBox / VMware                 │   │
│  │                                                  │   │
│  │  ┌─────────────────┐    Host-Only Network        │   │
│  │  │   KALI LINUX    │    192.168.56.0/24          │   │
│  │  │   (Attacker)    │◄──────────────────────────  │   │
│  │  │  192.168.56.10  │    ┌──────────────────┐    │   │
│  │  │  + NAT (web)    │    │  Windows 10/11   │    │   │
│  │  └─────────────────┘    │    (Target)      │    │   │
│  │           │             │  192.168.56.20   │    │   │
│  │           │             └──────────────────┘    │   │
│  │           │             ┌──────────────────┐    │   │
│  │           └────────────►│  Ubuntu Server   │    │   │
│  │                         │    (Target)      │    │   │
│  │                         │  192.168.56.30   │    │   │
│  │                         └──────────────────┘    │   │
│  │                         ┌──────────────────┐    │   │
│  │                         │  Metasploitable2 │    │   │
│  │                         │    (Target)      │    │   │
│  │                         │  192.168.56.40   │    │   │
│  │                         └──────────────────┘    │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

**VM Summary:**

| VM | Role | RAM | Storage | Network |
|---|---|---|---|---|
| Kali Linux | Attacker | 4 GB | 80 GB | NAT + Host-Only |
| Windows 10 | Target (Windows) | 4 GB | 60 GB | Host-Only only |
| Ubuntu Server | Target (Linux) | 2 GB | 30 GB | Host-Only only |
| Metasploitable 2 | Vulnerable target | 512 MB | 10 GB | Host-Only only |
| **Total** | | **~10.5 GB** | **~180 GB** | |

> ✅ **16 GB RAM host can run Kali + one target comfortably. 32 GB RAM can run all 4 simultaneously.**

---

### 12.2 Resource Management Tips

```
Manage RAM usage:
├── Run only the VMs you need for the current exercise
├── Close unused VMs (Kali running + target VM only)
├── Save VM state instead of shutting down (resumes faster)
└── Use Linux targets over Windows when possible (less RAM)

Manage Storage:
├── Enable dynamic disk allocation (VMs only use what they fill)
├── Store large VMs on external SSD if needed
├── Delete snapshots you no longer need
├── Compact VMs periodically:
│   VBoxManage modifymedium disk "/path/to/disk.vdi" --compact
└── Avoid saving RAM in snapshots unless needed (doubles snapshot size)

Manage CPU:
├── Limit each VM to 1-2 vCPUs unless needed
├── Don't run more VMs than you have physical cores
└── Suspend VMs not in use
```

---

### 12.3 Lab Documentation

Start documenting your lab from day one. This becomes:
- Your **portfolio** for job applications
- Your **reference** when you forget how something works
- Your **evidence** of skills for interviewers

**What to document:**
```markdown
# Home Lab Build Log

## Environment
- Host OS: Windows 11 Pro
- Hypervisor: VirtualBox 7.0
- RAM: 16 GB / Storage: 512 GB SSD

## VMs
| VM | OS | Purpose | IP | Credentials |
|----|----|---------|----|-------------|
| Kali | Kali Linux 2024.1 | Attacker | 192.168.56.10 | kali/kali |
| Win10 | Windows 10 Enterprise | Target | 192.168.56.20 | labuser/Password1 |
...

## Exercises Completed
- [ ] Ping sweep on lab network
- [ ] Full Nmap scan of Metasploitable2
- [ ] Exploit vsftpd 2.3.4 backdoor
...

## Findings / Notes
[Document what you learned, what worked, what didn't]
```

---

## 13. Hands-On Lab: Full Home Lab Build

### Lab Overview

In this lab you will build a complete, functional cybersecurity home lab with 4 VMs and verify connectivity.

**Estimated time:** 3–5 hours (most time is download + installation)

---

### Phase 1: Install VirtualBox

```
1. Download VirtualBox from virtualbox.org
2. Download Extension Pack (same version)
3. Install VirtualBox → Install Extension Pack
4. Launch VirtualBox → verify it opens correctly
```

**Verify:**
```bash
VBoxManage --version
# Should output: 7.x.x
```

---

### Phase 2: Configure Host-Only Network

```
VirtualBox → File → Host Network Manager → Create
Configure:
├── IPv4 Address: 192.168.56.1
├── IPv4 Network Mask: 255.255.255.0
├── DHCP Server: Enabled
│   ├── Server Address: 192.168.56.100
│   ├── Lower bound: 192.168.56.101
│   └── Upper bound: 192.168.56.254
→ Apply → Close
```

**Verify from host:**
```bash
# Windows:
ipconfig | findstr "192.168.56"
# Should show: 192.168.56.1

# Linux:
ip a show vboxnet0
```

---

### Phase 3: Deploy Kali Linux

```
Option A (Fastest): Download pre-built OVA from kali.org/get-kali
→ File → Import Appliance → Select OVA → Import
→ Settings → Network → Adapter 2 → Host-Only → vboxnet0

Option B: Install from ISO (see Section 5.3)
```

**Post-import:**
```bash
# Start Kali VM
# Login: kali / kali
# Change password:
passwd

# Update:
sudo apt update && sudo apt full-upgrade -y

# Check network interfaces:
ip a
# Should see: eth0 (NAT: 10.0.2.x) and eth1 (Host-Only: 192.168.56.x)

# Take snapshot:
# Machine → Take Snapshot → "Kali - Post Update - Clean"
```

---

### Phase 4: Deploy Metasploitable 2

```
1. Download from sourceforge.net/projects/metasploitable/
2. Extract .zip → Get Metasploitable.vmdk
3. New VM in VirtualBox:
   ├── Name: Metasploitable2
   ├── OS: Linux / Ubuntu 64-bit
   ├── RAM: 512 MB
   └── Hard Disk: Existing disk → select .vmdk
4. Settings → Network → Adapter 1 → Host-Only → vboxnet0
5. Start VM
```

**Verify:**
```bash
# Inside Metasploitable2:
# Login: msfadmin / msfadmin
ifconfig eth0
# Note IP: should be 192.168.56.xxx

# From Kali, ping Metasploitable:
ping 192.168.56.[metasploitable-ip]
# Should get responses

# Take snapshot:
# Machine → Take Snapshot → "Metasploitable2 - Clean"
```

---

### Phase 5: Deploy Windows 10

```
1. Download Windows 10 Evaluation ISO or pre-built VM
2. Create VM (Section 6.3)
3. Install Windows (Section 6.4)
4. Configure for lab (Section 6.5)
5. Install Guest Additions
6. Settings → Network → Adapter 1 → Host-Only only
7. Note IP: ipconfig in Command Prompt
```

**Verify:**
```bash
# From Kali:
ping 192.168.56.[windows-ip]
nmap -p 445 192.168.56.[windows-ip]  # Should show SMB open
```

---

### Phase 6: Deploy Ubuntu Server

```
1. Download Ubuntu Server 22.04 ISO
2. Create VM (Section 7.3)
3. Install with OpenSSH enabled (Section 7.4)
4. Configure for lab (Section 7.5)
5. Note IP: ip a
```

**Verify:**
```bash
# From Kali:
ssh labadmin@192.168.56.[ubuntu-ip]
# Should connect successfully
```

---

### Phase 7: Network Verification

Run these from Kali to verify your entire lab network:

```bash
# 1. Full sweep — discover all VMs on lab network
nmap -sn 192.168.56.0/24
# Should show: 192.168.56.1 (host), Kali, Metasploitable, Windows, Ubuntu

# 2. Quick service scan on each target
nmap -F 192.168.56.[metasploitable-ip]
# Should show: 21,22,23,25,80,139,445,3306,5432,5900 open

nmap -F 192.168.56.[windows-ip]
# Should show: 135,139,445,3389 open (at minimum)

nmap -F 192.168.56.[ubuntu-ip]
# Should show: 22,80 open (minimum)

# 3. Verify web services
curl http://192.168.56.[metasploitable-ip]
curl http://192.168.56.[ubuntu-ip]

# 4. Verify SSH
ssh msfadmin@192.168.56.[metasploitable-ip]
# password: msfadmin

ssh labadmin@192.168.56.[ubuntu-ip]
```

---

### Phase 8: Install DVWA on Ubuntu Server

```bash
# On Ubuntu Server (via SSH from Kali):
sudo apt install php php-mysqli php-gd libapache2-mod-php -y
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chown -R www-data:www-data DVWA/
sudo cp DVWA/config/config.inc.php.dist DVWA/config/config.inc.php

# Edit config
sudo nano DVWA/config/config.inc.php
# Set: $_DVWA['db_password'] = '';

# Enable PHP settings
sudo sed -i "s/allow_url_include = Off/allow_url_include = On/" /etc/php/*/apache2/php.ini
sudo systemctl restart apache2

# From Kali browser:
firefox http://192.168.56.[ubuntu-ip]/DVWA
# Click "Create / Reset Database"
# Login: admin / password
```

---

### Phase 9: Take Final Snapshots

```
Take a "Lab Ready" snapshot on ALL VMs:

Machine → Take Snapshot → "LAB READY - [date]"

This is your restore point before any exercise.
Before any new exercise: restore to "LAB READY"
After completing an exercise: restore to "LAB READY" for next one
```

---

### Lab Deliverable

Create `lab_08_home_lab_documentation.md` with:

```markdown
# My Home Lab Documentation

## Build Date: [date]

## Host Specs
- OS: [your OS]
- RAM: [GB]
- Storage: [GB]
- Hypervisor: [VirtualBox version]

## Network: 192.168.56.0/24

## VM Inventory
| VM | Purpose | IP | Credentials | Snapshot |
|----|---------|----|-----------  |---------|
| Kali | Attacker | | | |
| Metasploitable2 | Linux target | | | |
| Windows 10 | Windows target | | | |
| Ubuntu Server | Linux server | | | |

## Connectivity Verification
- [ ] Kali can ping all targets
- [ ] Nmap discovers all VMs
- [ ] SSH works to Metasploitable2
- [ ] SSH works to Ubuntu Server
- [ ] DVWA accessible in browser
- [ ] RDP works to Windows 10
- [ ] All VMs have clean snapshots

## Nmap Output (paste discovery scan result)
[paste nmap -sn 192.168.56.0/24 output here]

## Notes / Issues Encountered
[document anything that didn't work as expected and how you fixed it]
```

---

## 14. Summary & Key Takeaways

### What You Learned in Module 8

**Virtualization Fundamentals:**
- Type 1 hypervisors (ESXi, Proxmox, KVM) run on bare metal — better performance
- Type 2 hypervisors (VirtualBox, VMware Workstation) run on host OS — easier for home labs
- VirtualBox is free and cross-platform — ideal for this course
- Key terms: host, guest, vCPU, snapshot, clone, template, OVA

**Hypervisor Options:**
- VirtualBox — free, cross-platform, best for beginners
- VMware Workstation Pro — now free, better performance
- Proxmox — Type 1, free, best for dedicated lab machines
- QEMU/KVM — Linux-native, enterprise-grade, steeper learning curve

**VM Setup:**
- Kali Linux — pre-built OVA is fastest; always change default credentials; update immediately
- Windows 10/11 — Evaluation ISOs are free and legal; disable security features for target VMs
- Ubuntu Server — lightweight; enable SSH during install; install DVWA for web practice

**Vulnerable Targets:**
- Metasploitable 2 — dozens of exploitable services; never expose to real network
- Metasploitable 3 — more realistic; Windows + Linux; uses Vagrant
- DVWA — OWASP Top 10 in a web app; 4 security levels
- Juice Shop — modern Node.js web app; 100+ challenges
- VulnHub — downloadable CTF-style VMs

**VM Networking:**
- NAT — internet only; VMs isolated from each other
- NAT Network — internet + VMs talk to each other
- Bridged — VM on physical network; other devices can reach it
- Host-Only — VMs talk to each other + host; no internet; **best for attack labs**
- Internal — VMs only; host can't reach; most isolated

**Snapshots & Management:**
- Take snapshots before every exercise; restore after
- Clone VMs to create multiple identical targets
- OVA export/import for portability and sharing
- Vagrant for reproducible infrastructure-as-code lab setups

**Docker:**
- Containers share host kernel — faster but weaker isolation than VMs
- Never run malware in containers — use full VMs
- Docker useful for running web apps (DVWA, Juice Shop) and tools (Metasploit, Nmap)
- Key security risks: privileged containers, mounted Docker socket, running as root

---

## 15. Quiz: Test Your Knowledge

1. What is the key difference between a Type 1 and Type 2 hypervisor?

2. Which VirtualBox network mode allows multiple VMs to communicate with each other AND access the internet?

3. You are about to practice exploiting Metasploitable 2. Which VirtualBox network mode should you use for Metasploitable — and why?

4. What are the default credentials for Metasploitable 2, and what should you do immediately after importing the Kali pre-built image?

5. What does a VirtualBox snapshot capture, and why are snapshots critical in a home lab?

6. What is the difference between a Full Clone and a Linked Clone?

7. Name four types of vulnerabilities available in DVWA.

8. What is the key security risk of mounting the Docker socket (`/var/run/docker.sock`) inside a container?

9. What is the main advantage of containers over VMs? What is the main security disadvantage?

10. What is Vagrant, and why would a security professional use it instead of manually creating VMs through a GUI?

11. Your Kali VM can ping the host (192.168.56.1) but cannot ping the Windows target VM (192.168.56.20). Both are configured as Host-Only. What are two things you would check?

12. Why should vulnerable target VMs (Metasploitable, DVWA, etc.) NEVER be placed on a Bridged network adapter in a home lab?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Type 1** runs directly on hardware (no host OS) — used in enterprise. **Type 2** runs as an application on top of a host OS — used for home labs. Type 1 has better performance; Type 2 is easier to set up.

2. **NAT Network** — VMs share a common virtual network with internet access and can communicate with each other. (Regular NAT gives internet but each VM is isolated.)

3. **Host-Only** — Metasploitable is a vulnerable machine that should never be exposed to the internet or your real home network. Host-Only ensures it's only reachable by your Kali VM and host.

4. Default credentials: **msfadmin / msfadmin** (Metasploitable) and **kali / kali** (pre-built Kali). You should **change the Kali password immediately** with the `passwd` command.

5. A snapshot captures the **complete VM state** (disk, RAM if running, configuration) at a point in time. They're critical because they let you **restore to a clean state instantly** after testing, exploitation, or misconfiguration.

6. **Full Clone** — completely independent copy of the VM (same disk space). **Linked Clone** — only stores differences from the original (saves space but depends on the original VM existing).

7. Any four of: Brute Force, Command Injection, CSRF, File Inclusion (LFI/RFI), File Upload, SQL Injection, Blind SQL Injection, XSS (Reflected, Stored, DOM), Weak Session IDs, Insecure CAPTCHA.

8. Mounting the Docker socket gives the container **full control over the Docker daemon** — it can start new privileged containers, mount the entire host filesystem, and effectively **escape the container and own the host**.

9. **Advantage:** Containers start in milliseconds, use MBs of space (vs GBs for VMs), and have near-native performance. **Disadvantage:** Containers share the host kernel — a kernel exploit or container escape gives access to the host. VMs have a full separate kernel and stronger isolation.

10. Vagrant is a tool that defines VM environments in a text file (Vagrantfile) and builds them with one command (`vagrant up`). Security professionals use it for **reproducible, version-controlled lab environments** — the entire lab is defined in code, can be rebuilt from scratch, shared with teammates, and committed to Git.

11. Check: (1) **Both VMs are attached to the same Host-Only network adapter** (e.g., both on `vboxnet0`). (2) **Windows Firewall is not blocking ICMP** — disable the firewall on the Windows target for lab use.

12. A Bridged adapter puts the VM **directly on your physical network** — your router assigns it an IP, and every device on your home network (including anyone connected to your Wi-Fi) can reach the vulnerable machine. This exposes serious vulnerabilities to your entire home network.

</details>

---

## 16. Resources

### Official Documentation
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [Kali Linux Documentation](https://www.kali.org/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [Vagrant Documentation](https://developer.hashicorp.com/vagrant/docs)
- [Proxmox VE Documentation](https://pve.proxmox.com/wiki/Main_Page)

### Downloads
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Kali Linux Pre-built VMs](https://www.kali.org/get-kali/#kali-virtual-machines)
- [Windows 10 Evaluation](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise)
- [Ubuntu Server](https://ubuntu.com/download/server)
- [Metasploitable 2](https://sourceforge.net/projects/metasploitable/)
- [VulnHub](https://www.vulnhub.com/)

### Vulnerable Applications
- [DVWA GitHub](https://github.com/digininja/DVWA)
- [Juice Shop GitHub](https://github.com/juice-shop/juice-shop)
- [Metasploitable 3 GitHub](https://github.com/rapid7/metasploitable3)

### Learning Platforms (Complement Your Lab)
- [TryHackMe](https://tryhackme.com/) — Guided rooms, beginner-friendly
- [HackTheBox](https://www.hackthebox.com/) — More advanced, real-world style
- [PortSwigger Web Academy](https://portswigger.net/web-security) — Best for web application security

### Community Help
- [r/homelab](https://www.reddit.com/r/homelab/) — General home lab community
- [r/netsec](https://www.reddit.com/r/netsec/) — Network security discussion
- [VirtualBox Forums](https://forums.virtualbox.org/)
- [Kali Linux Forums](https://forums.kali.org/)

---

## Module Progress Checklist

Before moving to Module 9, confirm you can:

- [ ] Explain the difference between Type 1 and Type 2 hypervisors with examples
- [ ] Describe all 5 VirtualBox network modes and when to use each
- [ ] Have Kali Linux installed and updated in a VM
- [ ] Have at least one target VM (Metasploitable 2 recommended) running
- [ ] Confirm Kali can reach target VMs via ping
- [ ] Confirm Nmap discovery scan shows all lab VMs
- [ ] Have DVWA accessible in browser from Kali
- [ ] Have clean "Lab Ready" snapshots on all VMs
- [ ] Document your lab setup in a markdown file
- [ ] Understand the difference between Docker containers and VMs
- [ ] Know the security risks of misconfigured Docker containers

---

**Previous Module:** [← Module 7 — Linux Security & Administration](../Module_07_Linux_Security.md)

**Next Module:** [Module 9 — OSINT Part 1 →](../Module_09_OSINT_Part1.md)

---

*Module 8 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
