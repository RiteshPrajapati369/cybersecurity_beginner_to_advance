# Module 6: Windows Security & Administration

> **Phase 1 — Absolute Foundations**
> 🟢 Difficulty: Intermediate | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `06_WINDOWS_SECURITY`

---

## Table of Contents

1. [Windows Architecture](#1-windows-architecture)
2. [Windows Registry](#2-windows-registry)
3. [Windows Services](#3-windows-services)
4. [CMD and PowerShell for Security](#4-cmd-and-powershell-for-security)
5. [Windows Permissions](#5-windows-permissions)
6. [Windows Security Features](#6-windows-security-features)
7. [Windows Logging](#7-windows-logging)
8. [Active Directory (AD) Foundations](#8-active-directory-ad-foundations)
9. [Hands-On Lab: Audit Windows Security & Extract Event Logs](#9-hands-on-lab-audit-windows-security--extract-event-logs)
10. [Summary & Key Takeaways](#10-summary--key-takeaways)
11. [Quiz: Test Your Knowledge](#11-quiz-test-your-knowledge)
12. [Resources](#12-resources)

---

## 1. Windows Architecture

Windows OS architecture is a highly structured, dual-mode system designed to provide maximum stability, security, and hardware isolation. It is split into two primary operational modes: **User Mode** and **Kernel Mode**.

```
+-------------------------------------------------------+
|                      USER MODE                        |
|   [ Applications ]   [ Subsystems ]   [ ntdll.dll ]   |
+-------------------------------------------------------+
                           ||  (System Calls / Sysenter)
                           \/
+-------------------------------------------------------+
|                     KERNEL MODE                       |
|   [ Executive Services ]       [ Windows Kernel ]     |
|   [ Device Drivers ]           [ HAL (hal.dll) ]      |
+-------------------------------------------------------+
                           ||
                           \/
                  [ Physical Hardware ]

```

---

### 1.1 User Mode vs. Kernel Mode

The separation between User Mode and Kernel Mode forms the core security boundary of the Windows operating system.

#### User Mode (Ring 3)

The execution environment where standard applications, user services, and environment subsystems run.

* **Goal:** Provide an isolated, non-privileged environment for user applications.
* **Characteristics:** * Every application runs inside its own **isolated virtual memory space**.
* Applications cannot directly access physical hardware or core system memory.
* If a user-mode application crashes, it does not affect other processes or crash the entire OS.


* **Accessing Resources:** To interact with hardware or access protected files, user-mode applications must issue a **System Call**, passing control over to the Kernel Mode via the standard API interface.

#### Kernel Mode (Ring 0)

The privileged execution environment where the core components of the operating system, low-level drivers, and the kernel itself run.

* **Goal:** Provide unrestricted, direct access to physical hardware, system memory, and CPU execution cycles.
* **Characteristics:**
* All code executing in Kernel Mode shares a **single virtual address space**.
* It executes highly privileged instructions directly on the CPU.
* If an unhandled error or crash occurs inside a kernel-mode module or third-party driver, it compromises system integrity, leading to a system-wide crash known as the **BSOD (Blue Screen of Death)**.



---

### 1.2 Core Architectural Components

The Windows architecture consists of several interdependent layers that handle requests from the user interface down to the physical hardware.

#### Subsystems

Environment subsystems act as an intermediary layer that exposes specific OS personalities to applications.

* **Win32 Subsystem:** The primary subsystem in Windows (_csrss.exe_ and _win32k.sys_). It manages the graphical user interface (GUI), controls windows management, captures user inputs (keyboard/mouse), and handles basic OS environment options.

#### ntdll.dll

The lowest layer of User Mode. It serves as a bridge between user-space applications and the Kernel.

* **Function:** When an application calls a standard Windows API function, _ntdll.dll_ translates that request into a specific **Native API** format and uses hardware instructions (like _sysenter_ or _syscall_) to transition the request into Kernel Mode.

#### Executive Services

The core structural building block of Kernel Mode, containing specialized modules that manage system resources:

* **Object Manager:** Creates, manages, tracks, and secures Windows resources (such as files, threads, processes, and registry keys) by representing them as uniform objects.
* **Process Manager:** Controls the lifecycle of execution, handling the creation, management, and termination of processes and threads.
* **Security Reference Monitor (SRM):** The primary engine for Windows security. It enforces access control lists (ACLs), validates user access tokens during resource requests, and performs runtime authorization checks.
* **Memory Manager:** Implements virtual memory management, handles allocation/deallocation of physical and virtual memory spaces, and controls hardware paging tables.

#### Windows Kernel

Positioned directly beneath the Executive Services, the core kernel performs low-level operating system duties. It manages CPU scheduling, thread dispatching, interrupt handling, and multiprocessor synchronization. It is designed around a hybrid micro-kernel architecture to balance speed and modularity.

#### HAL (Hardware Abstraction Layer)

A low-level, loadable kernel module (_hal.dll_) that sits directly between the physical hardware and the rest of the operating system.

* **Function:** It hides platform-specific hardware differences (such as motherboard and chipset variations) from the OS core. This isolation allows the core Windows kernel, executive services, and drivers to run unmodified across varying hardware setups.

---

## 2. Windows Registry

The Windows Registry is a **centralized, hierarchical database** used by the operating system to store low-level configurations for the hardware, software drivers, system preferences, and user settings.

In simple terms: it acts as the brain's memory bank for the OS, dictating exactly how Windows behaves and interacts with applications.

```
                  [ My Computer / Desktop ]
                              |
       +----------------------+----------------------+
       |                                             |
[ HKEY_LOCAL_MACHINE ]                      [ HKEY_CURRENT_USER ]
       |                                             |
  +----+----+                                   +----+----+
  |         |                                   |         |
[SAM]   [SYSTEM]                            [Software] [Environment]

```

---

### 2.1 Registry Structure: Hives and Keys

The registry is organized in a tree-like structure similar to folders and files in a file system.

* **Hives (Root Keys):** The top-level folders, always prefixed with **_HKEY__**. These represent the primary logical divisions of the database.
* **Keys and Subkeys:** Equivalent to folders and subfolders. They organize configuration options into specific system domains.
* **Values:** Equivalent to files. These are the actual data entries containing configuration instructions, consisting of a **Name**, a **Data Type**, and the **Data** itself.

---

### 2.2 The 5 Root Hives (HKEYs)

While navigating the Registry Editor (_regedit.exe_), you will encounter five primary root hives. Memorize their distinct roles:

| Root Hive | Short Name | Purpose & Security Relevance |
| --- | --- | --- |
| **HKEY_CLASSES_ROOT** | **_HKCR_** | Stores file associations and COM (Component Object Model) class registrations. Explored by attackers for **file extension hijacking**. |
| **HKEY_CURRENT_USER** | **_HKCU_** | Stores configuration specific to the *currently logged-in user* (desktop wallpaper, environment variables, user software preferences). |
| **HKEY_LOCAL_MACHINE** | **_HKLM_** | Contains system-wide configurations for hardware, drivers, and security settings. Applies to *all users* and requires administrative privileges to modify. |
| **HKEY_USERS** | **_HKU_** | Contains individual user profile configurations for all loaded accounts on the system. **_HKCU_** is simply a dynamic link to the current user's SID inside _HKU_. |
| **HKEY_CURRENT_CONFIG** | _**HKCC**_ | Stores volatile hardware profile information gathered at system bootup (linked to _HKLM\SYSTEM\CurrentControlSet_). |

---

### 2.3 Critical HKLM Subhives

Within `HKEY_LOCAL_MACHINE` _**(HKLM)**_, there are essential subhives that security professionals and attackers monitor closely:

#### SAM (Security Accounts Manager)

* **Path:** _**HKLM\SAM**_
* **Purpose:** Stores local user account credentials, group mappings, and password hashes (LM/NTLM hashes).
* **Security Context:** This hive is locked by the system boot process. Attackers target this hive via memory dumping tools (like LSASS dumps) or backup extractions to perform **credential dumping**.

#### SYSTEM

* **Path:** _**HKLM\SYSTEM**_
* **Purpose:** Contains data regarding system startup, device driver configurations, and the _**CurrentControlSet**_ (which controls active system services).

#### SOFTWARE

* **Path:** **_HKLM\SOFTWARE**_
* **Purpose:** Stores configuration data for the operating system software and installed third-party applications. Attackers often abuse application paths here to achieve persistence.

---

### 2.4 Common Cyber Attacks Targeting the Registry

Because the Registry dictates how the systemboots and executes files, it is heavily targeted during post-exploitation phases.

#### 1. Persistence Mechanisms (Run Keys)

Attackers modify specific registry keys to ensure their malware automatically executes whenever the system boots or a user logs in.

* **Key Paths:** * `HKLM\Software\Microsoft\Windows\CurrentVersion\Run` (System-wide autorun)
* `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` (Current user autorun)


* **The Threat:** Adding a malicious executable path to these keys guarantees the malware maintains a persistent foothold.

#### 2. UAC Bypass (User Account Control)

* **The Threat:** Attackers alter registry paths associated with trusted Windows binaries (such as _**fodhelper.exe**_ or _**computerdefaults.exe**_) within `HKCU\Software\Classes\ms-settings\shell\open\command`. This allows them to execute administrative commands without triggering a UAC prompt.

#### 3. Registry-Based Malware (Fileless Attacks)

* **The Threat:** Advanced persistent threats (APTs) store entire malicious payloads (like encrypted PowerShell scripts) inside custom registry values. When a legitimate system process reads the value, it executes the code directly in memory, leaving **no file artifacts** on the hard drive for traditional antivirus tools to scan.

---

## 3. Windows Services

Windows Services are **long-running executable applications** that operate in the background. They run without a user interface and can start executing before any user logs into the system.

In simple terms: they are the background workers of the OS, handling tasks like network connections, web printing, anti-virus scanning, and system updates.

```
+-------------------------------------------------------+
|             Service Control Manager (SCM)             |
|                    (services.exe)                     |
+-------------------------------------------------------+
                           ||
        +------------------+------------------+
        |                                     |
        \/                                    \/
 [ svchost.exe ]                       [ Standalone Exe ]
 (Shared Process)                     (Dedicated Process)
  |--> RPCSS                           |--> Cybereason.exe
  |--> Winmgmt                         |--> MsMpEng.exe

```

---

### 3.1 The Service Control Manager (SCM)

The **Service Control Manager** (***services.exe***) is the core management component of the Windows service subsystem. It acts as the traffic controller for all background processes.

* **Goal:** Manage the startup, shutdown, configuration, and status tracking of all system services.
* **Database:** The SCM reads configuration data directly from the Windows Registry path: ***HKLM\SYSTEM\CurrentControlSet\Services***.
* **Interaction:** Security administrators interact with the SCM using GUI utilities like **Services snap-in** (***services.msc***), or command-line interfaces such as ***sc.exe*** and PowerShell cmdlets (e.g., ***Get-Service***).

---

### 3.2 Service Hosting Models

Windows executes services using two primary process models:

#### 1. Shared Processes (*svchost.exe*)

To conserve system resources, Windows groups multiple low-level internal services into a single execution container called **Service Host** (***svchost.exe***).

* **Characteristics:** Individual DLL-based services are loaded dynamically into a shared instance of ***svchost.exe***.
* **Security Relevance:** Because dozens of legitimate services use ***svchost.exe***, malicious actors frequently hide malware inside fake process names like ***svch0st.exe*** or inject malicious DLLs directly into a legitimate ***svchost.exe*** instance to blend in with normal traffic.

#### 2. Standalone / Dedicated Processes

Some complex applications require an entirely isolated execution space.

* **Characteristics:** The service runs inside its own dedicated executable file.
* **Examples:** Third-party endpoint detection agents (like ***Cybereason.exe*** or **CrowdStrike**) and the built-in Windows Defender core engine (***MsMpEng.exe***).

---

### 3.3 Service Startup Types

Every service is assigned a specific startup behavior that controls when it loads into memory:

| Startup Type | Behavior |
| --- | --- |
| **Automatic** | The service starts immediately during system bootup. |
| **Automatic (Delayed Start)** | The service starts shortly after the system boots to minimize initial login lag. |
| **Manual** | The service remains idle until explicitly started by a user, script, or dependent application. |
| **Disabled** | The service cannot be started by the system or user under any circumstances. |

---

### 3.4 Common Cyber Attacks Targeting Windows Services

Because services run with high privileges and survive system reboots, they are heavily targeted by adversaries during operations.

#### 1. Privilege Escalation via Unquoted Service Paths

* **The Vulnerability:** Occurs when a service path contains spaces and is not enclosed in quotation marks (e.g., ***C:\Program Files\Custom Sub\Service.exe***).
* **The Threat:** Windows attempts to resolve the path sequentially. An attacker with low privileges can place a malicious executable named ***Program.exe*** inside *C:*, causing the SCM to execute the malware with **SYSTEM privileges** the next time the service restarts.

#### 2. Insecure Service Permissions

* **The Vulnerability:** Weak Access Control Lists (ACLs) applied directly to a service configuration.
* **The Threat:** If a low-privileged user account is granted ***SERVICE_CHANGE_CONFIG*** permissions, an attacker can modify the service's binary path parameter (***binPath***) using ***sc config***. They can point the service to a malicious payload, restart the service, and gain full system control.

#### 3. Service Persistence

* **The Threat:** Attackers create an entirely new, malicious background service using administrative tools (***sc create MaliciousService binPath= "C:\temp\shell.exe" start= auto***). This ensures their command-and-control connection automatically re-establishes every time the computer powers on.
