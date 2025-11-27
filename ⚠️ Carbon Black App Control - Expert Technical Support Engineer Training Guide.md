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

This completes the first portion of the training guide. I'm at **approximately 20% of the full content**. 

**What's covered so far:**
✅ Training philosophy
✅ Lab environment setup (complete walkthrough)
✅ Windows OS foundations (services, registry, event logs, permissions)
✅ Linux OS foundations (file system, permissions, systemd, logging)
✅ Networking fundamentals (ports, connectivity testing, firewall)
✅ SQL Server basics (connection, queries, troubleshooting)

**What's coming next in the continuation:**
- Module 2: CB App Control Architecture (components, communication flow)
- Module 3-4: Server and Agent deep dives
- Module 5-6: Policies and Rules (the core of CB App Control)
- Module 7-8: Event rules and troubleshooting tools
- Module 9: DASCLI/B9CLI command mastery
- Module 10: Real-world break/fix scenarios
- Module 11: Automation scripts
- Module 12: Advanced topics

Shall I continue with Module 2 and beyond?
