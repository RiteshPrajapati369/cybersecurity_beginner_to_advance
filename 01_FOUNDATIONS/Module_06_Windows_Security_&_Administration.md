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

---

Defensive Feature,Operation,Security Benefit
Script Block Logging,Generates Event ID 4104,"Captures the full content of code blocks and scripts as they execute, exposing obfuscated malware."
Constrained Language Mode (CLM),Restricts PowerShell features,"Limits PowerShell functionality to basic commands, blocking advanced APIs and scripts commonly used in exploits."
Execution Policy,Set to RemoteSigned or AllSigned,Acts as a basic safety guard by determining which types of configuration scripts are allowed to run on the system.

---

## 5. Windows Permissions

Windows manages access to its files, folders, and resources using a structured permission framework. This ensures that users and processes can only see or modify what they are explicitly authorized to access.

In simple terms: it acts like a security guard holding a guest list, checking your identity before letting you open a folder or run a file.

```
       [ User Token ] ---> ( Requests access to File.txt )
                                  |
                                  \/
       +-------------------------------------------------------+
       |             Security Descriptor of File.txt           |
       |  - Owner: Administrator                               |
       |  - Discretionary Access Control List (DACL)           |
       +-------------------------------------------------------+
                                  |
            +---------------------+---------------------+
            |                                           |
            \/                                          \/
 [ Access Control Entry 1 ]                  [ Access Control Entry 2 ]
 - User: Alice                               - User: Bob
 - Permissions: Read, Write                  - Permissions: Deny All

```

---

### 5.1 Workgroups vs. Domains

Before looking at individual files, it helps to understand how Windows groups users across a network:

#### Workgroups

* **What it is:** A decentralized, peer-to-peer networking model where each computer maintains its own local user accounts and security database.
* **How it works:** If you want to log into three different computers in a workgroup, you need three separate user accounts created individually on each machine. It is designed for small home or home-office networks.

#### Domains

* **What it is:** A centralized client-server networking model controlled by a central server known as a **Domain Controller**.
* **How it works:** All user accounts, permissions, and computers are managed from a central database (***Active Directory***). A user can log into any computer connected to the domain using a single set of network credentials. This is the standard model for corporate environments.

---

### 5.2 Local Accounts vs. Domain Accounts

The type of account you use determines where your identity is validated:

* **Local Accounts:** These accounts are stored entirely within the local computer's memory database inside the Security Accounts Manager (***SAM***) hive. They only grant access to resources on that specific physical machine.
* **Domain Accounts:** These accounts are created and stored inside the central Active Directory database. They allow users to authenticate across the entire corporate infrastructure.

---

### 5.3 NT File System (NTFS) Permissions

The **NT File System** (***NTFS***) is the standard file system used by modern Windows operating systems, and it includes built-in security permissions.

#### Core NTFS Permissions

* **Read:** Allows users to view or open the file or view the contents of a folder.
* **Write:** Allows users to modify the contents of a file or add files/folders inside a directory.
* **Read & Execute:** Allows users to read files and run executable applications or scripts.
* **Modify:** Combines Read, Write, and Read & Execute permissions, while also allowing users to delete the file or folder.
* **Full Control:** Grants absolute ownership, allowing users to read, write, modify, delete, and change permissions or take ownership of the file.

---

### 5.4 DACL and SACL (The Access Control Lists)

Every file and folder on an NTFS drive contains a hidden security header called a **Security Descriptor**. This descriptor holds two crucial access lists:

#### Discretionary Access Control List (DACL)

* **Purpose:** This list defines **who** has access to a file and **what** they can do with it.
* **How it works:** The DACL is made up of individual entries called **Access Control Entries** (***ACEs***). Each ACE links a specific user or group (identified by their Security Identifier or ***SID***) to an explicit permission (e.g., Alice: Allow Read; Bob: Deny Write).

#### System Access Control List (SACL)

* **Purpose:** This list handles system auditing rather than access control.
* **How it works:** The SACL determines which access attempts should generate a security log entry. For example, it can be configured to say: "If anyone attempts to delete this critical payroll folder, generate a failure audit log entry."

---

### 5.5 Shared Permissions vs. NTFS Permissions

When a folder is made available over a network, two distinct sets of rules apply:

* **Share Permissions:** These only apply when a user accesses the folder **over the network** (using protocols like SMB). They do not apply to someone sitting directly at the physical computer.
* **NTFS Permissions:** These apply to users **both locally and over the network**. They are always active.

#### The Rule of Effective Permissions

When a user accesses a shared folder over the network, Windows evaluates both the Share permissions and the NTFS permissions. The **most restrictive** permission between the two sets always wins.

> **Example:** If a shared folder grants a network user **Full Control** via Share permissions, but the underlying NTFS permissions are set to **Read Only**, the effective permission for that network user is **Read Only**.


---

## 6. Windows Security Features

The Windows operating system includes several built-in security features designed to protect systems from malware, exploits, and unauthorized configuration changes.

In simple terms: these features act as the layers of armor protecting the core OS from external threats.

```
       [ Incoming Threat / Untrusted Code ]
                        |
                        \/
         +------------------------------+
         |      Windows Defender        |  <-- (Antivirus/Anti-Malware)
         +------------------------------+
                        |
                        \/
         +------------------------------+
         |  User Account Control (UAC)  |  <-- (Privilege Escalation Guard)
         +------------------------------+
                        |
                        \/
         +------------------------------+
         |     AppLocker / WDAC         |  <-- (Application Whitelisting)
         +------------------------------+
                        |
                        \/
             [ Protected OS Core ]

```

---

### 6.1 User Account Control (UAC)

**User Account Control** (***UAC***) is a core security feature that enforces the principle of least privilege.

* **Goal:** Prevent unauthorized changes to the operating system by restricting applications from running with administrative privileges unless explicitly approved.
* **How it works:** Even if you log in as an Administrator, Windows runs your everyday applications (like web browsers or media players) in a standard, low-privileged user token space.
* **The Prompt:** When an application attempts to modify system files, install software, or alter registry keys inside ***HKLM***, UAC freezes the screen and displays a secure prompt asking for verification or admin credentials.

---

### 6.2 Windows Defender Antivirus & Exploit Guard

Windows includes native endpoint protection capabilities that run continuously in the background.

#### Windows Defender Antivirus

* **Purpose:** Detects, blocks, and remediates malware, viruses, and spyware.
* **Mechanism:** Uses a mix of signature-based detection (matching files against a database of known threats), heuristic analysis (looking for suspicious behavior patterns), and cloud-powered machine learning to spot new variants.

#### Windows Defender Exploit Guard

A collection of intrusion prevention capabilities designed to lock down applications:

* **Attack Surface Reduction (ASR):** Blocks behaviors often abused by malware, such as preventing Microsoft Office apps from launching child processes or scripts.
* **Controlled Folder Access:** Protects critical directories (like Documents and Desktop) from unauthorized modifications, serving as a primary defense against ransomware encryption attempts.

---

### 6.3 Application Whitelisting: AppLocker vs. WDAC

To prevent untrusted or malicious software from running, Windows provides robust application control mechanisms.

#### AppLocker

* **What it is:** A traditional policy-based application control feature managed via Group Policy.
* **How it works:** Administrators define explicit rules based on file attributes—such as the digital signature publisher, path, or file hash—to determine which files are allowed to execute. It can restrict executables (***.exe***), scripts (***.ps1***, ***.vbs***), and installers.

#### Windows Defender Application Control (WDAC)

* **What it is:** A modern, enterprise-grade application whitelisting feature.
* **How it works:** WDAC operates deeper in the OS structure than AppLocker, enforcing restrictions directly at the Windows kernel level. It is designed to withstand tampering from compromised administrative accounts, making it highly effective against advanced persistent threats (***APTs***).

---

### 6.4 Exploit Mitigations: DEP and ASLR

Windows implements low-level memory protections to make it significantly harder for attackers to engineer working exploits for software vulnerabilities.

#### Data Execution Prevention (DEP)

* **The Defense:** Marks specific memory regions (such as the stack and heap) as non-executable.
* **Security Benefit:** If an attacker uses a vulnerability like a buffer overflow to inject malicious shellcode into a program's data memory area, DEP prevents the CPU from executing that code, forcing the application to safely crash instead.

#### Address Space Layout Randomization (ASLR)

* **The Defense:** Randomizes the memory locations where system components, executable code, and DLLs are loaded every time the computer boots.
* **Security Benefit:** Traditional exploits rely on static memory addresses to jump to specific functions. By shuffling the memory map, ASLR prevents attackers from guessing where target code resides, breaking the predictability required for reliable execution.

---

## 7. Windows Logging

Windows Logging is the system's built-in record-keeping mechanism. Every major activity—such as a user logging in, a file being modified, or a service crashing—is captured as a time-stamped entry called an **Event Log**.

In simple terms: it acts like a continuous flight data recorder for the operating system, allowing defenders to reconstruct exactly what happened during a cyber security incident.

```
+------------------------------------------------------------+
|                       Event Sources                        |
|   [ Application Errors ]  [ Security Audits ]  [ System ]  |
+------------------------------------------------------------+
                              ||
                              \/
+------------------------------------------------------------+
|                     Windows Event Log                      |
|                        (winevt)                            |
+------------------------------------------------------------+
                              ||
                              \/
+------------------------------------------------------------+
|                     Event Viewer Tool                      |
|                      (eventvwr.msc)                        |
+------------------------------------------------------------+

```

---

### 7.1 The Windows Event Viewer

The standard administrative graphical tool used to view and navigate system logs is the **Event Viewer** (***eventvwr.msc***). Within this interface, event records are categorized into distinct channels based on their source and purpose.

#### The 4 Core Windows Logs

* **Application Logs:** Records events logged by third-party applications or programs running on the system (e.g., a database tool failing to connect or an application crashing).
* **Security Logs:** Records authentication attempts, privilege utilization, and resource access events. This is the **most critical log channel** for security monitoring and threat detection.
* **System Logs:** Records events generated by the Windows operating system itself, such as driver boot failures, hardware issues, or system restarts.
* **Forwarded Events Logs:** Stores logs collected from remote computers across the network using **Windows Event Forwarding** (***WEF***). This allows defenders to aggregate logs from multiple machines into one central analysis server.

---

### 7.2 Anatomy of an Event Log

Every logged event contains structured properties that allow security teams to filter and parse data efficiently during an investigation:

* **Log Name:** The specific channel where the log is stored (e.g., ***Security***).
* **Source:** The software component or driver that generated the log event.
* **Date and Time:** The exact timestamp indicating when the action occurred.
* **Event ID:** A unique numerical value assigned by Windows that identifies the specific type of action or occurrence (e.g., Event ID ***4624*** translates to a successful login).
* **Task Category:** Further clarifies the type of sub-operation performed by the source component.
* **Level:** Identifies the severity of the event (***Information***, ***Warning***, ***Error***, or ***Critical***).

---

### 7.3 Critical Event IDs Every Defender Must Know

When analyzing a compromised system, security analysts use explicit Event IDs to trace an attacker's footprints. Memorize these fundamental Windows Security Event IDs:

| Event ID | Log Channel | Meaning | Security Significance |
| --- | --- | --- | --- |
| **4624** | Security | Successful Account Logon | Verifies when and how an account logged into the system (e.g., local vs. network logon types). |
| **4625** | Security | Failed Account Logon | Helps detect **Brute-Force** or password-spraying attacks when multiple failures occur in a short time. |
| **4688** | Security | A New Process Has Been Created | Tracks command execution. If process auditing is turned on, it reveals the exact command line arguments used by an attacker. |
| **4720** | Security | A User Account Was Created | Alerts defenders to potential unauthorized persistence if an attacker creates a new backdoor user account. |
| **1102** | Security | The Audit Log Was Cleared | Highly suspicious event. Indicates an attacker is attempting to cover their tracks by wiping the log history. |

---

### 7.4 Advanced Logging: Sysmon (System Monitor)

While standard Windows Event Logs provide excellent basic telemetry, advanced detection teams supplement them by installing **Sysmon** (***System Monitor***), a free tool from the Microsoft Sysinternals suite.

* **What it does:** Sysmon installs a system driver that monitors and records deep host-level activity directly into a dedicated event log channel: ***Microsoft-Windows-Sysmon/Operational***.
* **Why it is superior:** It provides granular visibility into advanced attack techniques that standard logs might miss.

#### Crucial Sysmon Event IDs

* **Event ID 1 (Process Creation):** Records the creation of every process, including its full command-line path, file hashes (MD5/SHA256), and parent process tracking. This makes it easy to spot suspicious execution chains, like ***cmd.exe*** being launched by Microsoft Word (***winword.exe***).
* **Event ID 3 (Network Connection):** Maps running processes directly to outbound network traffic, exposing exactly which application is establishing connections to unknown external IP addresses.
* **Event ID 11 (File Create):** Tracks when files are created or dropped onto the system, which is vital for monitoring downloaded malware or staged payload tools.

---

## 8. Active Directory (AD) Foundations

Active Directory (***AD***) is a directory service developed by Microsoft for Windows domain networks. It provides central authentication and authorization for computers, users, servers, and other resources within an enterprise network.

In simple terms: it acts as the centralized identity management hub for an organization, working like a corporate digital phonebook that also controls who can access which rooms and files.

```
                  [ Central Domain Controller ]
                  (Running Active Directory)
                              |
       +----------------------+----------------------+
       |                                             |
  [ Identity & Auth ]                       [ Resource Management ]
       |                                             |
  +----+----+                                   +----+----+
  |         |                                   |         |
[Users] [Groups]                             [Computers] [Shares]

```

---

### 8.1 Core AD Structural Components

Active Directory organizes network infrastructure using explicit logical and physical boundaries:

* **Domain Controller (DC):** The heart of an Active Directory network. It is the server that runs the AD Domain Services role, hosts the directory database, authenticates user identities, and enforces security policies.
* **Objects:** The basic building blocks of AD. Anything inside the directory is an object, such as a **User**, a **Group**, a **Computer**, or a **Printer**.
* **Organizational Units (OUs):** Containers used to group objects within a domain. OUs provide a structural hierarchy, making it easier to delegate administrative privileges and apply specific security configurations to subsets of users or devices.
* **Trees and Forests:** A **Tree** is a collection of one or more Windows domains that share a continuous namespace (e.g., ***https://www.google.com/search?q=corp.company.com*** and ***https://www.google.com/search?q=dev.company.com***). A **Forest** is the highest organizational boundary in AD, grouping multiple trees together under a single global configuration, schema, and security trust.

---

### 8.2 Authentication Protocols: NTLM vs. Kerberos

Active Directory relies on two primary protocols to verify user identities across the network ecosystem:

#### NT LAN Manager (NTLM)

* **What it is:** A legacy challenge-response authentication protocol.
* **How it works:** The client sends an authentication request, the server issues a random challenge, and the client encrypts that challenge using a hash of the user's password.
* **Security Flaw:** NTLM does not support mutual authentication (the client cannot verify if the server is legitimate). It is highly vulnerable to **Pass-the-Hash (PtH)** and **NTLM Relay** attacks, where adversaries intercept and reuse authentication tokens.

#### Kerberos

* **What it is:** The default, highly secure ticketing protocol used by modern Active Directory environments.
* **How it works:** It relies on a trusted third party called the **Key Distribution Center (KDC)**, which runs inside the Domain Controller. Instead of constantly verifying passwords, the KDC issues cryptographic tokens called **Tickets** (such as a Ticket Granting Ticket or ***TGT***).
* **Security Benefit:** Kerberos supports mutual authentication and prevents passwords or password hashes from being passed directly over the network wire.

---

### 8.3 Group Policy Objects (GPOs)

**Group Policy Objects** (***GPOs***) are collections of configuration rules that administrators apply to users and computers within Active Directory.

* **Purpose:** They allow centralized management of security configurations across thousands of enterprise endpoints simultaneously from a single console.
* **Defensive Controls:** Administrators use GPOs to enforce strong password complexity requirements, disable dangerous protocols (like SMBv1), restrict local administrator access, or push out firewall rule updates.
* **Inheritance:** GPOs follow a specific processing order: **Local**, **Site**, **Domain**, and **Organizational Unit** (***LSDO***). The policy applied closest to the object inside the OU hierarchy wins if there are conflicting rules.

---

### 8.4 Common Cyber Attacks Targeting Active Directory

Because Active Directory holds the keys to an entire enterprise network, it is the primary target for attackers seeking complete network control.

#### 1. Kerberoasting

* **The Threat:** Attackers request a Kerberos service ticket for a specific service account registered in AD. Because part of the ticket is encrypted with the service account's password hash, the attacker can take the ticket offline and run brute-force tools (like ***Hashcat***) to crack the plain-text password.

#### 2. AS-REP Roasting

* **The Threat:** Targets user accounts that explicitly have "Do not require Kerberos preauthentication" enabled. Attackers can request authentication data from the DC for these specific accounts without knowing the password, receiving an encrypted packet that can be cracked offline to reveal the credentials.

#### 3. Golden Ticket Attacks

* **The Threat:** If an attacker compromises the password hash of the special Active Directory account named ***krbtgt***, they can forge their own Ticket Granting Tickets (***TGTs***). This allows them to grant themselves unlimited administrative privileges to any resource in the entire forest, achieving complete, long-term persistence that bypasses password resets.

---

## 9. Hands-On Lab: Audit Windows Security & Extract Event Logs

This hands-on lab provides a step-by-step technical walk-through to configure advanced logging, simulate real adversary discovery techniques, and programmatically extract security telemetry.

```
+-------------------------------------------------------------+
|                     STEP 1: Policy Setup                    |
|  Configure Advanced Audit Policies via secpol.msc & GPO     |
+-------------------------------------------------------------+
                              ||
                              \/
+-------------------------------------------------------------+
|                  STEP 2: Telemetry Injection                |
|       Force Windows to Capture Full Command-Line Strings     |
+-------------------------------------------------------------+
                              ||
                              \/
+-------------------------------------------------------------+
|                   STEP 3: Attack Simulation                 |
|      Execute Discovery Binaries (whoami, net, netstat)       |
+-------------------------------------------------------------+
                              ||
                              \/
+-------------------------------------------------------------+
|                   STEP 4: Automated Parsing                 |
|    Query, Filter, and Extract Metadata via PowerShell Pipeline|
+-------------------------------------------------------------+

```

---

### 9.1 Lab Objectives

By completing this technical lab, you will acquire the following production-level skills:

1. **Manipulate Local Security Policies:** Establish baseline auditing for process lifecycles.
2. **De-obfuscate Execution Strings:** Force the operating system to log transparent string arguments rather than just binary execution flags.
3. **Programmatic Log Parsing:** Use object-oriented pipeline queries to pull actionable security artifacts without loading heavy GUI sub-systems.

---

### 9.2 Step 1: Enable Advanced Process Creation Auditing

Standard Windows configurations do not log individual process lifecycles due to database sizing constraints. To build a solid detection perimeter, we must explicitly enable detailed event tracking.

1. Launch the execution dialog by pressing **Win + R**, input ***secpol.msc***, and click **OK** to open the **Local Security Policy** console.
2. Inside the logical directory tree, navigate to:
***Advanced Audit Policy Configuration*** $\rightarrow$ ***System Audit Policies - Local Group Policy Object*** $\rightarrow$ ***Detailed Tracking***.
3. In the detail panel on the right, locate and double-click the ***Audit Process Creation*** policy attribute.
4. Access the properties tab, check the tracking flags for both **Success** and **Failure** execution paths, then click **Apply** and **OK**.

```
[ Security Management Console ]
└── Advanced Audit Policy Configuration
    └── System Audit Policies
        └── Detailed Tracking
            └── Audit Process Creation ──> Enable: [X] Success | [X] Failure

```

---

### 9.3 Step 2: Include Command-Line Arguments in Process Logs

Enabling basic process creation logs tells you *that* a program executed (e.g., ***powershell.exe*** opened), but it fails to log *what* parameters were called. To catch obfuscated scripts, malicious downloads, or credential arguments, you must turn on command-line string recording.

1. Launch the execution dialog by pressing **Win + R**, input ***gpedit.msc***, and press **Enter** to open the **Local Group Policy Editor**.
2. Drill down through the following schema path:
***Computer Configuration*** $\rightarrow$ ***Administrative Templates*** $\rightarrow$ ***System*** $\rightarrow$ ***Audit Process Creation***.
3. Double-click the policy entry labeled ***Include command line in process creation events***.
4. Shift the toggle selection from **Not Configured** to **Enabled**. This action forces the operating system to pass execution arguments straight into the security event log payload. Click **Apply** and **OK**.
5. To update the active runtime kernel parameters immediately without a system reboot, open an administrative terminal and run the policy update engine:
```cmd
gpupdate /force

```



---

### 9.4 Step 3: Simulate a Security Event (Adversary Emulation)

With your security monitoring system fully active, execute a sequence of typical environment discovery commands. Attackers run these exact commands right after compromising an endpoint to determine their privilege levels and map local network infrastructure.

Open a standard command prompt terminal (***cmd.exe***) and execute these three commands sequentially:

```cmd
whoami
net user
netstat -ano

```

> **Under the Hood:** Every time you press enter on these commands, the command interpreter calls the underlying binary files inside the system directory. The operating system kernel intercepts these executions and writes a detailed audit entry inside the volatile security log database under Event ID ***4688***.

---

### 9.5 Step 4: Query and Extract the Security Logs Using PowerShell

Manually hunting for specific events inside the native **Event Viewer** GUI (***eventvwr.msc***) is highly inefficient during active incident response operations. Instead, we use PowerShell to rapidly query, isolate, and format the telemetry.

1. Right-click the Windows Start menu button and select **Windows PowerShell (Admin)** to open an elevated shell environment.
2. Execute the following specialized pipeline script to isolate the exact process logs generated during your simulation:

```powershell
Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4688} | Select-Object -First 5 | Format-List TimeCreated, Id, Message

```

#### Detailed Metadata Analysis

Examine the plain-text configuration payload returned inside the **Message** attribute block of your PowerShell window. You will see several critical metadata points:

* **Subject $\rightarrow$ Security ID:** Identifies the explicit user account context that initiated the process (e.g., `SYSTEM` or your local username).
* **New Process Name:** The absolute path on the storage disk of the executed binary (e.g., ***C:\Windows\System32\whoami.exe***).
* **Process Command Line:** The complete string argument passed during execution (e.g., ***netstat -ano***). This is your primary artifact for checking for indicators of compromise (***IoCs***).
* **Creator Process Name:** The parent application responsible for spawning the child process (e.g., ***C:\Windows\System32\cmd.exe***). Checking this execution chain helps you spot anomalies, such as an internet browser or a mail client launching a command shell.

---

## 10. Summary & Key Takeaways

This module covered the core components of the Windows operating system from an administrative and security perspective. Understanding these foundations allows defenders to secure environments effectively and detect malicious activity.

---

### Key Takeaways

* **Security Boundaries:** The separation between **User Mode (Ring 3)** and **Kernel Mode (Ring 0)** prevents user-space applications from crashing the entire operating system or directly accessing physical hardware.
* **The System Hive:** The **Windows Registry** is a centralized database containing critical configuration data. Attackers frequently abuse registry keys like ***Run Keys*** for persistence and ***ms-settings*** for UAC bypasses.
* **Background Workers:** **Windows Services** run continuously in the background managed by the **Service Control Manager** (***services.exe***). Insecure configurations like ***Unquoted Service Paths*** or weak service permissions allow for easy privilege escalation.
* **Administrative Frameworks:** **PowerShell** processes object-oriented data, making it far superior to **CMD** for automation. However, its deep integration with the OS makes it a prime tool for **Living off the Land (LotL)** attacks.
* **Access Control:** File system security relies heavily on **NTFS Permissions** mapped through **Discretionary Access Control Lists (DACLs)**. When accessing files over a network, the most restrictive choice between Share and NTFS permissions becomes the effective permission.
* **Enterprise Identity:** **Active Directory (AD)** centralizes corporate identity management. Legacy environments relying on **NTLM** are heavily exposed to credential theft, whereas **Kerberos** provides secure, ticket-based authentication.
* **Security Telemetry:** Standard Windows event logging tracks critical actions via specific IDs (like Event ID ***4624*** for logons or ***4688*** for process creation). Supplementing these logs with **Sysmon** provides advanced host visibility into network sockets and file creation.

---

## 11. Quiz: Test Your Knowledge

Test your understanding of Windows security and administration concepts with these review questions.

#### 1. A security analyst spots an unhandled error inside a third-party kernel driver, causing a complete system failure. What is this state commonly called?

* A) User Mode Exception
* B) Blue Screen of Death (BSOD)
* C) Access Control Entry Failure
* D) Registry Corruption Event

#### 2. Which root registry hive contains configuration data specific to the user who is currently logged into the system session?

* A) ***HKEY_LOCAL_MACHINE***
* B) ***HKEY_CLASSES_ROOT***
* C) ***HKEY_CURRENT_USER***
* D) ***HKEY_USERS***

#### 3. An attacker abuses a service configuration path containing spaces that is not wrapped in quotation marks. What type of vulnerability is being exploited?

* A) Insecure Service Permissions
* B) Unquoted Service Path
* C) Service Hijacking
* D) DLL Search Order Hijacking

#### 4. Why do modern threat actors prefer "Living off the Land" techniques using tools like PowerShell rather than dropping compiled custom malware binaries onto a disk?

* A) PowerShell executes scripts directly in volatile memory, bypassing file-based antivirus scanners.
* B) PowerShell scripts automatically run with SYSTEM privileges without requiring UAC bypasses.
* C) PowerShell completely disables the Security Reference Monitor (SRM) upon launch.
* D) CMD commands cannot establish outbound network connections.

#### 5. A shared network folder grants a domain user "Full Control" via Share permissions, but the underlying NTFS file system permission is configured as "Read Only". What is the effective permission when the user connects over the network?

* A) Full Control
* B) Write Only
* C) Read Only
* D) Access Denied

#### 6. Which Windows Security Event ID explicitly indicates that a user account was successfully authenticated to a local or remote session?

* A) Event ID ***4688***
* B) Event ID ***4624***
* C) Event ID ***4625***
* D) Event ID ***1102***

#### 7. What specific token structure does the Kerberos authentication protocol utilize to manage access verification instead of passing password hashes over the wire?

* A) Challenge-Response Strings
* B) Access Control Entries
* C) Security Identifiers (SIDs)
* D) Cryptographic Tickets (e.g., TGT)

---

### Answer Key & Explanations

1. **B** — Errors or crashes inside Kernel Mode (Ring 0) affect shared memory space and stop the CPU execution loop, causing a system-wide crash (**BSOD**).
2. **C** — ***HKEY_CURRENT_USER*** (`HKCU`) maps configuration settings dynamically for the active user session.
3. **B** — An **Unquoted Service Path** allows an attacker to place a malicious executable along an unquoted file path containing spaces to intercept execution.
4. **A** — Fileless attacks utilize native administrative tools like PowerShell to execute code entirely in memory, leaving no artifacts on disk for traditional signature-based antivirus tools.
5. **C** — When combining Share and NTFS permissions, Windows always evaluates both lists and applies the **most restrictive** rule.
6. **B** — Event ID ***4624*** logs successful account logons within the Windows Security log channel.
7. **D** — Kerberos uses a Key Distribution Center (KDC) to issue centralized **Tickets** (like Ticket Granting Tickets) to verify identity safely.

---

## 12. Resources

To deepen your understanding of Windows internals, security features, and log analysis, explore these authoritative technical guides:

* **Windows Internals:** Review the [Microsoft Learn Documentation on Windows Internals](https://learn.microsoft.com/en-us/sysinternals/resources/windows-internals) to study deep kernel structures, memory management models, and process architectures.
* **Registry Management:** Explore the [Microsoft Guide on the Windows Registry](https://learn.microsoft.com/en-us/windows/win32/sysinfo/about-the-registry) for detailed structural references on hives, key components, and storage layouts.
* **Advanced Logging:** Download and configure [Microsoft Sysinternals System Monitor (Sysmon)](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) to enhance your endpoint event telemetry collection.
* **Active Directory Security:** Read the [Microsoft Active Directory Domain Services Security Documentation](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory) to learn enterprise design patterns and credentials protection strategies.
* **Threat Hunter Guides:** Bookmark the [MITRE ATT&CK Framework](https://attack.mitre.org/matrices/enterprise/windows/) to audit real-world techniques used by adversaries targeting Windows enterprise networks.

