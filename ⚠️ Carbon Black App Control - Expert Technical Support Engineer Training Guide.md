# Carbon Black App Control - Expert Technical Support Engineer Training Guide

> **Mission**: Transform from beginner to expert-level Technical Support Engineer with deep understanding of Carbon Black App Control architecture, troubleshooting, and real-world problem-solving.

---

## Table of Contents

1. [Training Philosophy & Approach](#training-philosophy--approach)
2. [Module 0: Prerequisites & Lab Environment Setup](#module-0-prerequisites--lab-environment-setup)
3. [Module 1: Core Foundations](#module-1-core-foundations)
4. [Module 2: Carbon Black App Control Architecture Deep Dive](#module-2-carbon-black-app-control-architecture-deep-dive)
5. [Module 3: Server Components & Installation](#module-3-server-components--installation)
6. [Module 4: Agent Components & Management](#module-4-agent-components--management)
7. [Module 5: Policies & Enforcement Levels](#module-5-policies--enforcement-levels)
8. [Module 6: Rules Engine Mastery](#module-6-rules-engine-mastery)
9. [Module 7: Event Rules & Automation](#module-7-event-rules--automation)
10. [Module 8: Troubleshooting Tools & Techniques](#module-8-troubleshooting-tools--techniques)
11. [Module 9: Command Line Interface (DASCLI/B9CLI)](#module-9-command-line-interface-dasclib9cli)
12. [Module 10: Real-World Troubleshooting Scenarios](#module-10-real-world-troubleshooting-scenarios)
13. [Module 11: Automation & Scripting](#module-11-automation--scripting)
14. [Module 12: Advanced Topics & Integration](#module-12-advanced-topics--integration)
15. [Appendix: Quick Reference Guides](#appendix-quick-reference-guides)

---

## Training Philosophy & Approach

### How This Training Works

**This is NOT a memorization course.** You will:
- ✅ Understand the **WHY** behind every concept
- ✅ Learn through **hands-on experimentation** with break/fix scenarios
- ✅ Build **mental models** that let you troubleshoot without documentation
- ✅ Develop **automation scripts** to save time
- ✅ Think like a **support engineer** - asking the right questions

### Learning Principles

1. **Understand Before Doing**: Every command, every setting has a purpose
2. **Break Things Intentionally**: The best learning comes from fixing what you broke
3. **Build Mental Maps**: Visualize how components interact
4. **Automate Repetitive Tasks**: Use scripting to become more efficient
5. **Document Your Learning**: Keep a troubleshooting journal

### How to Use This Guide

- 🔹 **Concept Blocks**: Understand theory
- 🛠️ **Hands-On Labs**: Practical exercises
- 💡 **Support Engineer Tips**: Real-world insights
- ⚠️ **Common Pitfalls**: Mistakes to avoid
- 🎯 **Challenge Exercises**: Test your understanding
- 🐛 **Break/Fix Scenarios**: Troubleshooting practice

---

## Module 0: Prerequisites & Lab Environment Setup

### 0.1 Skills Assessment

Before diving into Carbon Black App Control, assess your current skill level:

#### **Windows OS Knowledge** (Required)
- [ ] Can navigate Windows Event Viewer and interpret logs
- [ ] Understand Windows services (start, stop, dependencies)
- [ ] Familiar with registry structure and editing
- [ ] Know how to use PowerShell for basic tasks
- [ ] Understand file system permissions and ACLs
- [ ] Can use Task Manager and Resource Monitor
- [ ] Familiar with Windows kernel vs user mode processes

**If you need strengthening**: Complete Windows Server Administration basics

#### **Linux OS Knowledge** (Required)
- [ ] Comfortable with command-line navigation
- [ ] Understand file permissions (chmod, chown)
- [ ] Know systemd/systemctl service management
- [ ] Can read and parse log files (/var/log)
- [ ] Basic bash scripting ability
- [ ] Understand processes and PIDs
- [ ] Familiar with package managers (yum/dnf for RHEL/CentOS)

**If you need strengthening**: Complete Linux fundamentals course

#### **Networking Knowledge** (Required)
- [ ] Understand TCP/IP basics
- [ ] Know common ports (443, 41002 for CB App Control)
- [ ] Can use ping, netstat, telnet for connectivity testing
- [ ] Understand DNS resolution
- [ ] Familiar with firewall concepts
- [ ] Know how to read packet captures (basic Wireshark)

**If you need strengthening**: Study networking fundamentals

#### **Database Knowledge** (Required)
- [ ] Basic SQL querying (SELECT, WHERE, JOIN)
- [ ] Can navigate SQL Server Management Studio (SSMS)
- [ ] Understand indexes and query performance
- [ ] Know how to read SQL Server logs
- [ ] Familiar with backup/restore concepts

**If you need strengthening**: SQL Server basics for administrators

#### **Security Concepts** (Important)
- [ ] Understand file hashing (MD5, SHA-1, SHA-256)
- [ ] Know what whitelisting/blacklisting means
- [ ] Familiar with digital certificates and signing
- [ ] Understand least privilege principle
- [ ] Know basic malware/threat concepts

### 0.2 Lab Environment Setup

#### **Recommended Lab Architecture**

```
┌─────────────────────────────────────────────────────────┐
│                   Your Lab Network                       │
│                   (10.100.10.0/24)                      │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌────────────────────┐      ┌─────────────────────┐  │
│  │  CB App Control    │      │   Windows 10/11     │  │
│  │  Server            │◄────►│   Client            │  │
│  │  10.100.10.100     │      │   10.100.10.101     │  │
│  │                    │      │                     │  │
│  │  - Windows Server  │      │   - Agent Deployed  │  │
│  │  - SQL Server      │      │   - Testing Target  │  │
│  └────────────────────┘      └─────────────────────┘  │
│           ▲                                             │
│           │                                             │
│           ▼                                             │
│  ┌─────────────────────┐                               │
│  │   Linux Endpoint    │                               │
│  │   (RHEL/CentOS)     │                               │
│  │   10.100.10.102     │                               │
│  │                     │                               │
│  │   - Agent Deployed  │                               │
│  └─────────────────────┘                               │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### **Hardware/Software Requirements**

**Host Machine Minimum Specs:**
- CPU: 4 cores (8 recommended)
- RAM: 16GB (32GB recommended)
- Disk: 200GB free space
- Hypervisor: VMware Workstation, VirtualBox, or Hyper-V

**Server VM Specs:**
- OS: Windows Server 2019/2022
- CPU: 2 cores
- RAM: 8GB
- Disk: 100GB
- Software: SQL Server 2016+ (Express is fine for lab)

**Windows Client VM Specs:**
- OS: Windows 10/11
- CPU: 2 cores
- RAM: 4GB
- Disk: 60GB

**Linux Client VM Specs:**
- OS: RHEL 8/9 or CentOS 8 Stream
- CPU: 1 core
- RAM: 2GB
- Disk: 40GB

#### **Step-by-Step Lab Setup**

##### **Step 1: Create Virtual Network**

1. In your hypervisor, create a **NAT network** or **Host-Only network**
2. Assign subnet: `10.100.10.0/24`
3. Enable DHCP or use static IPs

💡 **Support Engineer Tip**: Use static IPs for lab - makes troubleshooting easier and mimics production environments

##### **Step 2: Deploy Windows Server VM**

1. Install Windows Server 2019/2022
2. Set hostname: `APPCONTROL`
3. Set static IP: `10.100.10.100`
4. Install Windows Updates
5. Disable Windows Firewall (for lab only!)
6. Install SQL Server 2019 Express:
   ```powershell
   # Download SQL Server Express
   # During installation:
   # - Choose "Basic" installation
   # - Enable Mixed Mode authentication
   # - Set SA password: Gr95M23rE9
   # - Add your admin user as SQL admin
   ```

**Why we do this**: CB App Control server requires SQL Server as its backend database. Understanding SQL basics is critical for troubleshooting server issues.

##### **Step 3: Install Carbon Black App Control Server**

**Prerequisites Check:**
```powershell
# Verify .NET Framework 4.8 or higher
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" | Select-Object Version

# Verify SQL Server is running
Get-Service MSSQLSERVER

# Verify IIS is NOT installed (CB uses its own web server)
Get-WindowsFeature Web-Server
```

**Installation Steps:**

1. Download CB App Control Server installer from Broadcom portal
2. Run installer as Administrator
3. Installation wizard prompts:
   - **Database Server**: `localhost` or `127.0.0.1`
   - **Database Instance**: Leave blank (default instance)
   - **Database Authentication**: SQL Server (use SA account)
   - **Database Name**: `Bit9Server` (default)
   - **Service Account**: Use Local System (for lab)
   - **Console Admin User**: `admin`
   - **Console Admin Password**: `admin123`
   - **Server Address**: `https://appcontrol` (or IP `10.100.10.100`)
   - **Web Server Port**: `443`

4. Wait for installation to complete (10-15 minutes)

**Post-Installation Verification:**
```powershell
# Check if CB App Control services are running
Get-Service | Where-Object {$_.Name -like "*Parity*" -or $_.Name -like "*Bit9*"}

# You should see:
# - Bit9 Parity Server (ParityServer)
# - Bit9 Parity Service (Parity)
# - Bit9 Parity Reporter (ParityReporter)

# Check if web console is accessible
Test-NetConnection -ComputerName localhost -Port 443

# Test database connectivity
sqlcmd -S localhost -U sa -P Gr95M23rE9 -Q "SELECT @@VERSION"
```

**Understanding What Just Happened:**

The installer:
1. Created a SQL database called `Bit9Server`
2. Installed three Windows services (we'll detail these in Module 3)
3. Configured a web server on port 443
4. Created initial policies and configuration

💡 **Support Engineer Tip**: Always verify services after installation. In the field, 80% of "server not working" issues are service-related.

##### **Step 4: Deploy Windows Client VM**

1. Install Windows 10/11
2. Set hostname: `WINDOWS-CLIENT`
3. Set static IP: `10.100.10.101`
4. Add DNS entry for `appcontrol` → `10.100.10.100` in `C:\Windows\System32\drivers\etc\hosts`:
   ```
   10.100.10.100    appcontrol
   ```
5. Install Chrome or Edge browser
6. Test connectivity to server:
   ```cmd
   ping 10.100.10.100
   curl https://appcontrol
   ```

##### **Step 5: Deploy Linux Client VM** (Optional but recommended)

1. Install RHEL 8/CentOS 8 Stream
2. Set hostname: `linux-client`
3. Set static IP: `10.100.10.102`
4. Add server to `/etc/hosts`:
   ```bash
   echo "10.100.10.100    appcontrol" >> /etc/hosts
   ```
5. Test connectivity:
   ```bash
   ping 10.100.10.100
   curl -k https://appcontrol
   ```

##### **Step 6: Access CB App Control Console**

1. From Windows Client, open browser
2. Navigate to `https://appcontrol`
3. Accept self-signed certificate warning
4. Login:
   - Username: `admin`
   - Password: `admin123`

5. You should see the main dashboard!

**First Console Tour:**
- **Menu Bar**: Main navigation (Gear, Rules, Assets, Tools, Reports, Admin)
- **Dashboard**: System health overview
- **Computer List**: View managed endpoints (currently empty)

### 0.3 Lab Snapshot Strategy

Before proceeding, create VM snapshots:

1. **Server-Clean-Install**: After CB App Control server installation
2. **Client-No-Agent**: Windows client before agent deployment
3. **Linux-No-Agent**: Linux client before agent deployment

💡 **Support Engineer Tip**: Snapshots are your time machine. In real support, you won't have this luxury, but in labs, use it to experiment fearlessly.

---

## Module 1: Core Foundations

### 1.1 Windows OS Deep Dive (CB App Control Context)

#### **Windows Services Architecture**

Carbon Black App Control relies heavily on Windows services. Let's understand them deeply.

**Key Concepts:**

```
User Space (Ring 3)
├── User Applications (notepad.exe, chrome.exe)
├── CB Agent User Components (Parity.exe, Notifier.exe)
└── Windows Services (managed by Service Control Manager)

────────────────────────────────────────────

Kernel Space (Ring 0)
├── Windows Kernel (ntoskrnl.exe)
├── Device Drivers
└── CB Agent Kernel Driver (Parity.sys)
```

**Why This Matters**: CB App Control operates at BOTH user and kernel levels. The kernel driver (Parity.sys) intercepts file operations before they reach the file system.

#### **Hands-On: Understanding Windows Services**

🛠️ **Exercise 1.1: Service Investigation**

1. Open Services console:
   ```powershell
   services.msc
   ```

2. Find a service (e.g., Windows Update)
3. Right-click → Properties
4. Observe:
   - **Startup Type**: Automatic, Manual, Disabled
   - **Log On As**: System, Network Service, or specific user
   - **Dependencies**: What services does it depend on?

5. Use command line to query service:
   ```powershell
   sc query wuauserv
   sc qc wuauserv  # Query configuration
   ```

**Understanding Service Dependencies:**

```powershell
# Get service dependencies
$service = Get-Service -Name wuauserv
$service.ServicesDependedOn  # Services this depends on
$service.DependentServices   # Services that depend on this
```

**Why This Matters for CB App Control**: The CB Agent services have dependencies. If SQL Server or networking services fail, CB services may fail to start.

🎯 **Challenge 1.1**: What happens if you stop a service that other services depend on? Try it with a non-critical service.

#### **Windows Registry for CB App Control**

The Registry stores CB App Control configuration.

**Key Registry Locations:**

```
HKEY_LOCAL_MACHINE\SOFTWARE\Bit9
├── Parity Agent
│   ├── ServerAddress (where agent connects)
│   ├── ServerPort (443 or 41002)
│   └── CacheFolder (where Cache.db is stored)
└── Parity Server
    └── Installation settings
```

🛠️ **Exercise 1.2: Registry Navigation**

1. Open Registry Editor:
   ```cmd
   regedit
   ```

2. Navigate to `HKEY_LOCAL_MACHINE\SOFTWARE`
3. Practice searching (Ctrl+F)

**PowerShell Registry Access:**
```powershell
# Read registry value
Get-ItemProperty -Path "HKLM:\SOFTWARE\Bit9\Parity Agent" -Name ServerAddress

# Set registry value (DON'T do this yet!)
# Set-ItemProperty -Path "HKLM:\SOFTWARE\Bit9\Parity Agent" -Name ServerAddress -Value "https://newserver"
```

⚠️ **Common Pitfall**: Never manually edit CB App Control registry without guidance from support. Use DASCLI commands instead.

#### **Windows Event Logs**

Event logs are your first troubleshooting destination.

**Key Logs for CB App Control:**
- **Application Log**: CB App Control server events
- **System Log**: Service start/stop, driver load failures
- **Security Log**: Authentication issues (if using Windows Auth for console)

🛠️ **Exercise 1.3: Event Log Mastery**

```powershell
# Get recent Application log errors
Get-EventLog -LogName Application -EntryType Error -Newest 50

# Filter for Bit9/Parity events
Get-EventLog -LogName Application -Source *Bit9* -Newest 100

# Export to file for analysis
Get-EventLog -LogName Application -After (Get-Date).AddHours(-24) | 
    Export-Csv -Path C:\temp\app-events.csv
```

**Reading Event Log Entries:**
- **Event ID**: Unique identifier (Google "Event ID XXXX" for details)
- **Source**: Application that logged the event
- **Level**: Information, Warning, Error, Critical
- **Message**: Descriptive text

💡 **Support Engineer Tip**: When troubleshooting, always collect logs from the time window when the issue occurred. Use `-After` and `-Before` parameters.

#### **File System & Permissions**

CB App Control needs proper permissions to access files.

**NTFS Permissions Review:**
- **Read (R)**: View file contents
- **Write (W)**: Modify file
- **Execute (X)**: Run file
- **Full Control**: All permissions + change permissions

🛠️ **Exercise 1.4: Permission Investigation**

```powershell
# Get file ACL (Access Control List)
Get-Acl "C:\Program Files (x86)\Bit9\Parity Agent\Cache.db" | Format-List

# Check if a user has access
$acl = Get-Acl "C:\Windows\System32\notepad.exe"
$acl.Access | Where-Object {$_.IdentityReference -like "*Users*"}
```

**Why This Matters**: CB App Control kernel driver intercepts file access. Understanding permissions helps you troubleshoot blocked files.

### 1.2 Linux OS Deep Dive (CB App Control Context)

#### **Linux File System Hierarchy**

```
/
├── /bin          # Essential command binaries
├── /boot         # Boot loader files
├── /dev          # Device files
├── /etc          # System configuration
├── /home         # User home directories
├── /lib          # System libraries
├── /opt          # Third-party software (CB Agent installed here!)
│   └── /Bit9
│       └── /bin
│           └── b9cli  # CB App Control CLI tool
├── /proc         # Process and kernel info (virtual filesystem)
├── /sys          # System device info (virtual filesystem)
├── /tmp          # Temporary files
├── /usr          # User programs and data
├── /var          # Variable data (logs!)
│   └── /log      # Log files
│       └── /bit9  # CB App Control logs
└── /root         # Root user home directory
```

💡 **Support Engineer Tip**: On Linux, CB Agent installs to `/opt/Bit9/`. All agent files, logs, and CLI tools are there.

#### **Linux Permissions**

```bash
ls -l /opt/Bit9/bin/b9cli
# -rwxr-xr-x 1 root root 1234567 Nov 27 10:00 b9cli
# │││││││││
# │││││││││
# ││││││││└─ Execute (others)
# │││││││└── Write (others)
# ││││││└─── Read (others)
# │││││└──── Execute (group)
# ││││└───── Write (group)
# │││└────── Read (group)
# ││└─────── Execute (owner)
# │└──────── Write (owner)
# └───────── Read (owner)
```

**Permission Numbers:**
- 4 = Read (r)
- 2 = Write (w)
- 1 = Execute (x)

Examples:
- `chmod 755 file` = rwxr-xr-x (owner: rwx, group: rx, others: rx)
- `chmod 644 file` = rw-r--r-- (owner: rw, group: r, others: r)

🛠️ **Exercise 1.5: Linux File Investigation**

```bash
# Navigate to CB Agent directory
cd /opt/Bit9/bin

# List all files with permissions
ls -lah

# Check who owns the files
ls -l | awk '{print $3, $4, $9}'  # Owner, Group, Filename

# Find files modified in last 24 hours
find /opt/Bit9 -type f -mtime -1

# Check disk usage
du -sh /opt/Bit9/*
```

#### **Systemd Service Management**

On modern Linux (RHEL 7+, CentOS 7+), services are managed by `systemd`.

```bash
# Check if CB Agent service is running
systemctl status b9daemon

# Start service
systemctl start b9daemon

# Stop service
systemctl stop b9daemon

# Enable service to start on boot
systemctl enable b9daemon

# View service logs
journalctl -u b9daemon -f  # Follow logs in real-time
journalctl -u b9daemon --since "1 hour ago"
```

**Understanding systemd unit files:**

```bash
# View service configuration
systemctl cat b9daemon

# Typical output:
[Unit]
Description=Bit9 Security Platform Agent
After=network.target

[Service]
Type=forking
ExecStart=/opt/Bit9/bin/b9daemon start
ExecStop=/opt/Bit9/bin/b9daemon stop

[Install]
WantedBy=multi-user.target
```

**What This Means:**
- `After=network.target`: Start after network is up
- `Type=forking`: Service spawns child process
- `WantedBy=multi-user.target`: Enable for multi-user runlevel

🎯 **Challenge 1.2**: Create a simple systemd service that writes to a log file every minute. Practice start/stop/enable commands.

#### **Linux Logging**

```bash
# View system log
tail -f /var/log/messages  # RHEL/CentOS
tail -f /var/log/syslog    # Ubuntu/Debian

# View CB Agent logs
tail -f /var/log/bit9/parity.log

# Search logs for errors
grep -i error /var/log/bit9/parity.log

# View logs with timestamps
journalctl --since "2025-11-27 09:00" --until "2025-11-27 10:00"
```

💡 **Support Engineer Tip**: Always collect logs before and after the issue occurred. A 1-hour window is usually sufficient.

### 1.3 Networking Fundamentals

#### **Ports Used by CB App Control**

| Port  | Protocol | Purpose | Direction |
|-------|----------|---------|-----------|
| 443   | HTTPS    | Web Console, REST API, Agent registration | Bidirectional |
| 41002 | HTTPS    | Agent-to-Server file inventory upload | Agent → Server |

🛠️ **Exercise 1.6: Network Connectivity Testing**

**From Windows Client:**
```powershell
# Test if port 443 is open
Test-NetConnection -ComputerName appcontrol -Port 443

# View active connections
netstat -ano | findstr "443"
netstat -ano | findstr "41002"

# Trace route to server
tracert 10.100.10.100

# DNS resolution test
nslookup appcontrol
```

**From Linux Client:**
```bash
# Test connectivity
ping -c 4 10.100.10.100

# Test port (telnet method)
telnet 10.100.10.100 443

# Better port test (nc - netcat)
nc -zv 10.100.10.100 443

# Check if process is listening on port
netstat -tuln | grep 443
```

**Understanding netstat Output:**
```
Proto  Local Address          Foreign Address        State
TCP    10.100.10.101:54234   10.100.10.100:443     ESTABLISHED
│      │                      │                     │
│      │                      │                     └─ Connection state
│      │                      └─ Server IP:Port
│      └─ Client IP:Ephemeral Port
└─ Protocol
```

⚠️ **Common Pitfall**: Agents can't connect if firewall blocks 443 or 41002. Always test connectivity before deploying agents.

#### **Firewall Verification**

**Windows Server (CB App Control Server):**
```powershell
# Check if firewall is on
Get-NetFirewallProfile

# List firewall rules for port 443
Get-NetFirewallRule | Where-Object {$_.Enabled -eq "True"} | 
    Get-NetFirewallPortFilter | Where-Object {$_.LocalPort -eq 443}

# Allow inbound 443 (if needed)
New-NetFirewallRule -DisplayName "CB App Control HTTPS" -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow
```

**Linux Client:**
```bash
# Check firewall status (firewalld)
systemctl status firewalld

# List rules
firewall-cmd --list-all

# Allow outbound HTTPS (usually allowed by default)
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

### 1.4 SQL Server Basics

#### **Why SQL Server Matters**

CB App Control stores ALL data in SQL Server:
- File inventory
- Policies
- Rules
- Events
- Approvals/Bans
- Agent metadata

**If SQL Server is slow or unavailable, CB App Control stops working.**

#### **Connecting to SQL Server**

🛠️ **Exercise 1.7: SQL Server Management Studio (SSMS)**

1. Open SSMS on server
2. Connect to `localhost` or `127.0.0.1`
3. Authentication: SQL Server
4. Login: `sa`
5. Password: `Gr95M23rE9`

**Key Databases:**
- `Bit9Server`: CB App Control main database
- `master`: SQL Server system database
- `msdb`: SQL Agent jobs database

**Navigate the Bit9Server Database:**
```sql
-- View all tables
USE Bit9Server;
GO

SELECT TABLE_NAME 
FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_TYPE = 'BASE TABLE'
ORDER BY TABLE_NAME;

-- Sample: View policies
SELECT * FROM [dbo].[policies];

-- Sample: View computers
SELECT * FROM [dbo].[computers];
```

#### **Basic Troubleshooting Queries**

```sql
-- Check database size
EXEC sp_spaceused;

-- View active connections
EXEC sp_who2;

-- Check if SQL Agent is running
EXEC xp_servicecontrol 'QueryState', 'SQLServerAGENT';

-- View recent errors
EXEC sp_readerrorlog 0, 1, N'error';
```

💡 **Support Engineer Tip**: When troubleshooting "slow server" issues, first check SQL performance. Look for:
- Long-running queries
- Blocking sessions
- High CPU/memory usage

🎯 **Challenge 1.3**: Write a SQL query to count how many computers are in each policy.

---

## Module 2: Carbon Black App Control Architecture Deep Dive

### 2.1 The Big Picture: How CB App Control Works

Before diving into components, let's understand the **mental model**:

```
┌─────────────────────────────────────────────────────────────┐
│                    CB App Control Concept                     │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  1. Agent inventories all files on endpoint                  │
│  2. Agent sends inventory to server                          │
│  3. Server checks each file against:                         │
│     - Approvals (whitelist)                                  │
│     - Bans (blacklist)                                       │
│     - Rules (conditional logic)                              │
│  4. Server sends verdict back to agent                       │
│  5. Agent enforces decision:                                 │
│     - Allow execution                                        │
│     - Block execution                                        │
│     - Notify user                                            │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

**Core Philosophy**: Default-deny (whitelist) approach
- If not explicitly approved → BLOCKED
- Think of it as an airport security checkpoint for executables

### 2.2 Component Architecture

#### **Server-Side Components**

```
CB App Control Server (Windows)
├── ParityServer.exe
│   ├── Role: Agent communication handler
│   ├── Port: 41002 (file inventory upload)
│   └── Function: Receives file data, updates database
│
├── Parity.exe (Server Service)
│   ├── Role: Core processing engine
│   ├── Function: Policy evaluation, rule matching
│   └── Database: Reads/writes to SQL Server
│
├── ParityReporter.exe
│   ├── Role: Cloud communication & background tasks
│   ├── Function: Sends data to Collective Defense Cloud
│   └── Tasks: Alert processing, syslog forwarding
│
├── Notifier.exe
│   ├── Role: User notification handler (server-side)
│   └── Function: Manages console alert notifications
│
└── SQL Server Database
    ├── Bit9Server (main database)
    ├── Tables: policies, computers, files, events
    └── Stored Procedures: Performance-critical operations
```

#### **Agent-Side Components (Windows)**

```
CB App Control Agent
├── Parity.sys (Kernel Driver)
│   ├── Location: C:\Windows\System32\drivers\parity.sys
│   ├── Role: File system filter driver
│   ├── Function: Intercepts file operations at kernel level
│   └── Filter Manager: Attached to volume stack
│
├── Parity.exe (Agent Service)
│   ├── Location: C:\Program Files (x86)\Bit9\Parity Agent\
│   ├── Role: User-mode agent service
│   ├── Functions:
│   │   - File hashing (MD5, SHA-1, SHA-256)
│   │   - Server communication (443)
│   │   - Policy enforcement
│   │   - Cache management
│   └── Config: Registry (HKLM\SOFTWARE\Bit9\Parity Agent)
│
├── Notifier.exe
│   ├── Role: User-facing notification window
│   ├── Function: Displays block notifications to user
│   └── Trigger: When Parity.sys blocks a file
│
└── Cache.db
    ├── Location: C:\Program Files (x86)\Bit9\Parity Agent\Cache\
    ├── Type: SQLite database
    ├── Contents:
    │   - File inventory (all files on endpoint)
    │   - Approval states (approved, unapproved, banned)
    │   - Policy configuration
    │   - Rule definitions
    └── Size: Can grow to hundreds of MB (normal)
```

#### **Agent-Side Components (Linux)**

```
CB App Control Agent (Linux)
├── b9daemon (Main Service)
│   ├── Location: /opt/Bit9/bin/b9daemon
│   ├── Role: Equivalent to Parity.exe on Windows
│   └── Systemd: b9daemon.service
│
├── b9cli (Command Line Tool)
│   ├── Location: /opt/Bit9/bin/b9cli
│   ├── Role: Equivalent to DASCLI on Windows
│   └── Usage: Agent status, troubleshooting
│
├── Cache.db
│   ├── Location: /opt/Bit9/cache/
│   └── Same purpose as Windows Cache.db
│
└── Logs
    └── Location: /var/log/bit9/parity.log
```

### 2.3 Communication Flow: Agent Registration

Let's trace what happens when you deploy an agent:

```
Step 1: Agent Installation
──────────────────────────
Windows Client            CB App Control Server
     │                           │
     │  [Installer runs]         │
     │  - Copies files           │
     │  - Creates registry keys  │
     │  - Installs Parity.sys    │
     │  - Starts Parity.exe      │
     │                           │

Step 2: Initial Connection
──────────────────────────
     │                           │
     │────── HTTPS GET ─────────>│  (Port 443)
     │  "Hello, I'm a new agent" │
     │                           │
     │<──── Server Response ─────│
     │   "Welcome! Here's your   │
     │    registration info"     │
     │                           │

Step 3: File Inventory
──────────────────────────
     │                           │
     │  [Agent scans disk]       │
     │  - Hashes all executables │
     │  - Builds file list       │
     │                           │
     │──── File Inventory ──────>│  (Port 41002)
     │   (Thousands of files)    │
     │                           │
     │                      [Server processes]
     │                      - Checks each file
     │                      - Queries reputation
     │                      - Applies rules
     │                           │
     │<──── Approval List ───────│
     │   "These files are OK"    │
     │   "Block these files"     │
     │                           │

Step 4: Ongoing Operation
──────────────────────────
     │                           │
     │  [User tries to run app]  │
     │  Notepad.exe              │
     │         │                 │
     │    [Parity.sys intercepts]│
     │         │                 │
     │    [Check Cache.db]       │
     │    - Is notepad.exe       │
     │      approved?            │
     │         │                 │
     │      YES → Allow          │
     │       NO → Block & Notify │
     │                           │
```

**Key Insight**: The agent is **autonomous**. Once it has downloaded policy and approvals, it can enforce even when disconnected from server.

### 2.4 Policy Enforcement Levels Explained

| Enforcement | What Happens | Use Case |
|-------------|--------------|----------|
| **Disabled** | No enforcement, monitoring only | Pre-deployment testing |
| **Low (Visibility)** | Allow everything, log events | Initial rollout, learning mode |
| **Medium (Local Approval)** | Block new files, but admin can approve locally | Standard corporate desktops |
| **High (Block)** | Block all unapproved files, no local approval | Servers, high-security endpoints |

**Connected vs Disconnected:**
- **Connected**: Agent has active connection to server
- **Disconnected**: Agent operates from Cache.db (last known good state)

**Real-World Scenario:**

```
Laptop in Office (Connected - Medium Enforcement):
- User tries to install Chrome
- Blocked (not approved)
- User submits approval request
- Admin approves via console
- User installs Chrome successfully

Same Laptop at Home (Disconnected - High Enforcement):
- User tries to install Chrome
- Blocked (not in Cache.db)
- NO approval request possible (offline)
- User cannot install until back in office
```

💡 **Support Engineer Tip**: Most "why is this blocked?" tickets are due to misunderstanding enforcement levels. Always check:
1. What policy is the computer in?
2. Is it connected or disconnected?
3. What's the enforcement level for that state?

### 2.5 Data Flow: File Approval Process

When a user reports "I can't run this program," here's what's happening under the hood:

```
User double-clicks program.exe
         │
         ▼
┌────────────────────────────────┐
│   Windows Explorer.exe         │
│   (CreateProcess API call)     │
└────────────────────────────────┘
         │
         ▼
┌────────────────────────────────┐
│   Windows Kernel               │
│   (ntoskrnl.exe)               │
└────────────────────────────────┘
         │
         ▼
┌────────────────────────────────┐
│   Filter Manager               │
│   (fltmgr.sys)                 │
└────────────────────────────────┘
         │
         ▼
┌────────────────────────────────┐
│   Parity.sys (CB Filter)       │
│   - Intercepts file operation  │
│   - Checks: Do I allow this?   │
└────────────────────────────────┘
         │
         ├─ Query Cache.db ────┐
         │                      │
         ▼                      ▼
    APPROVED?              BANNED?
         │                      │
        YES                    YES
         │                      │
         ▼                      ▼
    ✅ ALLOW              ❌ BLOCK
    (File runs)          (Notifier.exe shows)
         │                      │
         │                      └─> Event logged
         └─> Event logged
```

**What Cache.db Contains:**

```sql
-- Simplified view of Cache.db structure
Table: FileInstances
├── FilePath: C:\Windows\System32\notepad.exe
├── FileHash: 1A2B3C4D5E6F7G8H (SHA-256)
├── ApprovalState: Approved
├── Threat: 0 (Clean)
├── Trust: 100 (Microsoft signed)
└── LastSeen: 2025-11-27 10:30:00
```

🛠️ **Exercise 2.1: Understanding Cache.db**

1. On Windows Client (BEFORE agent installation):
```powershell
# Count how many .exe files exist
Get-ChildItem -Path C:\ -Recurse -Include *.exe -ErrorAction SilentlyContinue | Measure-Object
```

2. After agent installation, check Cache.db size:
```powershell
Get-ChildItem "C:\Program Files (x86)\Bit9\Parity Agent\Cache\Cache.db"
```

**Question**: Why is Cache.db so large? 
**Answer**: It stores metadata for every executable file on the system, plus approval state, hash, certificate info, etc.

### 2.6 Certificate and Signing

CB App Control makes approval decisions based on file attributes:

**File Attributes Hierarchy:**
```
1. File Hash (Most specific)
   └─ SHA-256 hash of exact file bytes
   └─ Example: 1A2B3C4D... (unique to this file)

2. Certificate (Publisher)
   └─ Digital signature on the file
   └─ Example: "Microsoft Corporation"

3. File Name (Least secure)
   └─ Name of the file
   └─ Example: "notepad.exe"
```

**Why This Matters for Approvals:**

If you approve by **publisher** (e.g., "Microsoft Corporation"):
- ✅ All Microsoft-signed files are approved
- ✅ Windows updates don't get blocked
- ✅ Scales well (one approval = thousands of files)

If you approve by **hash**:
- ✅ Only that exact file version is approved
- ❌ File updates require new approval
- ❌ Doesn't scale (every file needs individual approval)

🛠️ **Exercise 2.2: Certificate Investigation**

```powershell
# Check if a file is signed
Get-AuthenticodeSignature "C:\Windows\System32\notepad.exe"

# View certificate details
$sig = Get-AuthenticodeSignature "C:\Windows\System32\notepad.exe"
$sig.SignerCertificate | Format-List *

# Check multiple files
Get-ChildItem "C:\Windows\System32\*.exe" | 
    ForEach-Object {
        [PSCustomObject]@{
            File = $_.Name
            Publisher = (Get-AuthenticodeSignature $_.FullName).SignerCertificate.Subject
        }
    } | Format-Table
```

**Understanding Certificate Info:**
- **Subject**: Who owns the certificate (e.g., "CN=Microsoft Corporation")
- **Issuer**: Who issued the certificate (e.g., "VeriSign")
- **Valid From/To**: Certificate expiration dates
- **Thumbprint**: Unique ID for certificate

💡 **Support Engineer Tip**: When troubleshooting "why did this file get approved/blocked?", always check:
1. File hash approval
2. Publisher approval
3. Custom rules that might match

### 2.7 Rules Engine Logic

CB App Control processes files through a rules engine:

```
File Execution Attempt
         │
         ▼
┌─────────────────────────────────┐
│  Is file BANNED?                │
├─────────────────────────────────┤
│  YES → BLOCK (highest priority) │
│  NO  → Continue                 │
└─────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  Is file APPROVED?              │
├─────────────────────────────────┤
│  YES → ALLOW                    │
│  NO  → Continue                 │
└─────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  Do CUSTOM RULES match?         │
├─────────────────────────────────┤
│  - Trusted Path rules           │
│  - Certificate rules            │
│  - File Integrity rules         │
│  YES → Apply rule action        │
│  NO  → Continue                 │
└─────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  Policy Enforcement Level?      │
├─────────────────────────────────┤
│  - High: BLOCK                  │
│  - Medium: LOCAL APPROVAL       │
│  - Low: ALLOW (log event)       │
└─────────────────────────────────┘
```

**Priority Order (Critical to Understand):**
1. **Bans** (always block, no exceptions)
2. **Approvals** (explicit whitelist)
3. **Custom Rules** (conditional logic)
4. **Policy Default** (enforcement level)

🎯 **Challenge 2.1**: A user reports: "I approved Chrome, but it's still blocked!" What would you check first?

**Troubleshooting Steps:**
1. Is there a BAN rule on Chrome? (Bans override approvals)
2. Is the approval state correct in Cache.db? (Check via DASCLI)
3. Has the agent received the updated config list from server?
4. Is a custom rule blocking writes to the download location?

---

## Module 3: Server Components & Installation

### 3.1 ParityServer.exe - The Traffic Controller

**Role**: Handles all agent communication

**What It Does:**
```
Incoming Agent Connection (Port 41002)
         │
         ▼
┌─────────────────────────────────────┐
│  ParityServer.exe                   │
├─────────────────────────────────────┤
│  1. Authenticate agent              │
│  2. Receive file inventory          │
│  3. Parse file data                 │
│  4. Write to SQL database           │
│  5. Queue response to agent         │
└─────────────────────────────────────┘
         │
         ▼
    SQL Server (Bit9Server DB)
```

**Service Details:**
- **Service Name**: `Bit9 Parity Server`
- **Display Name**: `Bit9 Parity Server`
- **Startup Type**: Automatic
- **Log On As**: Local System (default) or dedicated service account
- **Dependencies**: SQL Server, Network service

**Common Issues:**

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| Service won't start | SQL Server not running | Start SQL Server first |
| Agents can't connect | Port 41002 blocked | Check firewall |
| High CPU usage | Large file inventory backlog | Check agent backlog stats |
| Service crashes | Database corruption | Check SQL Server logs |

🛠️ **Exercise 3.1: Monitor ParityServer**

```powershell
# Check service status
Get-Service "Bit9 Parity Server"

# View service details
Get-Service "Bit9 Parity Server" | Select-Object *

# Check if port 41002 is listening
netstat -ano | findstr "41002"

# View recent Event Log entries
Get-EventLog -LogName Application -Source "Bit9*" -Newest 20

# Monitor service in real-time
while ($true) {
    $svc = Get-Service "Bit9 Parity Server"
    Write-Host ("{0} - Status: {1}" -f (Get-Date), $svc.Status)
    Start-Sleep -Seconds 5
}
```

### 3.2 Parity.exe (Server Service) - The Brain

**Role**: Core processing engine for policy evaluation

**What It Does:**
```
File Data from ParityServer
         │
         ▼
┌─────────────────────────────────────┐
│  Parity.exe (Server)                │
├─────────────────────────────────────┤
│  1. Apply rules to files            │
│  2. Check cloud reputation          │
│  3. Calculate approval state        │
│  4. Update policies                 │
│  5. Process user requests           │
└─────────────────────────────────────┘
         │
         ├──> SQL Server (read/write)
         └──> ParityReporter (queue tasks)
```

**Service Details:**
- **Service Name**: `Bit9 Parity Service`
- **Display Name**: `Bit9 Parity Service`
- **Critical**: If this stops, console becomes unresponsive

🛠️ **Exercise 3.2: Service Dependencies**

```powershell
# View service dependencies
$svc = Get-Service "Bit9 Parity Service"
$svc.ServicesDependedOn  # What this service needs
$svc.DependentServices   # What needs this service

# Visualize dependency chain
$svc.ServicesDependedOn | ForEach-Object {
    Write-Host "Bit9 Parity Service depends on: $($_.Name)"
}
```

### 3.3 ParityReporter.exe - The Communicator

**Role**: Background tasks and cloud communication

**What It Does:**
- Sends telemetry to CB Collective Defense Cloud (port 443 outbound)
- Processes scheduled tasks (database maintenance, report generation)
- Forwards alerts to syslog/SIEM
- Manages notification emails

**Service Details:**
- **Service Name**: `Bit9 Parity Reporter`
- **Display Name**: `Bit9 Parity Reporter`
- **Can be disabled**: If no cloud connectivity needed (air-gapped)

💡 **Support Engineer Tip**: If alerts aren't being sent, check if ParityReporter is running and has internet access.

### 3.4 SQL Server Database Structure

The `Bit9Server` database contains all CB App Control data.

**Key Tables:**

```sql
-- View all tables (connect via SSMS)
USE Bit9Server;
GO

SELECT TABLE_NAME, 
       TABLE_TYPE
FROM INFORMATION_SCHEMA.TABLES
ORDER BY TABLE_NAME;

-- Key tables:
-- computers: All managed endpoints
-- files: File inventory
-- fileInstances: File locations on endpoints
-- policies: Policy definitions
-- rules: All rule types
-- events: Audit log
-- approvalRequests: Pending user requests
```

🛠️ **Exercise 3.3: Database Exploration**

```sql
-- Count computers by policy
SELECT p.name AS PolicyName, 
       COUNT(c.id) AS ComputerCount
FROM computers c
JOIN policies p ON c.policyId = p.id
GROUP BY p.name;

-- View recent events
SELECT TOP 100 
    timestamp,
    subtypeName,
    description,
    computerName
FROM events
ORDER BY timestamp DESC;

-- Check file approval states
SELECT approvalState,
       COUNT(*) AS FileCount
FROM files
GROUP BY approvalState;
```

**Understanding Approval States:**
- `0` = Unapproved
- `1` = Approved (local)
- `2` = Approved (global)
- `3` = Banned

### 3.5 Server Installation Best Practices

#### **Pre-Installation Checklist**

```
✅ Windows Server 2016+ (2019 or 2022 recommended)
✅ SQL Server 2016+ installed and running
✅ .NET Framework 4.8 or higher
✅ Ports 443 and 41002 available
✅ 50GB free disk space minimum
✅ 8GB RAM minimum (16GB recommended)
✅ Service account created (if not using Local System)
✅ SQL Server authentication configured (Mixed Mode)
✅ Firewall rules configured (if applicable)
```

#### **Installation Verification Script**

```powershell
# Save as: Verify-CBServerInstall.ps1

Write-Host "=== CB App Control Server Installation Verification ===" -ForegroundColor Cyan

# Check services
Write-Host "`n1. Checking Services..." -ForegroundColor Yellow
$services = @(
    "Bit9 Parity Server",
    "Bit9 Parity Service",
    "Bit9 Parity Reporter"
)

foreach ($svc in $services) {
    $status = (Get-Service $svc -ErrorAction SilentlyContinue).Status
    if ($status -eq "Running") {
        Write-Host "  ✓ $svc : Running" -ForegroundColor Green
    } else {
        Write-Host "  ✗ $svc : $status" -ForegroundColor Red
    }
}

# Check ports
Write-Host "`n2. Checking Ports..." -ForegroundColor Yellow
$ports = @(443, 41002)
foreach ($port in $ports) {
    $listening = netstat -ano | Select-String ":$port\s+.*LISTENING"
    if ($listening) {
        Write-Host "  ✓ Port $port is listening" -ForegroundColor Green
    } else {
        Write-Host "  ✗ Port $port is NOT listening" -ForegroundColor Red
    }
}

# Check database
Write-Host "`n3. Checking Database..." -ForegroundColor Yellow
try {
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Server=localhost;Database=Bit9Server;Integrated Security=True"
    $conn.Open()
    Write-Host "  ✓ Database connection successful" -ForegroundColor Green
    $conn.Close()
} catch {
    Write-Host "  ✗ Database connection failed: $_" -ForegroundColor Red
}

# Check web console
Write-Host "`n4. Checking Web Console..." -ForegroundColor Yellow
try {
    $response = Invoke-WebRequest -Uri "https://localhost" -UseBasicParsing -SkipCertificateCheck
    if ($response.StatusCode -eq 200) {
        Write-Host "  ✓ Web console is accessible" -ForegroundColor Green
    }
} catch {
    Write-Host "  ✗ Web console is NOT accessible: $_" -ForegroundColor Red
}

Write-Host "`n=== Verification Complete ===" -ForegroundColor Cyan
```

### 3.6 Server Configuration Files

**Key Locations:**

```
C:\Program Files\Bit9\Parity Server\
├── Config\
│   ├── ParityServer.config   # Server service config
│   └── Parity.config          # Core service config
├── Logs\
│   ├── ParityServer.log       # Server service logs
│   ├── Parity.log             # Core service logs
│   └── ParityReporter.log     # Reporter service logs
└── Tools\
    └── Installation logs
```

**Viewing Configuration (READ ONLY - Don't edit!):**

```powershell
# View ParityServer config
Get-Content "C:\Program Files\Bit9\Parity Server\Config\ParityServer.config"

# Search for specific settings
Select-String -Path "C:\Program Files\Bit9\Parity Server\Config\*.config" -Pattern "port"
```

💡 **Support Engineer Tip**: Never manually edit config files. Use the web console or support-provided scripts only.

---

## Module 4: Agent Components & Management

### 4.1 Parity.sys - The Kernel Guardian

**Role**: Kernel-mode file system filter driver

**How It Works:**

```
Application wants to execute file.exe
         │
         ▼
    CreateProcess() API call
         │
         ▼
    Windows Kernel (ntoskrnl.exe)
         │
         ▼
    Filter Manager (fltmgr.sys)
         │
         ├─ Volume Filter Stack
         │  ├─ [Other filters]
         │  ├─ [Antivirus filter]
         │  └─ Parity.sys ← CB App Control filter
         │
         ▼
    File System Driver (NTFS, etc.)
         │
         ▼
    Physical Disk
```

**What Parity.sys Intercepts:**
- `IRP_MJ_CREATE` - File open operations
- `IRP_MJ_WRITE` - File write operations
- `IRP_MJ_SET_INFORMATION` - File attribute changes
- `IRP_MJ_CLEANUP` - File close operations

**Filter Altitude**: Each filter driver has an "altitude" (priority):
- Higher altitude = processed first
- CB App Control altitude: `328010` (industry standard for application control)

🛠️ **Exercise 4.1: View Filter Drivers**

```cmd
# List all loaded filter drivers
fltmc

# Sample output:
Filter Name                     Num Instances    Altitude    Frame
------------------------------  -------------  ------------  -----
parity                                      3    328010         0
luafv                                       1    135000         0
npsvctrig                                   1     46000         0
WdFilter                                   12    328010         0

# View specific filter details
fltmc instances -f parity

# Detach filter (DANGEROUS - only for troubleshooting!)
# fltmc unload parity
```

**Understanding the Output:**
- **Num Instances**: How many volumes the filter is attached to (should match number of drives)
- **Altitude**: Priority in filter stack
- **Frame**: Filter manager framework version

💡 **Support Engineer Tip**: If `fltmc` doesn't show parity, the kernel driver failed to load. Check Windows Event Log → System for driver load errors.

**Common Parity.sys Issues:**

| Symptom | Cause | Troubleshooting |
|---------|-------|-----------------|
| Driver not listed in fltmc | Failed to load at boot | Check Event Viewer: System log for error 7000 |
| Blue Screen (BSOD) on boot | Driver incompatibility | Boot into Safe Mode, uninstall agent |
| Files not being blocked | Driver detached | Run `fltmc instances` to verify attachment |

### 4.2 Parity.exe (Agent Service) - The Enforcer

**Role**: User-mode agent service that coordinates with kernel driver

**What It Does:**

```
Parity.exe (Agent Service)
├── File Hashing Engine
│   ├── MD5 (legacy, fast)
│   ├── SHA-1 (standard)
│   └── SHA-256 (most secure)
│
├── Server Communication
│   ├── Register with server (port 443)
│   ├── Upload file inventory (port 41002)
│   ├── Download policy updates
│   └── Heartbeat every 60 seconds
│
├── Cache Management
│   ├── Maintains Cache.db
│   ├── Syncs with server database
│   └── Handles offline mode
│
└── Policy Enforcement
    ├── Receives block/allow from Parity.sys
    ├── Checks Cache.db for approval
    └── Logs events
```

**Service Configuration:**

```powershell
# View agent service
Get-Service Parity

# Service properties
Get-CimInstance Win32_Service -Filter "Name='Parity'" | 
    Select-Object Name, StartMode, State, StartName, PathName
```

**Registry Configuration:**

```
HKEY_LOCAL_MACHINE\SOFTWARE\Bit9\Parity Agent
├── ServerAddress (REG_SZ): https://appcontrol
├── ServerPort (REG_DWORD): 443
├── CacheFolder (REG_SZ): C:\Program Files (x86)\Bit9\Parity Agent\Cache
├── ConfigListVersion (REG_DWORD): Current config list number
└── LastPolicyUpdate (REG_QWORD): Timestamp of last policy sync
```

🛠️ **Exercise 4.2: Inspect Agent Configuration**

```powershell
# Read agent registry settings
$agentReg = "HKLM:\SOFTWARE\Bit9\Parity Agent"
Get-ItemProperty -Path $agentReg | Format-List

# Check server address
$serverAddr = (Get-ItemProperty -Path $agentReg).ServerAddress
Write-Host "Agent connects to: $serverAddr"

# Check when policy was last updated
$lastUpdate = (Get-ItemProperty -Path $agentReg).LastPolicyUpdate
$dateTime = [DateTime]::FromFileTime($lastUpdate)
Write-Host "Last policy update: $dateTime"
```

### 4.3 Cache.db - The Local Brain

**Role**: SQLite database containing agent's local copy of all enforcement data

**Database Schema (Simplified):**

```
Cache.db (SQLite)
├── fileInstances
│   ├── filePath (text)
│   ├── fileHash (text, SHA-256)
│   ├── approvalState (integer: 0=unapproved, 1=approved, 3=banned)
│   ├── threat (integer: 0=clean, 100=malicious)
│   ├── trust (integer: 0=unknown, 100=trusted)
│   └── lastSeen (datetime)
│
├── policies
│   ├── policyId (integer)
│   ├── policyName (text)
│   ├── enforcementLevel (integer)
│   └── configData (blob)
│
└── rules
    ├── ruleId (integer)
    ├── ruleType (text: ban, approval, custom)
    ├── ruleData (blob)
    └── enabled (boolean)
```

**Cache.db Lifecycle:**

```
Agent Initialization
         │
         ▼
┌─────────────────────────────────┐
│ Scan local file system          │
│ - Hash all executables           │
│ - Store in Cache.db              │
│ - Mark as "unapproved"           │
└─────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│ Upload inventory to server       │
│ (This can take hours for         │
│  systems with many files)        │
└─────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│ Server returns approvals         │
│ - Update Cache.db                │
│ - Mark files as approved/banned  │
└─────────────────────────────────┘
         │
         ▼
    Agent is now operational
```

**Cache Consistency Check:**

When you see "Cache Consistency Check" in the console:
- Agent is re-scanning the file system
- Ensures Cache.db matches actual disk state
- Triggered when: agent was offline, files changed, or manually initiated

💡 **Support Engineer Tip**: Large Cache.db files (500MB+) are normal on servers with many files. Don't delete it - that forces re-initialization!

🛠️ **Exercise 4.3: Analyze Cache.db**

```powershell
# Check Cache.db size
$cachePath = "C:\Program Files (x86)\Bit9\Parity Agent\Cache\Cache.db"
$cacheFile = Get-Item $cachePath
Write-Host "Cache.db Size: $([math]::Round($cacheFile.Length / 1MB, 2)) MB"

# Count files in inventory (requires SQLite CLI tool)
# Download sqlite3.exe from sqlite.org
cd "C:\Program Files (x86)\Bit9\Parity Agent\Cache"
.\sqlite3.exe Cache.db "SELECT COUNT(*) FROM fileInstances;"

# View sample data
.\sqlite3.exe Cache.db "SELECT filePath, approvalState FROM fileInstances LIMIT 10;"
```

**Interpreting Cache.db Data:**

```sql
-- Connect to Cache.db using sqlite3

-- Count files by approval state
SELECT 
    CASE approvalState
        WHEN 0 THEN 'Unapproved'
        WHEN 1 THEN 'Approved (Local)'
        WHEN 2 THEN 'Approved (Global)'
        WHEN 3 THEN 'Banned'
        ELSE 'Unknown'
    END AS State,
    COUNT(*) AS Count
FROM fileInstances
GROUP BY approvalState;

-- Find high-threat files
SELECT filePath, threat, trust
FROM fileInstances
WHERE threat > 50
ORDER BY threat DESC
LIMIT 20;
```

### 4.4 Notifier.exe - The User Interface

**Role**: Displays block notifications to end users

**What Happens When a File is Blocked:**

```
User tries to execute blocked.exe
         │
         ▼
    Parity.sys intercepts
         │
         ▼
    Checks Cache.db → BLOCKED
         │
         ▼
    Parity.sys returns "ACCESS_DENIED"
         │
         ▼
    Parity.exe receives block event
         │
         ▼
    Launches Notifier.exe
         │
         ▼
┌────────────────────────────────┐
│  Security Notification         │
├────────────────────────────────┤
│  This file has been blocked    │
│  by Carbon Black App Control   │
│                                │
│  File: blocked.exe             │
│  Reason: Unapproved file       │
│                                │
│  [Request Approval]  [Close]   │
└────────────────────────────────┘
```

**Notifier Customization:**

You can customize notification text in the console:
- Settings → System Settings → UI Settings
- Change text for: Block message, Approval request, Notify-only message

🛠️ **Exercise 4.4: Trigger a Block Notification**

1. Ensure Windows Client is in High Enforcement policy
2. Download a new executable (e.g., PuTTY)
3. Try to run it
4. Observe Notifier.exe popup
5. Click "Request Approval"
6. Fill in justification and email

**What Happened Behind the Scenes:**
1. Parity.sys blocked execution
2. Parity.exe logged event
3. Notifier.exe displayed popup
4. Approval request sent to server
5. Event appears in console: Tools → Approval Requests

### 4.5 Agent Deployment Methods

#### **Method 1: Manual Installation (Lab/Testing)**

```powershell
# Download agent installer from server console
# Navigate to: Assets → Computers → Add Computer

# Install silently
Start-Process -FilePath ".\AgentSetup.exe" -ArgumentList "/s /v`" /qn SERVER=https://appcontrol`"" -Wait

# Verify installation
Get-Service Parity
```

#### **Method 2: Group Policy Object (GPO) - Production**

```
1. Copy agent installer to network share
2. Create GPO in Active Directory
3. Computer Configuration → Policies → Software Settings → Software Installation
4. New Package → Select AgentSetup.msi
5. Link GPO to target OU
6. Wait for Group Policy refresh (90 minutes or gpupdate /force)
```

#### **Method 3: SCCM/Intune (Enterprise)**

```
1. Import agent package to SCCM/Intune
2. Create deployment task sequence
3. Include command line: 
   msiexec /i AgentSetup.msi /quiet SERVER=https://appcontrol
4. Deploy to target collection
5. Monitor deployment status
```

#### **Method 4: Cloning/Imaging (VDI)**

**DO NOT clone agents!** Each agent needs unique identity.

**Correct Process:**
1. Install OS and applications
2. Create master image WITHOUT CB agent
3. Deploy image
4. Install CB agent post-deployment (via GPO/script)

💡 **Support Engineer Tip**: Cloned agents cause "duplicate agent" issues. Each agent has unique GUID stored in registry and Cache.db.

### 4.6 Agent Upgrade Process

**How Upgrades Work:**

```
Server Console
     │
     └─ Upload new agent installer
         │
         └─ Create upgrade task
             │
             └─ Select target computers
                 │
                 ▼
              Agents download installer
                 │
                 ▼
              Local upgrade executes
                 │
                 ├─ Stop Parity service
                 ├─ Install new files
                 ├─ Preserve Cache.db
                 ├─ Start Parity service
                 └─ Report status to server
```

**Best Practices:**
1. Test upgrade on pilot group first
2. Schedule upgrades during maintenance window
3. Monitor agent status after upgrade
4. Keep Cache.db intact (don't delete during upgrade)

🛠️ **Exercise 4.5: Check Agent Version**

```powershell
# Method 1: Registry
$agentReg = "HKLM:\SOFTWARE\Bit9\Parity Agent"
$version = (Get-ItemProperty -Path $agentReg).AgentVersion
Write-Host "Agent Version: $version"

# Method 2: File version
$parityExe = "C:\Program Files (x86)\Bit9\Parity Agent\Parity.exe"
(Get-Item $parityExe).VersionInfo | Format-List

# Method 3: DASCLI (we'll cover this in Module 9)
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status
```

### 4.7 Agent Troubleshooting Flowchart

```
Agent Issue Reported
         │
         ▼
    Is service running?
    (Get-Service Parity)
         │
    ┌────┴────┐
   NO        YES
    │          │
    ▼          ▼
Start it   Is driver loaded?
 │         (fltmc | findstr parity)
 │              │
 │         ┌────┴────┐
 │        NO        YES
 │         │          │
 │         ▼          ▼
 │    Reinstall   Can it connect to server?
 │    agent      (Test-NetConnection -Port 443)
 │                    │
 │               ┌────┴────┐
 │              NO        YES
 │               │          │
 │               ▼          ▼
 │          Check       Check Cache.db
 │          firewall/   (Is it current?)
 │          network          │
 │                      ┌────┴────┐
 │                     NO        YES
 │                      │          │
 │                      ▼          ▼
 │                 Force sync  Check policy
 │                 (dascli     assignment
 │                  refreshcache)   │
 │                                   │
 └───────────────────────────────────┘
                 │
                 ▼
         Collect diagnostics
         (dascli capture)
```

---

## Module 5: Policies & Enforcement Levels

### 5.1 Policy Fundamentals

**What is a Policy?**
- A collection of settings that determines how CB App Control behaves on an endpoint
- Think of it as a "security profile" assigned to groups of computers

**Policy Components:**

```
Policy: "Standard Corporate"
├── Enforcement Levels
│   ├── Connected: Medium (Local Approval)
│   └── Disconnected: High (Block)
│
├── Rules Assigned
│   ├── Software Rules (ban PuTTY)
│   ├── Custom Rules (trusted path: C:\Approved\)
│   └── Advanced Rules (allow code compilation)
│
├── Settings
│   ├── Track File Changes: Enabled
│   ├── Monitor Process Activity: Enabled
│   ├── Tamper Protection: Enabled
│   └── Cache Consistency: Every 24 hours
│
└── Computers Assigned
    ├── WORKSTATION-001
    ├── WORKSTATION-002
    └── ... (500 computers)
```

### 5.2 Understanding Enforcement Levels in Depth

Let's break down each enforcement level with real-world scenarios.

#### **Enforcement Level: Disabled**

**What Happens:**
- Agent is installed but not enforcing
- All files allowed to execute
- Events are NOT logged
- Use case: Pre-deployment testing

```
User Action: Run unknown.exe
         │
         ▼
    Parity.sys sees it
         │
         ▼
    Policy: DISABLED
         │
         ▼
    ✅ ALLOW (no logging)
```

💡 **Support Engineer Tip**: Disabled mode is different from uninstalling the agent. Driver is loaded, but not active.

#### **Enforcement Level: Low (Visibility Mode)**

**What Happens:**
- All files allowed to execute
- Events ARE logged
- Use case: Learning mode, baselining environment

```
User Action: Run unknown.exe
         │
         ▼
    Parity.sys sees it
         │
         ▼
    Policy: LOW ENFORCEMENT
         │
         ├─> ✅ ALLOW execution
         └─> 📝 LOG event to server
```

**Real-World Scenario:**

```
Day 1: Deploy agents in Low Enforcement
Days 2-7: Collect data on what users run
Day 8: Review data, create approval rules
Day 9: Switch to Medium Enforcement
```

🛠️ **Exercise 5.1: Low Enforcement Data Analysis**

After running in Low mode for 1 week:

```sql
-- Connect to Bit9Server database
USE Bit9Server;

-- Top 100 most-run unapproved files
SELECT TOP 100
    f.fileName,
    f.publisher,
    COUNT(*) AS ExecutionCount
FROM events e
JOIN files f ON e.fileId = f.id
WHERE e.subtypeName = 'File executed'
  AND f.approvalState = 0  -- Unapproved
  AND e.timestamp >= DATEADD(day, -7, GETDATE())
GROUP BY f.fileName, f.publisher
ORDER BY ExecutionCount DESC;
```

**Analysis Questions:**
1. Are there many Microsoft-signed files? (Should be approved by publisher)
2. Are there business applications? (Approve by publisher or hash)
3. Are there unknown/unsigned files? (Investigate before approving)

#### **Enforcement Level: Medium (Local Approval Mode)**

**What Happens:**
- Approved files: Execute normally
- Unapproved files: BLOCKED, but admin can approve locally
- Use case: Standard corporate desktops with power users

```
User Action: Run unknown.exe
         │
         ▼
    Parity.sys checks Cache.db
         │
    ┌────┴────┐
 APPROVED  UNAPPROVED
    │          │
    ▼          ▼
✅ ALLOW   ❌ BLOCK
            │
            └─> Notifier popup
                 │
                 └─> User requests approval
                      │
                      └─> Admin reviews & approves locally
                           │
                           └─> File now approved in Cache.db
                                │
                                └─> User can run it
```

**Local Approval Methods:**

1. **Via Console UI:**
   - Tools → Approval Requests
   - Review request
   - Click "Approve File Locally"

2. **Via File Details:**
   - Assets → Find Files
   - Search for file
   - Click "Approve Locally"

3. **Via Event:**
   - Reports → Events
   - Find block event
   - Right-click → Approve File Locally

💡 **Support Engineer Tip**: Local approvals only apply to that specific computer. If you want global approval, use "Approve Globally" instead.

🛠️ **Exercise 5.2: Practice Local Approval Flow**

From Lab 2, Task 5 (modified):

1. Move Windows Client to a new policy: "Medium Enforcement Test"
   ```
   Policy Settings:
   - Connected: Medium
   - Disconnected: High
   ```

2. Try to run an unapproved file (e.g., SlackSetup.exe from Downloads)

3. Observe block notification

4. Submit approval request:
   - Justification: "Required for team communication"
   - Email: admin@localhost.com

5. As admin, approve the request:
   ```
   Console → Tools → Approval Requests
   → Click on request
   → Review details (Trust: 90, Threat: 0)
   → Click "Approve File Locally"
   ```

6. Try running SlackSetup.exe again → Should work!

7. Check approval state:
   ```sql
   -- In SQL Server
   USE Bit9Server;
   
   SELECT 
       f.fileName,
       f.approvalState,
       c.computerName
   FROM files f
   JOIN fileInstances fi ON f.id = fi.fileId
   JOIN computers c ON fi.computerId = c.id
   WHERE f.fileName LIKE '%slack%';
   ```

#### **Enforcement Level: High (Block Mode)**

**What Happens:**
- Approved files: Execute normally
- Unapproved files: BLOCKED, NO local approval possible
- Use case: Servers, high-security endpoints, production systems

```
User Action: Run unknown.exe
         │
         ▼
    Parity.sys checks Cache.db
         │
    ┌────┴────┐
 APPROVED  UNAPPROVED
    │          │
    ▼          ▼
✅ ALLOW   ❌ BLOCK
            │
            └─> Notifier popup (no approval button)
                 │
                 └─> User CANNOT proceed
                      │
                      └─> Must contact admin
                           │
                           └─> Admin approves via console
                                │
                                └─> Agent syncs Cache.db
                                     │
                                     └─> User can run file
```

**High Enforcement Workflow:**

```
User encounters block
     │
     ▼
Creates support ticket
     │
     ▼
Admin investigates file
     │
     ├─> Check threat/trust scores
     ├─> Research file purpose
     └─> Verify it's legitimate
         │
         ▼
    Admin approves globally
         │
         └─> All agents sync new approval
              │
              └─> File now runs on all systems
```

💡 **Support Engineer Tip**: High enforcement is not "maximum security"—it's about **change control**. It prevents unapproved software, but approved malware would still run.

🛠️ **Exercise 5.3: High Enforcement Scenario**

**Scenario**: Your DMZ servers are in High Enforcement. A critical patch needs to be deployed.

**Before patch deployment:**

```powershell
# On patch file
$patchFile = "C:\Patches\CriticalUpdate-KB5001234.exe"

# Get file hash
$hash = (Get-FileHash $patchFile -Algorithm SHA256).Hash

# Pre-approve in console:
# Rules → Software Rules → Add File Rule
# Rule Type: Approval
# File Type: Hash
# Hash: [paste SHA-256 hash]
# Rule Applies To: DMZ Policy

# Wait for agents to sync (check Policy Status = "Up to date")

# Deploy patch
```

**Why This Works:**
- Patch is pre-approved by hash
- Agents sync approval before patch runs
- No blocks, no user interruption

### 5.3 Connected vs. Disconnected Enforcement

**Connected State:**
- Agent has active TCP connection to server
- Can upload events, download policy updates
- Uses "Connected" enforcement level

**Disconnected State:**
- Agent cannot reach server (offline, VPN disconnected, server down)
- Uses local Cache.db for decisions
- Uses "Disconnected" enforcement level
- Cannot submit approval requests

**Grace Period:**
- When agent first loses connectivity, there's a configurable grace period (default: 30 minutes)
- During grace period, agent stays in "Connected" enforcement
- After grace period expires, switches to "Disconnected" enforcement

🛠️ **Exercise 5.4: Test Disconnected Behavior**

1. Check current agent connection:
   ```cmd
   cd "C:\Program Files (x86)\Bit9\Parity Agent"
   dascli.exe status
   ```
   Look for: `Connection: Connected`

2. Block agent from reaching server:
   ```powershell
   # Add firewall rule to block outbound 443
   New-NetFirewallRule -DisplayName "Block CB Agent" -Direction Outbound -RemoteAddress 10.100.10.100 -Action Block
   ```

3. Wait for grace period to expire (or set grace period to 1 minute in policy settings for testing)

4. Check agent status again:
   ```cmd
   dascli.exe status
   ```
   Look for: `Connection: Disconnected`

5. Test behavior:
   - Try to run approved file → Works (from Cache.db)
   - Try to run unapproved file → Blocked (per disconnected enforcement)

6. Restore connectivity:
   ```powershell
   Remove-NetFirewallRule -DisplayName "Block CB Agent"
   ```

**Real-World Scenario:**

```
Laptop User Workflow:

Office (Connected - Medium):
├─ Can install approved software
├─ Can request approvals for new software
└─ Admin approves quickly

Coffee Shop (Disconnected - High):
├─ Cannot install ANY new software
├─ Can only run previously approved apps
└─ Must return to office for approvals
```

💡 **Support Engineer Tip**: "Why can't I install software at home?" → Check if laptop is in Disconnected High enforcement.

### 5.4 Creating Policies - Lab Walkthrough

Let's work through Lab 2 from your materials, with deep explanations.

#### **Lab 2, Task 1: Create "Security IT Endpoints" Policy**

**Scenario**: Your security team needs flexibility to test new tools, but you still want monitoring.

```
Policy Requirements:
- Allow testing of unapproved software
- Track all file changes for audit
- Monitor activity without blocking
```

**Step-by-Step (with WHY):**

1. **Navigate to Policies:**
   ```
   Console → Rules → Policies
   ```

2. **Click "Add Policy"**

3. **Configure Settings:**
   ```
   Policy Name: Security IT Endpoints
   Description: Policy for Security IT team
   Mode: Control   ← Actively enforcing (vs. Report-only)
   
   Enforcement Levels:
   - Connected: Low    ← Allow everything, log events
   - Disconnected: Low ← Same when offline
   
   Initial Settings: Template Policy  ← Inherits defaults
   Track File Changes: ✓ Enabled      ← Monitor writes/mods
   ```

4. **Click "Save & Exit"**

**What You Just Created:**

A policy that:
- ✅ Allows all software to run (Low enforcement)
- ✅ Logs every execution for audit trail
- ✅ Tracks file changes (creations, modifications)
- ✅ Maintains same behavior when offline

**Testing the Policy:**

```powershell
# Assign a test computer to this policy
# Console → Assets → Computers → Select computer
# Action → Move Computer to Policy: Security IT Endpoints

# Wait for policy status = "Up to date"

# Try running any file → Works!

# Check events
# Console → Reports → Events
# Filter: Policy = "Security IT Endpoints"
# You'll see execution events being logged
```

#### **Lab 2, Task 2: Create "Standard Corporate" Policy**

**Scenario**: Corporate desktops need flexibility at the office, but security when remote.

```
Policy Requirements:
- Office (Connected): Users can request approvals
- Remote (Disconnected): Locked down, no new software
```

**Configuration:**

```
Policy Name: Standard Corporate
Mode: Control

Enforcement Levels:
- Connected: Medium       ← Local approval possible
- Disconnected: High      ← Full lockdown offline

Initial Settings: Template Policy
Track File Changes: ✓ Enabled
```

**Why This Design:**
- At office: IT can quickly approve legitimate software
- At home/travel: Laptop is protected, can't install malware accidentally

#### **Lab 2, Task 3: Create "DMZ" Policy**

**Scenario**: DMZ servers are production, internet-facing. Maximum security required.

```
Policy Requirements:
- No unapproved software ever
- No user-initiated approvals
- All software changes must be pre-approved by admin
```

**Configuration:**

```
Policy Name: DMZ
Description: Policy for DMZ Sub Network
Mode: Control

Enforcement Levels:
- Connected: High         ← Block all unapproved
- Disconnected: High      ← Same offline

Track File Changes: ✓ Enabled
```

**Why High/High:**
- DMZ servers should be static (no software changes without change control)
- If compromised, attacker can't install tools
- All software must be pre-approved before deployment

🎯 **Challenge 5.1**: Design a policy for developer workstations that allows them to compile and test code in `C:\Dev\`, but blocks everything else.

**Hint**: You'll need High enforcement + Custom Rules (we'll cover in Module 6)

### 5.5 Policy Assignment Strategies

**Method 1: Manual Assignment (Small Environments)**
```
Console → Assets → Computers
Select computer(s) → Action → Move Computer to Policy
```

**Method 2: Automatic by Computer Name Pattern (Medium Environments)**
```
Console → Rules → Policies → Select Policy
Advanced Options → Computer Naming Rule
Pattern: DMZ-* (matches DMZ-WEB01, DMZ-DB01, etc.)
```

**Method 3: Automatic by IP Range (Network-Based)**
```
Console → Rules → Policies → Select Policy
Advanced Options → Computer IP Range
Range: 10.100.20.0/24 (all computers in DMZ subnet)
```

**Method 4: Automatic by Active Directory (Enterprise)**
```
Prerequisites:
- CB App Control integrated with AD
- Computers in specific OUs

Console → Rules → Policies → Select Policy
Advanced Options → Active Directory OU
OU: OU=Workstations,OU=Corporate,DC=company,DC=com
```

💡 **Support Engineer Tip**: Always document your policy assignment logic. When troubleshooting "why is this computer in the wrong policy?", you'll need to trace the assignment rules.

### 5.6 Configuration List (CL) - The Version Number

**What is the Config List?**
- A version number that increments with every policy/rule change
- Agents track their current CL version
- Server sends updated CL when changes are made

```
Server Makes Change
     │
     ▼
Config List increments (CL: 145 → 146)
     │
     ▼
Agents connect and see new CL available
     │
     ▼
Agents download CL 146
     │
     ▼
Agents update Cache.db
     │
     ▼
Policy Status shows "Up to date"
```

**Checking CL Version:**

```powershell
# On agent
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status

# Look for line:
# Config List: 146
```

```sql
-- On server (SQL query)
USE Bit9Server;

SELECT 
    c.computerName,
    c.policyName,
    c.configListVersion AS AgentCL,
    (SELECT MAX(version) FROM configList) AS ServerCL,
    CASE 
        WHEN c.configListVersion = (SELECT MAX(version) FROM configList) 
        THEN 'Up to date'
        ELSE 'Outdated'
    END AS Status
FROM computers c
ORDER BY Status, computerName;
```

**Common Issue**: Agent shows "Outdated" policy status

**Troubleshooting:**

```
Agent Policy Status: Outdated
         │
         ▼
Step 1: Verify connectivity
├─> Can agent reach server?
├─> Test-NetConnection -ComputerName appcontrol -Port 443
└─> If NO → Fix network/firewall
         │
         ▼
Step 2: Check agent service
├─> Is Parity service running?
├─> Get-Service Parity
└─> If STOPPED → Start service, investigate why it stopped
         │
         ▼
Step 3: Force config refresh
├─> dascli.exe refreshconfig
└─> Wait 60 seconds, check status again
         │
         ▼
Step 4: Check server CL processing
├─> Are there pending CL jobs?
├─> SQL: SELECT * FROM configList WHERE processed = 0
└─> If YES → Server may be backlogged
         │
         ▼
Step 5: Review agent logs
├─> Check Parity.log for errors
├─> Look for: "Failed to download config list"
└─> Address specific error
```

🛠️ **Exercise 5.5: Force Config List Refresh**

```powershell
# Before refresh
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status | Select-String "Config List"

# Force immediate refresh
.\dascli.exe refreshconfig

# Monitor status
Start-Sleep -Seconds 10
.\dascli.exe status | Select-String "Config List"

# Verify in console
# Console → Assets → Computers → Select computer
# Check "Policy Status" column → Should show "Up to date"
```

**Why CL Matters:**

Without updated config list:
- New approvals don't take effect
- Policy changes don't apply
- Rules don't activate
- Agent operates on stale data

💡 **Support Engineer Tip**: When users report "I approved this file but it's still blocked," first check if agent CL matches server CL.

### 5.7 Policy Cloning and Inheritance

**Scenario**: You have 10 policies that all need the same new rule applied.

**Option 1: Manual (Tedious)**
- Edit each policy individually
- Apply rule to each
- Risk of inconsistency

**Option 2: Template Policy + Cloning (Smart)**

```
Step 1: Create "Master Template" Policy
├─> Configure all common settings
├─> Add common rules
└─> Save

Step 2: Clone for specific use cases
├─> Clone → "Workstation Template"
│   └─> Modify: Connected = Medium
├─> Clone → "Server Template"
│   └─> Modify: Connected = High
└─> Clone → "Laptop Template"
    └─> Modify: Disconnected = High
```

**How to Clone:**

```
Console → Rules → Policies
→ Select source policy
→ Action → Clone Policy
→ Name: [New Policy Name]
→ Modify settings as needed
→ Save
```

**Inheritance Example:**

```
Template Policy (Parent)
├── Track File Changes: Enabled
├── Monitor Process Activity: Enabled
├── Tamper Protection: Enabled
└── Rule: Block PowerShell scripts in temp folders

   ↓ Clone ↓

Standard Corporate (Child)
├── ✓ Inherits all parent settings
├── ✓ Inherits parent rules
└── Modification: Connected = Medium
```

💡 **Support Engineer Tip**: Use cloning to maintain consistency. When you update the template, you can re-clone or manually update children.

### 5.8 Policy-Specific Settings Deep Dive

#### **Track File Changes**

**What it does**: Monitors file creation, modification, deletion

**Events generated:**
- File created
- File modified
- File deleted
- File renamed

**Use case**: Compliance, forensic investigation, ransomware detection

**Performance impact**: Minimal (events are async)

🛠️ **Exercise 5.6: Test File Change Tracking**

```powershell
# Ensure policy has "Track File Changes" enabled

# Create a test file
New-Item -Path C:\Temp\test.txt -ItemType File -Value "Hello"

# Modify it
Add-Content -Path C:\Temp\test.txt -Value "`nWorld"

# Delete it
Remove-Item -Path C:\Temp\test.txt

# Check events in console
# Console → Reports → Events
# Filter: Event Type = "File Change"
# You'll see all three operations logged
```

#### **Monitor Process Activity**

**What it does**: Tracks process creation, termination, and lineage

**Events generated:**
- Process started
- Process terminated
- Parent-child process relationships

**Use case**: Detect unusual process chains (e.g., Word spawning PowerShell)

**Query Example:**

```sql
-- Find processes spawned by Microsoft Office
USE Bit9Server;

SELECT 
    e.timestamp,
    e.computerName,
    parentProcess.fileName AS ParentProcess,
    childProcess.fileName AS ChildProcess
FROM events e
JOIN files parentProcess ON e.parentFileId = parentProcess.id
JOIN files childProcess ON e.fileId = childProcess.id
WHERE parentProcess.fileName IN ('WINWORD.EXE', 'EXCEL.EXE', 'POWERPNT.EXE')
  AND childProcess.fileName IN ('powershell.exe', 'cmd.exe', 'wscript.exe')
  AND e.timestamp >= DATEADD(day, -7, GETDATE())
ORDER BY e.timestamp DESC;
```

**Why This Matters**: Malicious macros often spawn command interpreters.

#### **Tamper Protection**

**What it does**: Prevents users from disabling/uninstalling CB agent

**Protections:**
- Service cannot be stopped by non-admin
- Agent files cannot be deleted
- Registry keys cannot be modified
- Driver cannot be unloaded

**How it works:**

```
User attempts: sc stop Parity
         │
         ▼
Windows Service Control Manager
         │
         ▼
Parity service receives stop request
         │
         ▼
Checks: Is requester an approved admin?
         │
    ┌────┴────┐
   YES        NO
    │          │
    ▼          ▼
 Allow      Deny & log event
```

**Tamper Protection Bypass (Emergency):**

If you need to manually stop agent for troubleshooting:

```powershell
# Method 1: Via DASCLI (preferred)
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe stop

# Method 2: Boot into Safe Mode
# - Tamper protection inactive in Safe Mode
# - Stop service manually
# - NOT recommended except for emergencies
```

⚠️ **Warning**: Disabling tamper protection or stopping agent removes protection. Only do this for legitimate troubleshooting.

#### **Cache Consistency Interval**

**What it does**: Periodic full disk scan to ensure Cache.db matches reality

**Options:**
- Never (manual only)
- Every 24 hours (default)
- Every 12 hours
- Every 6 hours
- On every agent startup

**When it runs:**
```
Cache Consistency Check Started
         │
         ▼
Agent scans all executable locations
├─ C:\Windows\
├─ C:\Program Files\
├─ C:\Program Files (x86)\
└─ User profiles
         │
         ▼
Compare hashes against Cache.db
         │
    ┌────┴────┐
 MATCH    NEW/CHANGED
    │          │
    ▼          ▼
 No action   Update Cache.db
              │
              └─> Send new files to server
```

**Performance Impact:**
- High during scan (disk I/O intensive)
- Can take 30 minutes to several hours on file servers

💡 **Support Engineer Tip**: Schedule cache consistency during maintenance windows for servers with millions of files.

🛠️ **Exercise 5.7: Trigger Manual Cache Consistency**

```powershell
# Check current cache status
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status | Select-String "Cache"

# Trigger manual consistency check
.\dascli.exe refreshcache

# Monitor progress
Get-EventLog -LogName Application -Source "Bit9*" -Newest 10

# In console, you'll see:
# Assets → Computers → Select computer
# Status shows: "Cache consistency in progress"
```

**When to Force Cache Consistency:**
1. After restoring from backup
2. After disk corruption
3. When file approvals seem out of sync
4. When agent reports "cache inconsistent" errors

### 5.9 Multi-Tenancy and Policy Isolation (Advanced)

**Scenario**: Managed Service Provider (MSP) managing multiple customers

**Problem**: Don't want Customer A to see Customer B's data

**Solution**: Multi-tenancy

```
CB App Control Server
├── Customer A Group
│   ├── Policies: "Customer A - Workstations"
│   ├── Computers: Only Customer A endpoints
│   └── Admins: Customer A IT staff (limited view)
│
└── Customer B Group
    ├── Policies: "Customer B - Servers"
    ├── Computers: Only Customer B endpoints
    └── Admins: Customer B IT staff (limited view)
```

**How to Configure:**

```
1. Create Computer Groups
   Console → Assets → Computer Groups → Add Group
   Name: Customer A
   
2. Create Role-Based Access Control
   Console → Admin → Users → Add User
   Username: customer-a-admin
   Role: Limited Administrator
   Scope: Computer Group = "Customer A"
   
3. Assign policies to groups
   Console → Rules → Policies → Select policy
   Advanced Options → Restrict to Computer Group: "Customer A"
```

**Access Control Matrix:**

| Role | Can View All Policies | Can Modify All Policies | Can See All Computers |
|------|----------------------|------------------------|----------------------|
| Global Admin | ✅ | ✅ | ✅ |
| Group Admin | ❌ (only their group) | ❌ (only their group) | ❌ (only their group) |
| Read-Only | ✅ (all, but no changes) | ❌ | ✅ |

💡 **Support Engineer Tip**: Multi-tenancy prevents accidental cross-contamination but adds complexity. Document your group structure clearly.

---

## Module 6: Rules Engine Mastery

### 6.1 Rule Types Overview

CB App Control has multiple rule types that work together:

```
Rule Hierarchy (Evaluation Order)
│
├── 1. BAN RULES (Highest priority)
│   └── Always block, override everything
│
├── 2. APPROVAL RULES
│   └── Explicitly allow files
│
├── 3. CUSTOM RULES
│   ├── Trusted Directory Rules
│   ├── Memory Rules
│   ├── Certificate Rules
│   └── Script Rules
│
└── 4. POLICY DEFAULT (Lowest priority)
    └── Enforcement level determines default action
```

**Key Principle**: Most specific wins

```
Example Conflict:
- BAN rule: Block putty.exe (by hash)
- APPROVAL rule: Approve all files from C:\Tools\

User runs C:\Tools\putty.exe
         │
         ▼
BAN rule matches → BLOCKED
(BAN overrides APPROVAL)
```

### 6.2 File Approval Rules

**Approval Criteria:**

1. **By Hash (Most Specific)**
   - Exact file match
   - Example: SHA-256 = 1A2B3C4D...
   - Use case: Approve specific version of software

2. **By Certificate (Publisher)**
   - All files signed by publisher
   - Example: "Microsoft Corporation"
   - Use case: Approve all Microsoft updates

3. **By File Name**
   - Any file with this name (least secure)
   - Example: notepad.exe
   - Use case: Legacy/unsigned internal tools

**Creating Approval Rules:**

🛠️ **Exercise 6.1: Approve by Hash**

```
Scenario: User needs to run PuTTY.exe (downloaded from official site)

Step 1: Get file details
Console → Assets → Find Files
Search: putty.exe
→ Click on file entry
→ Note: Hash, Threat Score, Trust Score

Step 2: Create approval rule
Console → Rules → Software Rules → Add Rule
Rule Type: Approval
Rule Name: PuTTY SSH Client
File Specification:
  ├─ Hash: [paste SHA-256]
  └─ Applies to: All Computers (or specific policy)
Save

Step 3: Verify
→ Computer downloads updated config list
→ Try running putty.exe → Success!
```

🛠️ **Exercise 6.2: Approve by Publisher**

```
Scenario: Approve all Adobe software

Step 1: Find an Adobe file
Console → Assets → Find Files
Search: acrobat.exe
→ Click file
→ Note: Publisher = "Adobe Inc."

Step 2: Create certificate rule
Console → Rules → Software Rules → Add Rule
Rule Type: Approval
Rule Name: Adobe Software
File Specification:
  ├─ Publisher: Adobe Inc.
  ├─ Applies to: All Computers
  └─ Description: Approve all Adobe products
Save

Step 3: Test
→ Install Adobe Reader on test machine
→ Should install without blocks
→ Check events: File approved via publisher rule
```

**Publisher Approval Best Practices:**

✅ **DO:**
- Approve well-known publishers (Microsoft, Adobe, Google)
- Verify certificate validity before approval
- Document why publisher was approved

❌ **DON'T:**
- Approve unknown publishers without research
- Use publisher approval for internal/unsigned tools
- Forget to check certificate expiration dates

💡 **Support Engineer Tip**: When approving by publisher, always check the publisher certificate first. Malware sometimes uses fake/expired certificates.

**Certificate Investigation:**

```powershell
# Check file certificate
$file = "C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrobat.exe"
$sig = Get-AuthenticodeSignature $file

# View certificate details
$sig.SignerCertificate | Select-Object Subject, Issuer, NotBefore, NotAfter, Thumbprint

# Verify certificate chain
$sig.SignerCertificate.Verify()  # Returns True if valid
```

### 6.3 Ban Rules

**When to Use Bans:**
- Known malware
- Unauthorized tools (hacking tools, keyloggers)
- License violations (banned software in your org)
- Policy violations (personal software on corporate devices)

**Ban Rule Criteria (same as approvals):**
- By Hash
- By Publisher
- By File Name

🛠️ **Exercise 6.3: Create a Ban Rule**

```
Scenario: Ban cryptocurrency miners

Step 1: Research known miner hashes
(In real world, you'd get these from threat intel)
For lab: Use any executable as test

Step 2: Create ban rule
Console → Rules → Software Rules → Add Rule
Rule Type: Ban
Rule Name: Cryptocurrency Miners
File Specification:
  ├─ Hash: [SHA-256 of miner]
  ├─ Applies to: All Computers
  └─ Description: Block crypto mining software
Save

Step 3: Test
→ Place banned file on test machine
→ Try to execute
→ BLOCKED immediately
→ Notifier shows: "This file is banned by your administrator"
```

**Ban Rule Priority Test:**

```
Setup:
1. Create ban rule for putty.exe (by hash)
2. Create approval rule for C:\Tools\ (trusted directory)
3. Place putty.exe in C:\Tools\

Expected Result:
→ Try to run C:\Tools\putty.exe
→ BLOCKED (ban overrides trusted directory)

Verification:
Console → Reports → Events
→ Event: "File blocked by ban rule"
→ Rule: [Ban rule name]
```

💡 **Support Engineer Tip**: Bans cannot be overridden by any other rule. Use with caution—you can accidentally ban critical system files.

⚠️ **Horror Story**: Admin once created ban rule for `svchost.exe` (Windows critical process) by name. Every Windows machine blue-screened on reboot. Always test bans on isolated systems first!

### 6.4 Custom Rules - Trusted Directories

**Concept**: "I trust everything in this folder"

**Use Cases:**
- Developer build output: `C:\Dev\Build\`
- Internal apps repository: `\\fileserver\apps\`
- Application temp folders

**How It Works:**

```
User runs: C:\Dev\Build\myapp.exe
         │
         ▼
Parity.sys intercepts
         │
         ▼
Check: Is file in trusted directory?
         │
         ├─ Rule: Trust C:\Dev\Build\* (recursively)
         ▼
     YES → ALLOW (bypass hash checking)
```

**Creating Trusted Directory Rules:**

🛠️ **Exercise 6.4: Trusted Directory Setup**

```
Scenario: Developers compile code in C:\Dev\

Step 1: Create directory rule
Console → Rules → Custom Rules → Add Rule
Rule Type: Trusted Directory
Rule Name: Developer Build Output
Path Specification:
  ├─ Path: C:\Dev\**\*  (recursive)
  ├─ Applies to: Policy = "Developer Workstations"
  └─ Include Subdirectories: ✓ Enabled
Save

Step 2: Test
→ Compile a new .exe in C:\Dev\MyProject\bin\
→ Run immediately without waiting for approval
→ Check event: "File allowed by trusted directory rule"
```

**Path Wildcards:**

```
C:\Dev\*                → Only files directly in C:\Dev\
C:\Dev\**\*             → All files in C:\Dev\ and subdirectories
C:\Dev\*.exe            → Only .exe files in C:\Dev\
C:\Program Files\*\*.dll → All DLLs one level deep
```

**Security Considerations:**

❌ **DANGEROUS:**
```
Trusted Directory: C:\Users\**\*
Why: Users could place malware in any subfolder
Result: Complete bypass of whitelisting
```

✅ **SAFE:**
```
Trusted Directory: C:\InternalApps\
Why: Only admins can write to this folder
Result: Controlled trust boundary
```

**Best Practices:**

1. **Restrict write access** to trusted directories
   ```powershell
   # Set permissions so only admins can write
   $acl = Get-Acl "C:\InternalApps"
   $acl.SetAccessRuleProtection($true, $false)  # Disable inheritance
   
   # Grant read/execute to users
   $userRule = New-Object System.Security.AccessControl.FileSystemAccessRule(
       "BUILTIN\Users",
       "ReadAndExecute",
       "ContainerInherit,ObjectInherit",
       "None",
       "Allow"
   )
   $acl.AddAccessRule($userRule)
   
   # Grant full control to admins only
   $adminRule = New-Object System.Security.AccessControl.FileSystemAccessRule(
       "BUILTIN\Administrators",
       "FullControl",
       "ContainerInherit,ObjectInherit",
       "None",
       "Allow"
   )
   $acl.AddAccessRule($adminRule)
   
   Set-Acl "C:\InternalApps" $acl
   ```

2. **Audit trusted directory activity**
   ```sql
   -- Monitor trusted directory executions
   USE Bit9Server;
   
   SELECT 
       e.timestamp,
       e.computerName,
       e.userName,
       f.filePath,
       r.ruleName
   FROM events e
   JOIN files f ON e.fileId = f.id
   JOIN rules r ON e.ruleId = r.id
   WHERE r.ruleType = 'TrustedDirectory'
     AND e.timestamp >= DATEADD(day, -7, GETDATE())
   ORDER BY e.timestamp DESC;
   ```

3. **Never trust user-writable locations**
   ```
   ❌ C:\Users\
   ❌ C:\Temp\
   ❌ C:\Windows\Temp\
   ❌ %APPDATA%
   ```

### 6.5 Memory Rules - Advanced Execution Control

**Concept**: Control process execution based on runtime behavior, not just files

**Use Cases:**
- Block scripts executed from memory (PowerShell, VBScript)
- Block DLL injection
- Prevent code execution from non-executable memory

**Memory Rule Types:**

1. **Script Execution Rules**
   ```
   Rule: Block PowerShell scripts from temp folders
   
   Condition:
   - Process: powershell.exe
   - Script Path: C:\Users\*\AppData\Local\Temp\*
   
   Action: BLOCK
   ```

2. **DLL Load Rules**
   ```
   Rule: Block unsigned DLLs loading into signed processes
   
   Condition:
   - Target Process: Signed by Microsoft
   - DLL: Not signed
   
   Action: BLOCK (prevents DLL injection attacks)
   ```

🛠️ **Exercise 6.5: Block PowerShell in Temp**

```
Scenario: Prevent malware from running PowerShell scripts in temp folders

Step 1: Create memory rule
Console → Rules → Custom Rules → Add Rule
Rule Type: Memory Rule
Rule Name: Block PowerShell Scripts in Temp
Process: powershell.exe
Script Path: C:\Users\*\AppData\Local\Temp\*.ps1
Action: Block
Applies to: All Computers
Save

Step 2: Test
→ Create test script:
   C:\Users\YourUser\AppData\Local\Temp\test.ps1
   Content: Write-Host "Hello"

→ Try to run:
   powershell.exe -File C:\Users\YourUser\AppData\Local\Temp\test.ps1

→ BLOCKED with event: "Script execution blocked by memory rule"
```

**Memory Rule Limitations:**

- Higher performance impact than file rules (runtime checks)
- Requires deep understanding of process behavior
- Can have false positives if too aggressive

💡 **Support Engineer Tip**: Memory rules are powerful but complex. Start with well-documented examples from CB documentation before creating custom ones.

### 6.6 File Integrity Rules

**Concept**: Monitor critical files for unauthorized changes

**Use Cases:**
- Detect tampering of system files
- Monitor configuration file changes
- Compliance (PCI-DSS, SOX)

**How It Works:**

```
System File: C:\Windows\System32\drivers\etc\hosts
         │
         ▼
CB Agent maintains hash baseline
         │
         ▼
File changes detected
         │
         ▼
Alert generated + optionally revert change
```

🛠️ **Exercise 6.6: Monitor Hosts File**

```
Step 1: Create file integrity rule
Console → Rules → Custom Rules → Add Rule
Rule Type: File Integrity
Rule Name: Monitor Hosts File Changes
File Path: C:\Windows\System32\drivers\etc\hosts
Action: Alert Only (or Block & Revert)
Applies to: All Computers
Save

Step 2: Test
→ Edit hosts file:
   notepad C:\Windows\System32\drivers\etc\hosts
   Add line: 127.0.0.1 test.local
   Save

→ Check alert:
   Console → Tools → Alerts
   Alert: "Hosts file modified"
```

**File Integrity Actions:**

| Action | What Happens | Use Case |
|--------|--------------|----------|
| **Alert Only** | Log event, send notification | Monitoring, audit |
| **Block Change** | Prevent file modification | Critical config files |
| **Block & Revert** | Prevent and restore original | System files, compliance |

### 6.7 Script Rules

**Concept**: Control script interpreter behavior (PowerShell, VBScript, JScript, Python)

**Common Patterns:**

1. **Block Script Execution by Location**
   ```
   Rule: Block .vbs files from Downloads folder
   Pattern: C:\Users\*\Downloads\*.vbs
   Action: Block
   ```

2. **Block Script Execution by Content**
   ```
   Rule: Block scripts containing "Invoke-WebRequest"
   Pattern: Regex match in script content
   Action: Block + Alert
   ```

3. **Whitelist Approved Scripts**
   ```
   Rule: Allow admin scripts
   Pattern: C:\AdminScripts\**\*
   Action: Allow
   ```

🛠️ **Exercise 6.7: Prevent Malicious Macros**

```
Scenario: Block macros from Office documents in temp/downloads

Step 1: Create script rule
Console → Rules → Custom Rules → Add Rule
Rule Type: Script Rule
Rule Name: Block Office Macros in Temp
Interpreter: wscript.exe, cscript.exe
Script Path: 
  - C:\Users\*\Downloads\*
  - C:\Users\*\AppData\Local\Temp\*
Action: Block
Applies to: Standard Corporate Policy
Save

Step 2: Test
→ Create malicious macro test (fake):
   - Open Excel
   - Insert VBA macro that writes file
   - Save as .xlsm in Downloads
   → Try to open → Macro blocked!
```

**Script Rule Logging:**

```sql
-- Find blocked script executions
USE Bit9Server;

SELECT 
    e.timestamp,
    e.computerName,
    e.userName,
    e.description,
    f.filePath AS ScriptPath
FROM events e
JOIN files f ON e.fileId = f.id
WHERE e.subtypeName = 'Script blocked'
  AND e.timestamp >= DATEADD(day, -1, GETDATE())
ORDER BY e.timestamp DESC;
```

### 6.8 Rule Precedence Deep Dive

**Complex Scenario**:

```
Setup:
- Ban rule: Block putty.exe (by hash)
- Approval rule: Approve all files signed by "SimonTatham"
- Trusted Directory: C:\Tools\**\*
- Policy: High Enforcement

File: putty.exe
- Location: C:\Tools\putty.exe
- Publisher: SimonTatham
- Hash: [matches ban rule]

Question: Is it blocked or allowed?
```

**Evaluation Order:**

```
Step 1: Check BAN rules
├─ Ban rule matches putty.exe hash
└─ RESULT: BLOCKED ❌
    │
    └─ Stop processing (ban overrides everything)
```

**Answer**: BLOCKED (bans always win)

**Another Scenario**:

```
Setup:
- Approval rule: Approve calc.exe (by hash)
- Trusted Directory: C:\Windows\System32\
- File Integrity rule: Block changes to C:\Windows\System32\*

File: calc.exe
- Location: C:\Windows\System32\calc.exe
- Action: User tries to replace it with modified version

Question: What happens?
```

**Evaluation:**

```
Step 1: Check BAN rules → No match

Step 2: Check File Integrity rules
├─ calc.exe is monitored
├─ Change detected (hash mismatch)
└─ RESULT: BLOCKED ❌ + File reverted to original
```

**Answer**: BLOCKED by file integrity rule (protects against tampering even if file is approved)

🎯 **Challenge 6.1**: Design a ruleset for this requirement:

```
Requirement:
- Allow Python developers to run scripts in C:\Dev\
- Block all Python execution from Downloads
- Allow approved Python packages from C:\Python\Lib\
- Ban execution of obfuscated Python scripts

Design your rules and explain the precedence.
```

**Solution Approach:**

```
Rule Set Design:

1. BAN RULE (Priority 1)
   Name: Block Obfuscated Python
   Pattern: *.pyc (compiled Python), *.pyo (optimized)
   Applies to: All Computers
   
2. TRUSTED DIRECTORY (Priority 2)
   Name: Developer Python Workspace
   Path: C:\Dev\**\*.py
   Applies to: Policy = "Developers"
   
3. TRUSTED DIRECTORY (Priority 3)
   Name: Python Standard Library
   Path: C:\Python\Lib\**\*.py
   Applies to: All Computers
   
4. CUSTOM RULE - Script Block (Priority 4)
   Name: Block Python in Downloads
   Interpreter: python.exe
   Script Path: C:\Users\*\Downloads\*
   Action: Block
   
Precedence Explanation:
- Ban rule (#1) blocks obfuscated scripts everywhere, even in C:\Dev\
- Trusted directories (#2, #3) allow approved locations
- Script block (#4) denies execution from Downloads
- Policy enforcement handles everything else
```

---

## Module 7: Event Rules & Automation

### 7.1 Understanding Events

**What Are Events?**
- Actions logged by CB App Control
- Stored in SQL Server database (events table)
- Viewable in console: Reports → Events

**Event Categories:**

```
File Events
├── File executed
├── File blocked
├── File created
├── File modified
├── File deleted
└── File renamed

Process Events
├── Process started
├── Process terminated
└── Process relationship (parent-child)

Agent Events
├── Agent started
├── Agent stopped
├── Policy updated
├── Cache consistency started/completed
└── Connection state changed

System Events
├── Rule triggered
├── Approval requested
├── User notification
└── Alert generated
```

🛠️ **Exercise 7.1: Event Exploration**

```sql
-- Connect to Bit9Server database

-- Count events by type (last 24 hours)
SELECT 
    subtypeName,
    COUNT(*) AS EventCount
FROM events
WHERE timestamp >= DATEADD(hour, -24, GETDATE())
GROUP BY subtypeName
ORDER BY EventCount DESC;

-- Top blocked files
SELECT TOP 20
    f.fileName,
    f.publisher,
    COUNT(*) AS BlockCount
FROM events e
JOIN files f ON e.fileId = f.id
WHERE e.subtypeName = 'File blocked'
  AND e.timestamp >= DATEADD(day, -7, GETDATE())
GROUP BY f.fileName, f.publisher
ORDER BY BlockCount DESC;

-- User with most block events (potential issue?)
SELECT TOP 10
    e.userName,
    e.computerName,
    COUNT(*) AS BlockCount
FROM events e
WHERE e.subtypeName = 'File blocked'
  AND e.timestamp >= DATEADD(day, -1, GETDATE())
GROUP BY e.userName, e.computerName
ORDER BY BlockCount DESC;
```

**Interpreting Results:**

```
Top Blocked File: chrome_installer.exe (50 blocks)
         │
         ▼
Questions to Ask:
1. Is this a legitimate file? (Check trust/threat scores)
2. Why are users trying to install Chrome? (Policy issue?)
3. Should we approve Chrome? (Business decision)
4. Is this malware disguised as Chrome? (Investigate hash)
```

### 7.2 Event Rules - Automated Response

**Concept**: "When X event happens, do Y automatically"

**Event Rule Components:**

```
Event Rule
├── Trigger Condition
│   └── "File executed" from Downloads folder
│
├── Filter Criteria
│   ├── Publisher = NULL (unsigned)
│   └── Threat > 50
│
└── Action
    ├── Send email alert
    ├── Ban file automatically
    └── Isolate computer (if integrated with EDR)
```

🛠️ **Exercise 7.2: Auto-Ban High-Threat Files**

```
Scenario: Automatically ban any file with threat score > 90

Step 1: Create event rule
Console → Rules → Event Rules → Add Rule
Rule Name: Auto-Ban Malware
Trigger Event: File executed
Conditions:
  ├─ Threat Score: Greater than

90
  └─ Approval State: Unapproved
Action:
  ├─ Ban file (by hash)
  └─ Send email notification to: security@company.com
Applies to: All Computers
Save

Step 2: Test (simulated)
→ In test environment, manually set a file's threat score to 95
→ Try to execute it
→ Event rule triggers:
   - File auto-banned
   - Email sent
   - Event logged: "File banned by event rule"
```

**Common Event Rule Use Cases:**

| Use Case | Trigger | Action |
|----------|---------|--------|
| **Auto-approve trusted publishers** | File blocked + Publisher = "Microsoft" | Approve by publisher |
| **Alert on sensitive file access** | File opened + Path = "C:\Confidential\*" | Send SIEM alert |
| **Ban execution from temp** | File executed + Path = "*\Temp\*" + Unsigned | Ban file |
| **Track installer activity** | File created + Extension = ".msi" | Log to separate table |

### 7.3 Notification Rules

**Concept**: Send alerts based on events

**Notification Methods:**
- Email
- Syslog
- SNMP trap
- Custom webhook

🛠️ **Exercise 7.3: Email Alert for Banned Files**

```
Step 1: Configure SMTP settings
Console → Admin → System Settings → Email Settings
SMTP Server: smtp.gmail.com
Port: 587
Authentication: ✓ Enabled
Username: alerts@company.com
Password: [app password]
Save

Step 2: Create notification rule
Console → Rules → Event Rules → Add Rule
Rule Name: Notify on Ban Attempts
Trigger Event: File blocked
Conditions:
  └─ Block Reason: Banned file
Action:
  └─ Send email to: security@company.com
     Subject: [CB Alert] Banned File Execution Attempt
     Body: 
       Computer: {computerName}
       User: {userName}
       File: {fileName}
       Path: {filePath}
       Time: {timestamp}
Save

Step 3: Test
→ Try to execute a banned file
→ Check email inbox for alert
```

**Email Template Variables:**

```
Available Variables:
{computerName}     → Endpoint hostname
{userName}         → User who triggered event
{fileName}         → Name of file
{filePath}         → Full path to file
{fileHash}         → SHA-256 hash
{timestamp}        → Event date/time
{threatScore}      → Threat score (0-100)
{trustScore}       → Trust score (0-100)
{publisher}        → File publisher
{eventType}        → Type of event
{description}      → Event description
```

### 7.4 Integration with SIEM

**Syslog Forwarding Setup:**

```
Step 1: Enable syslog in CB
Console → Admin → System Settings → Syslog Settings
Enable Syslog: ✓
Syslog Server: 10.100.10.200
Port: 514
Protocol: UDP
Facility: Local0
Severity: Informational
Save

Step 2: Configure which events to forward
Console → Rules → Event Rules
→ Select events to forward (e.g., all block events)
→ Action: Send to Syslog
```

**Sample Syslog Message:**

```
<134>Nov 27 10:30:15 WINDOWS-CLIENT Bit9: type=FileBlocked computer=WINDOWS-CLIENT user=jdoe file=malware.exe path=C:\Users\jdoe\Downloads\malware.exe hash=1A2B3C4D... threat=95
```

**Parsing in Splunk:**

```
index=cb_appcontrol sourcetype=syslog
| rex field=_raw "type=(?<event_type>\w+) computer=(?<computer>\S+) user=(?<user>\S+) file=(?<file>\S+) threat=(?<threat>\d+)"
| where threat > 80
| stats count by computer, user, file
```

### 7.5 Custom Scripts via Event Rules (Advanced)

**Concept**: Execute PowerShell/Bash scripts when events occur

**Use Case**: Auto-quarantine infected files

🛠️ **Exercise 7.4: Auto-Quarantine High-Threat Files**

```powershell
# Save as: C:\Scripts\Quarantine-File.ps1

param(
    [string]$FilePath,
    [string]$ComputerName,
    [string]$FileHash
)

# Quarantine location
$quarantineFolder = "\\fileserver\quarantine\$ComputerName"

# Create quarantine folder if it doesn't exist
if (!(Test-Path $quarantineFolder)) {
    New-Item -ItemType Directory -Path $quarantineFolder -Force
}

# Move file to quarantine
try {
    Move-Item -Path $FilePath -Destination "$quarantineFolder\$($FileHash)_$(Split-Path $FilePath -Leaf)" -Force
    
    # Log success
    Write-EventLog -LogName Application -Source "CB Quarantine" -EventId 1000 -Message "File quarantined: $FilePath"
    
    exit 0
} catch {
    # Log failure
    Write-EventLog -LogName Application -Source "CB Quarantine" -EventId 1001 -EntryType Error -Message "Failed to quarantine: $_"
    
    exit 1
}
```

**Event Rule Configuration:**

```
Console → Rules → Event Rules → Add Rule
Rule Name: Auto-Quarantine Malware
Trigger Event: File blocked
Conditions:
  └─ Threat Score: Greater than 90
Action:
  └─ Execute Script:
     Script Path: C:\Scripts\Quarantine-File.ps1
     Parameters: -FilePath "{filePath}" -ComputerName "{computerName}" -FileHash "{fileHash}"
Applies to: All Computers
Save
```

💡 **Support Engineer Tip**: Custom scripts are powerful but increase complexity. Always test thoroughly in lab before deploying to production.

---

## Module 8: Troubleshooting Tools & Techniques

### 8.1 Troubleshooting Methodology

**The Support Engineer's Mindset:**

```
Issue Reported
     │
     ▼
1. GATHER INFORMATION
   ├─ What is the symptom?
   ├─ When did it start?
   ├─ What changed recently?
   └─ Can you reproduce it?
     │
     ▼
2. ISOLATE THE PROBLEM
   ├─ Is it one computer or many?
   ├─ Is it one user or many?
   ├─ Is it one file or many?
   └─ Is it policy-related or technical?
     │
     ▼
3. CHECK THE BASICS
   ├─ Is the service running?
   ├─ Is there network connectivity?
   ├─ Is the agent up to date?
   └─ Is the policy correct?
     │
     ▼
4. REVIEW LOGS
   ├─ Agent logs
   ├─ Server logs
   ├─ Windows Event Logs
   └─ SQL Server logs
     │
     ▼
5. FORM HYPOTHESIS
   └─ Based on symptoms + logs
     │
     ▼
6. TEST HYPOTHESIS
   ├─ Make ONE change
   └─ Observe result
     │
     ▼
7. DOCUMENT & RESOLVE
   ├─ What was the root cause?
   ├─ What was the fix?
   └─ How to prevent recurrence?
```

### 8.2 Agent Diagnostic Tools

#### **dascli.exe / b9cli Status**

**Primary Diagnostic Tool on Windows:**

```powershell
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status
```

**Sample Output:**

```
Product Version: 8.9.4.23
Agent Status: Running
Connection: Connected
Server: https://appcontrol:443
Policy: Standard Corporate
Policy Status: Up to date
Config List: 146
Last Server Contact: 2025-11-27 10:28:15
Cache Status: Consistent
Cache Size: 487 MB
Files in Inventory: 45,231
Tamper Protection: Enabled
```

**Interpreting Status Output:**

| Field | Good Value | Problem Indicators |
|-------|-----------|-------------------|
| **Agent Status** | Running | Stopped, Starting (stuck) |
| **Connection** | Connected | Disconnected, Connection Failed |
| **Policy Status** | Up to date | Outdated, Sync Pending |
| **Cache Status** | Consistent | Inconsistent, Rebuilding |
| **Last Server Contact** | Within 5 minutes | > 1 hour ago |

🛠️ **Exercise 8.1: Status Interpretation**

```powershell
# Get detailed status
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status > C:\Temp\agent-status.txt

# Review each field
Get-Content C:\Temp\agent-status.txt

# Check for problems
$status = .\dascli.exe status
if ($status -match "Disconnected") {
    Write-Host "PROBLEM: Agent is disconnected" -ForegroundColor Red
}
if ($status -match "Outdated") {
    Write-Host "PROBLEM: Policy is outdated" -ForegroundColor Red
}
if ($status -match "Inconsistent") {
    Write-Host "PROBLEM: Cache is inconsistent" -ForegroundColor Red
}
```

#### **dascli.exe Commands Reference**

```powershell
# View all commands
.\dascli.exe help

# Key commands:
.\dascli.exe status              # Current agent status
.\dascli.exe refreshconfig       # Force policy update
.\dascli.exe refreshcache        # Force cache consistency check
.\dascli.exe stop                # Stop agent service (bypasses tamper protection)
.\dascli.exe start               # Start agent service
.\dascli.exe capture             # Collect diagnostic bundle
.\dascli.exe version             # Agent version info
```

**Usage Examples:**

```powershell
# Problem: Agent shows "Policy Outdated"
.\dascli.exe refreshconfig
Start-Sleep -Seconds 30
.\dascli.exe status  # Should now show "Up to date"

# Problem: Files not being detected correctly
.\dascli.exe refreshcache
# This starts cache consistency check (can take hours on servers)

# Problem: Agent misbehaving, need to restart
.\dascli.exe stop
Start-Sleep -Seconds 5
.\dascli.exe start
```

### 8.3 Log File Analysis

#### **Windows Agent Logs**

```
Location: C:\Program Files (x86)\Bit9\Parity Agent\logs\
Key Files:
├── Parity.log          # Main agent activity log
├── ParityDebug.log     # Detailed debug information
└── Notifier.log        # User notification log
```

**Reading Parity.log:**

```
Sample Log Entry:
2025-11-27 10:30:15,234 [INFO] FileEvent: C:\Users\jdoe\Downloads\setup.exe blocked (unapproved)
│                        │      │
│                        │      └─ Event description
│                        └─ Log level
└─ Timestamp

Log Levels:
- INFO: Normal operations
- WARN: Potential issues (not blocking operations)
- ERROR: Problems that affect functionality
- DEBUG: Detailed technical information
```

🛠️ **Exercise 8.2: Log Analysis**

```powershell
# View recent errors
$logPath = "C:\Program Files (x86)\Bit9\Parity Agent\logs\Parity.log"
Select-String -Path $logPath -Pattern "ERROR" | Select-Object -Last 20

# Find blocks in last hour
$oneHourAgo = (Get-Date).AddHours(-1).ToString("yyyy-MM-dd HH:mm")
Select-String -Path $logPath -Pattern "blocked" | 
    Where-Object {$_.Line -match $oneHourAgo} |
    ForEach-Object {$_.Line}

# Search for specific file
Select-String -Path $logPath -Pattern "putty.exe"

# Export errors to file for analysis
Select-String -Path $logPath -Pattern "ERROR" | 
    Out-File C:\Temp\parity-errors.txt
```

**Common Log Patterns:**

| Pattern | Meaning | Action Required |
|---------|---------|-----------------|
| `Connection failed` | Can't reach server | Check network/firewall |
| `Certificate validation failed` | SSL/TLS issue | Check server certificate |
| `Database locked` | Cache.db in use | Stop agent, check for corruption |
| `Permission denied` | File access issue | Check NTFS permissions |
| `Memory allocation failed` | Resource exhaustion | Check system resources |

#### **Linux Agent Logs**

```bash
# Location: /var/log/bit9/parity.log

# View recent entries
tail -f /var/log/bit9/parity.log

# Search for errors
grep -i error /var/log/bit9/parity.log

# View logs from specific time
journalctl -u b9daemon --since "2025-11-27 10:00" --until "2025-11-27 11:00"

# Follow logs in real-time
journalctl -u b9daemon -f
```

### 8.4 Server-Side Troubleshooting

#### **Server Service Logs**

```
Location: C:\Program Files\Bit9\Parity Server\logs\
Key Files:
├── Parity.log              # Core server operations
├── ParityServer.log        # Agent communication
├── ParityReporter.log      # Cloud communication, tasks
└── ParityInstall.log       # Installation logs
```

🛠️ **Exercise 8.3: Server Log Analysis**

```powershell
# Check for service errors
$serverLogPath = "C:\Program Files\Bit9\Parity Server\logs\Parity.log"
Select-String -Path $serverLogPath -Pattern "ERROR|FATAL" | Select-Object -Last 50

# Find SQL connection issues
Select-String -Path $serverLogPath -Pattern "SQL|database"

# Check agent connection problems
$commLogPath = "C:\Program Files\Bit9\Parity Server\logs\ParityServer.log"
Select-String -Path $commLogPath -Pattern "connection failed|timeout"
```

#### **SQL Server Troubleshooting**

**Common SQL Issues:**

1. **Database Growing Too Large**
   ```sql
   -- Check database size
   USE Bit9Server;
   EXEC sp_spaceused;
   
   -- Check table sizes
   SELECT 
       t.NAME AS TableName,
       SUM(a.total_pages) * 8 / 1024 AS TotalSpaceMB
   FROM sys.tables t
   INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
   INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
   INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
   GROUP BY t.NAME
   ORDER BY TotalSpaceMB DESC;
   
   -- Usually culprit: events table
   ```

2. **Slow Queries**
   ```sql
   -- Find long-running queries
   SELECT 
       session_id,
       start_time,
       status,
       command,
       wait_type,
       wait_time,
       cpu_time,
       total_elapsed_time/1000 AS elapsed_seconds
   FROM sys.dm_exec_requests
   WHERE database_id = DB_ID('Bit9Server')
     AND session_id <> @@SPID  -- Exclude current session
   ORDER BY total_elapsed_time DESC;
   ```

3. **Blocking Sessions**
   ```sql
   -- Find blocking chains
   SELECT 
       blocking.session_id AS BlockingSessionID,
       blocked.session_id AS BlockedSessionID,
       blocked_sql.text AS BlockedQuery,
       blocking_sql.text AS BlockingQuery
   FROM sys.dm_exec_requests blocked
   INNER JOIN sys.dm_exec_requests blocking ON blocked.blocking_session_id = blocking.session_id
   CROSS APPLY sys.dm_exec_sql_text(blocked.sql_handle) AS blocked_sql
   CROSS APPLY sys.dm_exec_sql_text(blocking.sql_handle) AS blocking_sql
   WHERE blocking.session_id IS NOT NULL;
   
   -- Kill blocking session if necessary (CAUTION!)
   -- KILL [SessionID]
   ```

### 8.5 Diagnostic Data Collection

#### **dascli.exe capture (Windows)**

**Automated diagnostic bundle:**

```powershell
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe capture

# Output: C:\Program Files (x86)\Bit9\Parity Agent\diagnostic_[timestamp].zip

# Contents:
# - Agent logs
# - System info
# - Registry exports
# - Event logs (relevant entries)
# - Cache.db metadata (not entire database)
```

#### **b9cli diagnostics (Linux)**

```bash
cd /opt/Bit9/bin
sudo ./b9cli diagnostics

# Output: /opt/Bit9/diagnostics_[timestamp].tar.gz
```

#### **Manual Data Collection**

When `capture` isn't sufficient:

```powershell
# Create collection folder
$timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$collectionPath = "C:\Temp\CB_Diagnostics_$timestamp"
New-Item -ItemType Directory -Path $collectionPath

# Collect agent logs
Copy-Item "C:\Program Files (x86)\Bit9\Parity Agent\logs\*" -Destination $collectionPath

# Export agent registry
reg export "HKLM\SOFTWARE\Bit9" "$collectionPath\agent-registry.reg"

# Collect system info
systeminfo > "$collectionPath\systeminfo.txt"
Get-Service | Where-Object {$_.Name -like "*Bit9*" -or $_.Name -like "*Parity*"} | 
    Format-List * > "$collectionPath\services.txt"

# Collect Event Logs
Get-EventLog -LogName Application -Source "*Bit9*" -Newest 500 | 
    Export-Csv "$collectionPath\application-events.csv" -NoTypeInformation

# Test connectivity
Test-NetConnection -ComputerName appcontrol -Port 443 > "$collectionPath\connectivity.txt"
Test-NetConnection -ComputerName appcontrol -Port 41002 >> "$collectionPath\connectivity.txt"

# Compress for sending
Compress-Archive -Path $collectionPath -DestinationPath "C:\Temp\CB_Diagnostics_$timestamp.zip"

Write-Host "Diagnostics collected: C:\Temp\CB_Diagnostics_$timestamp.zip"
```

### 8.6 Common Issues & Solutions

#### **Issue 1: Agent Shows "Disconnected"**

**Symptoms:**
- Agent status: Disconnected
- Policy status: Outdated
- Last contact: Hours/days ago

**Troubleshooting:**

```powershell
# Step 1: Verify network connectivity
Test-NetConnection -ComputerName appcontrol -Port 443
Test-NetConnection -ComputerName appcontrol -Port 41002

# Step 2: Check DNS resolution
nslookup appcontrol

# Step 3: Verify agent service
Get-Service Parity

# Step 4: Check firewall
Get-NetFirewallRule | Where-Object {$_.Enabled -eq "True" -and $_.Direction -eq "Outbound"} |
    Get-NetFirewallPortFilter | Where-Object {$_.RemotePort -eq 443}

# Step 5: Review agent logs
Select-String -Path "C:\Program Files (x86)\Bit9\Parity Agent\logs\Parity.log" -Pattern "connection"
```

**Common Causes & Fixes:**

| Cause | Fix |
|-------|-----|
| Firewall blocking | Allow outbound 443, 41002 |
| Wrong server address in registry | Update via DASCLI or console |
| Proxy requires authentication | Configure proxy in agent settings |
| Server certificate expired | Renew server SSL certificate |
| Agent service crashed | Restart service, check logs for why |

#### **Issue 2: Files Blocked Incorrectly**

**Symptoms:**
- Legitimate file blocked
- File should be approved but isn't

**Troubleshooting:**

```
Step 1: Check file approval state
Console → Assets → Find Files
→ Search for file
→ Check: Approval State, Threat, Trust

Step 2: Verify policy assignment
Console → Assets → Computers
→ Find computer
→ Check: Policy, Enforcement Level

Step 3: Check for ban rules
Console → Rules → Software Rules
→ Search for file hash/name
→ Is there a ban rule?

Step 4: Check agent cache
On endpoint:
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe status
→ Is policy up to date?

Step 5: Force policy refresh
.\dascli.exe refreshconfig
Wait 30 seconds
Try file again
```

#### **Issue 3: Cache.db Corruption**

**Symptoms:**
- Agent status: Cache Inconsistent
- Files approved but still blocked
- High CPU usage by Parity.exe

**Fix:**

```powershell
# Step 1: Stop agent
cd "C:\Program Files (x86)\Bit9\Parity Agent"
.\dascli.exe stop

# Step 2: Backup current cache (just in case)
Copy-Item "Cache\Cache.db" "Cache\Cache.db.backup"

# Step 3: Delete corrupted cache
Remove-Item "Cache\Cache.db"

# Step 4: Start agent (will rebuild cache)
.\dascli.exe start

# Step 5: Monitor rebuild
.\dascli.exe status
# Cache Status will show "Rebuilding" then "Consistent"

# Note: This can take 30 minutes to several hours depending on file count
```

⚠️ **Warning**: Deleting Cache.db forces agent to re-inventory all files and re-download configuration from server. Only do this when cache is confirmed corrupt.

#### **Issue 4: Server Console Slow/Unresponsive**

**Symptoms:**
- Web console loads slowly
- Timeouts when searching files
- SQL queries taking minutes

**Troubleshooting:**

```sql
-- Check active connections
EXEC sp_who2;

-- Find expensive queries
SELECT TOP 10
    qs.execution_count,
    qs.total_worker_time/qs.execution_count AS avg_cpu_time,
    qs.total_elapsed_time/qs.execution_count AS avg_elapsed_time,
    SUBSTRING(qt.text, qs.statement_start_offset/2+1,
        (CASE WHEN qs.statement_end_offset = -1
            THEN LEN(CONVERT(nvarchar(max), qt.text)) * 2
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset)/2) AS query_text
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
WHERE qt.dbid = DB_ID('Bit9Server')
ORDER BY avg_elapsed_time DESC;

-- Check index fragmentation
SELECT 
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    ips.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(DB_ID('Bit9Server'), NULL, NULL, NULL, 'DETAILED') ips
JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 30
ORDER BY ips.avg_fragmentation_in_percent DESC;
```

**Common Fixes:**

1. **Rebuild Indexes**
   ```sql
   USE Bit9Server;
   EXEC sp_MSforeachtable @command1="ALTER INDEX ALL ON ? REBUILD";
   ```

2. **Update Statistics**
   ```sql
   EXEC sp_updatestats;
   ```

3. **Purge Old Events**
   ```sql
   -- Check event count
   SELECT COUNT(*) FROM events;
   
   -- Purge events older than 90 days
   DELETE FROM events WHERE timestamp < DATEADD(day, -90, GETDATE());
   
   -- Or use console: Admin → System Settings → Data Retention
   ```

4. **Check Server Resources**
   ```powershell
   # CPU usage
   Get-Counter '\Processor(_Total)\% Processor Time'
   
   # Memory usage
   Get-Counter '\Memory\Available MBytes'
   
   # Disk queue length (should be < 2)
   Get-Counter '\PhysicalDisk(_Total)\Avg. Disk Queue Length'
   ```

---

This continuation covers **Module 5 (Policy Enforcement), Module 6 (Rules Engine), Module 7 (Event Rules & Automation), and Module 8 (Troubleshooting Tools & Techniques)**. 

Would you like me to continue with **Module 9: Command Line Interface (DASCLI/B9CLI)**, **Module 10: Real-World Troubleshooting Scenarios**, and the remaining modules?
