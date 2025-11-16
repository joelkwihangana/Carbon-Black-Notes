# Carbon Black App Control: Zero to Expert Technical Support Guide

## Table of Contents

1. [Foundation: Understanding the Product Philosophy](#1-foundation-understanding-the-product-philosophy)
2. [Architecture Deep Dive](#2-architecture-deep-dive)
3. [Installation and Initial Configuration](#3-installation-and-initial-configuration)
4. [Policy Management from First Principles](#4-policy-management-from-first-principles)
5. [File State Management and Approval Methods](#5-file-state-management-and-approval-methods)
6. [Custom Rules: The Power Tools](#6-custom-rules-the-power-tools)
7. [Advanced Rules and Expert Rules](#7-advanced-rules-and-expert-rules)
8. [Event Analysis and Troubleshooting](#8-event-analysis-and-troubleshooting)
9. [Agent Diagnostics and DASCLI](#9-agent-diagnostics-and-dascli)
10. [Real-World Troubleshooting Scenarios](#10-real-world-troubleshooting-scenarios)
11. [Performance Optimization](#11-performance-optimization)
12. [Integration and API](#12-integration-and-api)
13. [Daily Technical Support Tasks](#13-daily-technical-support-tasks)

---

## 1. Foundation: Understanding the Product Philosophy

### 1.1 The "Default Deny" Mindset

**Why this matters for support:** Most customer confusion stems from not understanding this fundamental principle.

Carbon Black App Control operates on a **"guilty until proven innocent"** model:

```
Traditional Antivirus:          CB App Control:
─────────────────────          ───────────────
Everything allowed    →         Everything blocked
↓                               ↓
Block known bad       →         Allow known good
```

**Real support scenario:**
```
Customer: "Why can't my users install Chrome?"
You: "Because Chrome.exe is not approved in your environment. 
      App Control doesn't ask 'Is this malware?' 
      It asks 'Is this explicitly approved?' 
      If no, it blocks."
```

### 1.2 The Three Questions CB App Control Asks

For **every** file execution attempt:

1. **"Do I know this file?"** (Is it in my inventory?)
2. **"What is this file's state?"** (Approved, Banned, or Unapproved)
3. **"What does my policy say to do?"** (Based on enforcement level)

**Flow diagram you'll explain daily:**

```
File attempts to execute
         ↓
Is file Banned? → YES → BLOCK (always)
         ↓ NO
Is file Approved? → YES → ALLOW (always)
         ↓ NO
File is Unapproved → Check Enforcement Level
         ↓
    ┌────┴────┬────────┐
    ↓         ↓        ↓
  HIGH     MEDIUM     LOW
  Block    Prompt   Monitor
```

### 1.3 Key Terminology (Your Daily Language)

| Term | Definition | Support Context |
|------|-----------|-----------------|
| **Initialization** | Agent's first scan of existing files on endpoint | "Your computer is in initialization for 15 minutes after agent install - files found during this time are automatically locally approved" |
| **Interesting Files** | Executables, DLLs, scripts that CB tracks | "CB App Control doesn't track text files or images, only 'interesting files'" |
| **Local State** | File approval status on specific computer | "This file is locally approved on Server01 but globally unapproved" |
| **Global State** | File approval status across all computers | "Globally banning this file will block it everywhere" |
| **Enforcement Level** | Policy setting for handling unapproved files | "High blocks, Medium prompts, Low monitors" |

---

## 2. Architecture Deep Dive

### 2.1 The Three Components (Physical Reality)

#### Component 1: CB App Control Server

**What it actually is:**
- Windows Server (typically 2016/2019/2022)
- Runs Microsoft SQL Server (Express or Standard)
- Hosts IIS web server for console
- Physical location: On-premises data center or VM

**What it does:**
```
┌─────────────────────────────────┐
│   CB App Control Server         │
│                                 │
│  ┌──────────────────────────┐  │
│  │  SQL Database            │  │ ← Stores all file inventory
│  │  - File catalog          │  │   policies, rules, events
│  │  - Events                │  │
│  │  - Policies/Rules        │  │
│  └──────────────────────────┘  │
│                                 │
│  ┌──────────────────────────┐  │
│  │  IIS Web Console         │  │ ← Admin interface
│  └──────────────────────────┘  │
│                                 │
│  ┌──────────────────────────┐  │
│  │  Agent Communication     │  │ ← Listens on port 41002
│  │  Service (Port 41002)    │  │
│  └──────────────────────────┘  │
└─────────────────────────────────┘
```

**Support troubleshooting questions:**
- "Is the server service running?" (`services.msc` → Parity Server)
- "Can agents reach port 41002?" (Firewall check)
- "Is SQL Server responsive?" (Connection issues)

#### Component 2: CB App Control Agent

**What it actually is:**
- Lightweight software installed on endpoints
- Kernel-level driver (Parity.sys) that monitors file operations
- Service (DasHost.exe) that communicates with server

**Physical installation locations:**
```
Windows:
C:\Program Files (x86)\Bit9\Parity Agent\
  ├── DasHost.exe          ← Main agent service
  ├── Parity.sys           ← Kernel driver
  ├── DasCli.exe           ← Command-line tool (your best friend)
  ├── NotifierApp.exe      ← User notification window
  └── [configuration files]

Linux:
/opt/bit9/
  ├── b9daemon
  ├── b9cli
  └── [config]

macOS:
/Library/Application Support/Bit9/
```

**What the agent does every second:**

```
1. Monitors all file execution attempts
   ↓
2. Checks local cache: "Do I know this file's hash?"
   ↓
3. If unknown → Send hash to server
   ↓
4. Server responds: "Approved/Banned/Unapproved"
   ↓
5. Agent enforces based on policy
   ↓
6. Logs event and syncs with server
```

**Real support scenario:**
```
Customer: "Agent shows offline but server says connected"
You: "Check these in order:
      1. Port 41002 connectivity: telnet <server> 41002
      2. Agent service running: sc query dashost
      3. Certificate trust: dascli status
      4. Recent events: Check Events page for disconnect"
```

#### Component 3: CB App Control Console

**Physical reality:**
- Web interface hosted on IIS
- URL: `https://<server-name>` or `https://<server-IP>`
- Uses port 443 (HTTPS)

**Console architecture:**

```
Browser (Admin) → HTTPS (443) → IIS → Server Backend → SQL Database
                                  ↓
                         Authentication (Local/AD)
                                  ↓
                         Role-Based Access Control
```

### 2.2 Network Communication Flow (Critical for Troubleshooting)

**Agent → Server Communication:**

```
Agent Computer                    CB App Control Server
─────────────────                 ─────────────────────
                                  
1. Agent service starts
   ↓
2. Resolve server DNS name
   ↓
3. TCP connection to port 41002 ───→ Server accepts
   ↓                                  ↓
4. TLS handshake (certificate) ←───→ Server validates
   ↓                                  ↓
5. Authenticate with ComKey    ───→  Server verifies
   ↓                                  ↓
6. Send file hashes/events     ───→  Server processes
   ↓                                  ↓
7. Receive policy updates      ←───  Server responds
   ↓
8. Maintain keepalive every 30s
```

**Critical ports you'll troubleshoot:**

| Port | Protocol | Purpose | Who Needs Access |
|------|----------|---------|------------------|
| **41002** | TCP | Agent-to-Server communication | Agents → Server |
| **443** | TCP | Console access | Admins → Server |
| **1433** | TCP | SQL Server (internal) | Localhost only |
| **514** | UDP | Syslog export (optional) | Server → SIEM |

**Troubleshooting network issues:**

```powershell
# On agent computer - Test connectivity
Test-NetConnection -ComputerName appcontrol.company.com -Port 41002

# On agent - Check DNS resolution
nslookup appcontrol.company.com

# On agent - Verify certificate trust
dascli status
# Look for: "Server certificate validation: Passed"

# On server - Check if port is listening
netstat -ano | findstr :41002

# On server - Check firewall
Get-NetFirewallRule -DisplayName "*Carbon*" | Format-Table
```

---

## 3. Installation and Initial Configuration

### 3.1 Pre-Installation Checklist (What You'll Ask Customers)

Before troubleshooting installation issues, verify:

**Server Requirements:**
```
OS: Windows Server 2016/2019/2022
CPU: 4+ cores
RAM: 16 GB minimum (32 GB for 10,000+ endpoints)
Disk: 200 GB+ (database grows with file inventory)
SQL: SQL Server 2016+ (Express for <5K endpoints, Standard for more)
```

**Network Requirements:**
```
✓ Static IP or reliable DNS name
✓ Ports 41002 and 443 available
✓ SSL certificate (trusted by agents)
✓ Active Directory connectivity (if using AD integration)
```

### 3.2 Installation Process (Technical Reality)

**Server Installation Steps:**

```
1. Run CBAppControl-Server-8.11.x.exe
   ↓
2. Installer creates:
   - Program Files\Confer
   - SQL Database: "AppControl"
   - IIS Application Pool: "CbAppControl"
   - Windows Service: "Parity Server"
   ↓
3. Initial configuration wizard:
   - Create admin account
   - Set server URL (critical!)
   - Import license
   ↓
4. Server generates Communication Key (ComKey)
   - Used by agents to authenticate
   - Found in: Settings → Communication Key
```

**Agent Installation:**

```
Windows Silent Install:
msiexec /i CBAppControl-Agent-8.11.x.msi /quiet SERVER=appcontrol.company.com KEY=<ComKey>

Linux:
sudo ./install.sh -s appcontrol.company.com -k <ComKey>

macOS:
sudo installer -pkg CBAppControl-Agent.pkg -target /
sudo /opt/bit9/b9cli --server appcontrol.company.com --key <ComKey>
```

**First Agent Connection (What Actually Happens):**

```
Minute 0-1: Agent installed
  └─ Service starts
  └─ Connects to server on 41002
  └─ Downloads initial policy

Minute 1-15: Initialization phase
  └─ Agent scans all local fixed drives
  └─ Inventories all "interesting files"
  └─ Sends hashes to server (thousands of files)
  └─ Files found during init = Locally Approved
  
Minute 15+: Normal operation
  └─ Policy enforcement begins
  └─ Real-time monitoring active
```

**Support scenario:**
```
Customer: "Agent installed but not showing in console"
You: "Walk me through:
      1. Check agent service: sc query dashost
      2. Check connectivity: dascli connect
      3. Check for initialization: dascli status
         Look for 'Initializing: Yes' or 'Percent Complete'
      4. Check Events page: Filter for Computer = <hostname>
         Look for 'Agent initialized' event"
```

### 3.3 Initial Configuration (Post-Install)

**Critical First Steps:**

```
1. Verify Server URL
   Console → Settings → Server Settings
   ✓ Matches agent configuration
   ✓ Resolvable from agent networks
   
2. Configure Certificate
   Settings → Communication → SSL Certificate
   ✓ Must be trusted by agents
   ✓ Common Name matches server URL
   
3. Create Policies
   Rules → Policies → Add Policy
   ✓ Start with Low enforcement for testing
   ✓ Default policy applies to unassigned computers
   
4. Set Up Active Directory (if applicable)
   Settings → Active Directory
   ✓ Configure LDAP connection
   ✓ Enable computer synchronization
```

---

## 4. Policy Management from First Principles

### 4.1 Understanding Policies (The Core Concept)

**What a policy actually is:**

A policy is a **container of settings** that determines:
1. How strictly to enforce (enforcement level)
2. Which computers it applies to
3. Which rules are active
4. What to do when connected vs. disconnected

**Policy anatomy:**

```yaml
Policy Name: "Production Servers"
Mode: Control
Enforcement Level:
  Connected: High
  Disconnected: High
Rules Applied:
  - Global rules (all policies)
  - Policy-specific rules
Computers Assigned: 47
Features:
  - Track file changes: Yes
  - Device control: Enabled
  - Memory scan: Enabled
```

### 4.2 Enforcement Levels Explained (Deep Understanding)

#### High Enforcement (Block Unapproved)

**What it means:**
- Only explicitly approved files can execute
- Everything else is blocked, no exceptions
- No user choice

**When to use:**
- Production servers (stable, rarely change)
- Critical systems (payment processing, database servers)
- Compliance-required lockdown

**What happens:**

```
User double-clicks unapproved.exe
         ↓
Agent intercepts execution attempt
         ↓
Checks file state: Unapproved
         ↓
Policy enforcement: High
         ↓
BLOCK EXECUTION
         ↓
Show notifier: "This file is not approved"
         ↓
Log event: "Execution blocked"
         ↓
(Optional) Create approval request
```

**Real support scenario:**
```
Customer: "Users can't install anything on servers"
You: "That's by design with High enforcement. Options:
      1. Approve the specific application globally
      2. Use Trusted Users (let specific users install)
      3. Create approval request workflow
      4. Use Policy Override (temporary Low enforcement)
      5. Use Trusted Path rule for specific directory
      
      What's the business requirement?"
```

#### Medium Enforcement (Prompt Unapproved)

**What it means:**
- User gets a dialog: "Allow this file to run?"
- User can choose Yes (run once) or No (block)
- User decision doesn't change file state

**When to use:**
- Development workstations
- Power users who need flexibility
- Testing environments

**What happens:**

```
User double-clicks unapproved.exe
         ↓
Agent intercepts
         ↓
File state: Unapproved, Policy: Medium
         ↓
SHOW NOTIFIER PROMPT:
┌─────────────────────────────────┐
│ Unknown File Detected           │
│                                 │
│ File: unapproved.exe            │
│ Publisher: Unknown              │
│                                 │
│ [Allow Once] [Block] [Details]  │
└─────────────────────────────────┘
         ↓
User clicks "Allow Once"
         ↓
File executes (file state unchanged)
         ↓
Event logged: "Execution prompted, user allowed"
```

**Support consideration:**
```
Customer: "Users keep clicking 'Allow' on malware"
You: "Medium enforcement requires user training. Consider:
      1. Move to High enforcement
      2. Use reputation rules to auto-block low-trust files
      3. Implement approval request workflow
      4. Monitor 'Execution prompted' events for patterns"
```

#### Low Enforcement (Monitor Unapproved)

**What it means:**
- All files execute (approved, unapproved, but NOT banned)
- Full visibility into what's running
- No blocking, no prompting

**When to use:**
- Initial deployment (learning mode)
- Assessment phase
- End-user workstations with flexibility needs

**What happens:**

```
User double-clicks unapproved.exe
         ↓
Agent intercepts
         ↓
File state: Unapproved, Policy: Low
         ↓
ALLOW EXECUTION (silently)
         ↓
Event logged: "Execution monitored"
         ↓
File details sent to server (if first seen)
```

### 4.3 Connected vs. Disconnected Enforcement

**Critical concept for laptops:**

```
Office Network (Connected)         Home/Travel (Disconnected)
────────────────────────          ────────────────────────────
Agent can reach server            Agent cannot reach server
  ↓                                  ↓
Uses "Connected" enforcement      Uses "Disconnected" enforcement
  ↓                                  ↓
Can request real-time approvals   Must rely on local cache
  ↓                                  ↓
Example: Low (flexible)           Example: High (lockdown)
```

**Common configuration:**
```yaml
Policy: "Corporate Laptops"
Connected Enforcement: Low
Disconnected Enforcement: High

Rationale:
- In office: Flexible, can approve new software
- Outside office: Locked down, only approved software runs
```

**Troubleshooting scenario:**
```
Customer: "Laptop user can't run software at home"
You: "Check policy settings:
      1. Console → Assets → Computers → Select laptop
      2. View Current Policy
      3. Check 'Disconnected Enforcement'
      4. If High: User can only run previously approved software
      5. Options:
         - Approve software before travel
         - Lower disconnected enforcement
         - Use Policy Override code"
```

### 4.4 Policy Assignment Methods

**Method 1: Manual Assignment**

```
Console → Assets → Computers
Select computer(s) → Action → Move Computer to Policy: <PolicyName>
```

**Method 2: Active Directory (Automatic)**

```
Policy Configuration:
AD Rule: "OU=Servers,DC=company,DC=com"
         ↓
Agent on computer queries AD
         ↓
"I'm in OU=Servers"
         ↓
Server automatically assigns matching policy
```

**Method 3: Default Policy**

```
All computers not explicitly assigned
         ↓
Automatically assigned to policy marked "Default"
```

**Support troubleshooting:**
```
Customer: "Computer moved to wrong policy automatically"
You: "Check AD-based policy assignment:
      1. Rules → Policies → Select Policy → AD Rules
      2. Check LDAP query: Does it match this computer?
      3. Common issue: Overlapping AD rules in multiple policies
      4. Policy precedence: Explicit > AD-based > Default
      5. Events page: Filter 'Policy assigned' for this computer"
```

---

## 5. File State Management and Approval Methods

### 5.1 File States (The Foundation of Everything)

**Every file has TWO states:**

```
Global State (Server-wide)         Local State (Per Computer)
──────────────────────            ─────────────────────────
- Approved                        - Approved
- Unapproved                      - Unapproved  
- Banned                          - Banned
```

**Why two states matter:**

```
Scenario: Chrome.exe

Global State: Unapproved
Local State on Computer A: Approved (installed during initialization)
Local State on Computer B: Unapproved (new install attempt blocked)

Result:
- Computer A can run Chrome
- Computer B cannot run Chrome (High enforcement)
```

**File state hierarchy (most important troubleshooting concept):**

```
1. Banned (Global or Local) → ALWAYS BLOCKS
   ↓
2. Approved (Global or Local) → ALWAYS ALLOWS
   ↓
3. Unapproved → Depends on enforcement level
```

**Support scenario:**
```
Customer: "Why can one server run this .exe but another can't?"
You: "Check local state on both:
      1. Console → Tools → Find Files
      2. Search filename
      3. Click '+' next to file → All File Instances
      4. Compare 'Local State' column for both servers
      5. Likely: File locally approved on Server A (from initialization)
         but unapproved on Server B"
```

### 5.2 Approval Methods (Your Daily Toolkit)

#### Method 1: Global Approval by Hash

**What it does:**
Approves this exact file (by SHA-256 hash) on all computers

**When to use:**
- Corporate-standard applications
- Software deployed enterprise-wide
- Security tools

**How to do it:**

```
Method A: From Find Files page
1. Tools → Find Files
2. Search for filename
3. Select file(s)
4. Action → Change Global State → Approved

Method B: From Approval Request
1. Tools → Approval Requests
2. Click request
3. Action → Approve File Globally

Method C: From Events page
1. Reports → Events
2. Filter: Subtype = "Execution blocked"
3. Click '+' next to file
4. Action → Approve File Globally
```

**What actually happens:**

```
1. Admin approves firefox.exe
   ↓
2. Server creates hash-based rule:
   File Hash: 4a3b5c6d7e8f...
   State: Approved
   Scope: Global
   ↓
3. All agents receive policy update
   ↓
4. Local cache updated: firefox.exe = Approved
   ↓
5. File can now execute on all computers
```

#### Method 2: Local Approval

**What it does:**
Approves file on specific computer only

**When to use:**
- Computer-specific tools
- One-off installations
- Testing before global approval

**How to do it:**

```
Method A: From Computer Details
1. Assets → Computers → Select computer
2. Related Views → Files on This Computer
3. Select file
4. Action → Change Local State → Approved

Method B: From Approval Request
1. Tools → Approval Requests
2. Click request
3. Action → Approve File Locally

Method C: From Events (computer-filtered)
1. Reports → Events → Add Filter: Computer = <hostname>
2. Find blocked file event
3. Action → Approve Locally
```

**Real support scenario:**
```
Customer: "I approved the file but it's still blocked on other computers"
You: "You used Local Approval. That only works on one computer.
      Check: Tools → Find Files → Search filename → View Details
      Look at 'Local State' vs 'Global State'
      To approve everywhere: Change Global State to Approved"
```

#### Method 3: Publisher-Based Approval (Certificate Rules)

**What it does:**
Approves all files signed by a specific publisher

**Critical understanding:**

```
Certificate Chain:
Code Signing Cert → Intermediate CA → Root CA

CB App Control checks:
1. Is file signed?
2. Is signature valid?
3. Is signing certificate trusted?
4. Is publisher approved?
5. Has file been tampered with?
```

**How to approve by publisher:**

```
1. Tools → Find Files
2. Search and select file
3. Click '+' → View Details
4. Under Publisher info, click publisher name
5. Click "Approve Publisher"
   OR
6. Rules → Software Rules → File Rules
7. Add File Rule → Type: Approval
8. Rule Type: Publisher
9. Select certificate from file
```

**What actually happens:**

```
Example: Approve "Microsoft Corporation"

1. Create publisher rule:
   Publisher: CN=Microsoft Corporation, O=Microsoft Corporation, ...
   State: Approved
   ↓
2. Any file signed by this certificate = Approved
   ↓
3. Includes: All Microsoft updates, tools, applications
   ↓
4. Agent validates:
   - Signature present? ✓
   - Signature valid? ✓
   - Certificate trusted? ✓
   - Publisher approved? ✓
   → ALLOW
```

**Advanced: Certificate conditions**

```
You can require:
- Minimum version number
- Specific product name
- File description patterns
- Date constraints

Example:
Approve Microsoft files
+ Product name contains "Windows"
+ Version >= 10.0.0.0
```

**Support troubleshooting:**
```
Customer: "Publisher approval isn't working"
You: "Check these in order:
      1. Is file actually signed?
         Find Files → Select file → View Details → Certificate tab
      2. Is certificate valid? (not expired, not revoked)
      3. Is root CA trusted on endpoint?
         Agent must trust the certificate chain
      4. Does rule match exactly?
         Rule publisher must match file certificate
      5. Check Events: Filter 'Certificate validation failed'
         Common: Timestamp certificate issues"
```

#### Method 4: Trusted Directory

**What it does:**
Network share where all executables are automatically approved

**When to use:**
- Software deployment share
- Controlled release repository
- Admin-only accessible location

**How to configure:**

```
1. Create network share: \\fileserver\software
2. Set NTFS permissions: Only admins can write
3. Console: Rules → Software Rules → Custom
4. Add Custom Rule:
   Name: Software Repository
   Type: Trusted Directory
   Path: \\fileserver\software\*
   Policy: Select applicable policies
```

**What happens:**

```
Admin copies chrome_installer.exe to \\fileserver\software\
         ↓
CB App Control server crawls share (every 10 minutes)
         ↓
Discovers chrome_installer.exe
         ↓
File hash added to approved list (Global State: Approved)
         ↓
All users can now install Chrome from this share
         ↓
If user copies to desktop: Still approved (hash-based)
```

**Critical security note:**
```
⚠️ TRUSTED DIRECTORIES ARE DANGEROUS IF NOT SECURED

Bad configuration:
Path: C:\Users\*\Downloads\*
Problem: User can approve malware by downloading to this folder

Good configuration:
Path: \\secure-file-server\approved-software\*
NTFS: Only IT staff have Write access
Result: Only IT can place approved software
```

#### Method 5: Trusted Users and Groups

**What it does:**
Users/groups whose installations are automatically locally approved

**When to use:**
- Developers needing flexibility
- IT administrators
- Application packagers

**How to configure:**

```
1. Console → Rules → Policies → Select Policy
2. User/Group Rights tab
3. Add → Select AD user/group
4. Grant "Install Software" right
```

**What happens:**

```
John (Developer) is in "Trusted Users"
John's computer is in High Enforcement
         ↓
John runs installer.exe
         ↓
Agent checks: "Who is running this file?"
         ↓
User: John (Domain\john.doe)
         ↓
John is in Trusted Users list
         ↓
File is Locally Approved on John's computer
         ↓
Execution allowed (even in High Enforcement)
         ↓
Only approved on John's computer, not global
```

**Support scenario:**
```
Customer: "Developers need to install tools but we use High enforcement"
You: "Use Trusted Users:
      1. Create AD group: 'CB_Trusted_Developers'
      2. Add developers to group
      3. Policy → User Rights → Add group
      4. Grant 'Install Software'
      5. Developers can now install (locally approved)
      6. Software doesn't auto-approve for other users
      7. Review installations in Events: Filter Subtype = 'Trusted install'"
```

#### Method 6: Reputation-Based Approval

**What it does:**
Automatically approves files based on Carbon Black Cloud reputation

**Requires:**
- Active File Reputation Service subscription
- Server connected to CB Cloud

**How it works:**

```
Unknown file attempts to execute
         ↓
Agent sends hash to Server
         ↓
Server queries CB Cloud: "What's the reputation?"
         ↓
CB Cloud responds: Trust Factor (0-10)
         ↓
Server checks Reputation Rules
         ↓
Rule: "Auto-approve if Trust ≥ 8"
         ↓
Trust = 9 → File Approved Automatically
```

**Configuration:**

```
1. Settings → File Reputation → Enable
2. Rules → Software Rules → File Rules
3. Add File Rule:
   Type: Approval
   Trigger: Reputation Rule
   Condition: Trust Factor ≥ 8
   Action: Approve Globally
```

**Trust Factor scale:**

```
10 = Known good (Microsoft, Adobe, etc.)
8-9 = Trusted (common applications)
5-7 = Unknown/Neutral
2-4 = Suspicious
0-1 = Known malicious
```

### 5.3 Banning Files (The Inverse)

**Global vs. Local Ban:**

```
Global Ban:
- Blocks file on ALL computers
- Overrides any approval
- Use for: Known malware, prohibited software

Local Ban:
- Blocks file on specific computer
- Use for: Computer-specific restrictions
```

**How to ban:**

```
Same methods as approval:
1. Find Files → Select → Action → Change State → Banned
2. Software Rules → Add File Rule → Type: Ban
3. Events → Select blocked file → Ban Globally
```

**Ban takes precedence:**

```
Priority order:
1. BANNED (local or global) → Always blocks
2. Approved (local or global) → Allows if not banned
3. Unapproved → Depends on enforcement
```

---

## 6. Custom Rules: The Power Tools

### 6.1 Rule Types Overview

Custom rules control file operations beyond simple execution:

| Rule Type | Controls | Use Case |
|-----------|----------|----------|
| **Trusted Path** | Execution from specific locations | Allow running from authorized folders |
| **File Integrity Control** | Write/Delete/Rename of files | Protect system files from modification |
| **File Creation Control** | Creating/Writing new files | Control where files can be written |
| **Execution Control** | Running specific processes | Block/Allow execution regardless of approval |

### 6.2 Trusted Path Rules

**Purpose:** Allow execution from specific directories

**Real scenario from labs:**

```
Problem: High enforcement blocks new .bat scripts
Requirement: Developers need to test scripts in C:\Code\
Solution: Trusted Path rule for C:\Code\*
```

**Configuration:**

```yaml
Rule Name: Code Directory Execution
Type: Trusted Path
Path: C:\Code\*
Execute Action: Allow and Promote
Policy: Development Policy
```

**What "Allow and Promote" means:**

```
File in C:\Code\ attempts to execute
         ↓
Trusted Path rule matches
         ↓
Allow and Promote:
1. Allow execution (bypass enforcement)
2. "Promote" = Make file Locally Approved
         ↓
File is now locally approved
         ↓
Can execute even if copied elsewhere
```

**Process exclusions (advanced):**

```yaml
Rule: Trusted Path for C:\Tools\*
Process Exclusion: C:\Windows\System32\cmd.exe

Meaning:
- Files in C:\Tools\ can execute
- EXCEPT when launched by cmd.exe
- Use case: Prevent script-based workarounds
```

**Support scenario:**
```
Customer: "Users bypass our controls by copying files to C:\Temp"
You: "DO NOT create Trusted Path for user-writable directories!
      Trusted Path should only be for admin-controlled locations:
      ✓ Good: \\fileserver\approved-tools\*
      ✗ Bad: C:\Users\*\*
      ✗ Bad: C:\Temp\*
      
      User-writable Trusted Paths = Security bypass"
```

### 6.3 File Integrity Control Rules

**Purpose:** Protect files/folders from modification

**From Lab 2 scenario:**

```
Requirement: Protect Windows hosts file from tampering
File: C:\Windows\System32\drivers\etc\hosts
Problem: Malware tries to modify for DNS hijacking
```

**Basic protection rule:**

```yaml
Rule Name: Protect Hosts File
Type: File Integrity Control
Write Action: Block
Path: C:\Windows\System32\drivers\etc\*
Policy: All Policies

```

**What happens:**

```
Notepad.exe tries to save changes to hosts file
         ↓
Agent intercepts file write operation
         ↓
File Integrity Control rule matches
         ↓
Write Action: Block
         ↓
BLOCK WRITE OPERATION
         ↓
Show notifier: "Write blocked by policy"
         ↓
Event logged: "File write blocked"
```

**Process exclusions (allow specific programs):**

```yaml
Rule Name: Allow Notepad to Edit Hosts
Type: File Creation Control
Write Action: Allow
Path: C:\Windows\System32\drivers\etc\hosts
Process: C:\Windows\System32\notepad.exe
Policy: Admin Policy

Priority: Specific rules override general rules
Result: Notepad can edit, but other programs cannot
```

**Real-world examples:**

```yaml
# Protect Transaction Files
Rule: Block Writes to POS Transactions
Type: File Integrity Control
Path: C:\Program Files\POSsystem\Transactions\*
Write Action: Block
Process Exclusion: C:\Program Files\POSsystem\POSsystem.exe
# Only the POS app itself can modify transaction files

# Protect System32
Rule: System32 Write Protection
Type: File Integrity Control
Path: C:\Windows\System32\*
Write Action: Block
Process Exclusion: C:\Windows\System32\msiexec.exe
Process Exclusion: C:\Windows\System32\wusa.exe
# Only installers/updaters can modify System32

# Protect Configuration Files
Rule: Protect App Config
Type: File Integrity Control
Path: C:\ProgramData\MyApp\config.xml
Write Action: Block
Process Exclusion: C:\Program Files\MyApp\MyApp.exe
# Only the app can modify its own config
```

**Support troubleshooting:**

```
Customer: "File Integrity Control blocking legitimate updates"
You: "Two approaches:
      1. Add Process Exclusion for the updater:
         - Find the updater executable
         - Add to Process Exclusion list
         
      2. Create more specific rule with Allow action:
         - Higher priority than Block rule
         - Matches the updater process
         
      Debug steps:
      1. Events → Filter 'File write blocked'
      2. Identify the blocked process
      3. Determine if it's legitimate
      4. Add exclusion or create Allow rule"
```

### 6.4 File Creation Control Rules

**Purpose:** Control where and by whom files can be created

**Difference from File Integrity Control:**

```
File Integrity Control:
- Protects existing files from modification
- Blocks write/delete/rename of specific files

File Creation Control:
- Controls creation of NEW files
- Can allow or block writes to directories
- Can restrict by process
```

**Lab 3 scenario:**

```
Problem: Block writes to C:\Code\ by default
Exception: Allow Notepad to write (for development)
```

**Configuration:**

```yaml
# Rule 1: Block all writes to Code directory
Rule Name: Block Code Writes
Type: File Creation Control
Write Action: Block
Path: C:\Code\*
Policy: Development

# Rule 2: Allow Notepad specifically
Rule Name: Permit Notepad Writes to Code
Type: File Creation Control
Write Action: Allow
Path: C:\Code\*
Process: C:\Windows\System32\notepad.exe
Policy: Development
```

**How rules interact (priority):**

```
Scenario: Notepad tries to save file.txt in C:\Code\

Step 1: Check all matching rules
- Rule 1 matches: Block C:\Code\*
- Rule 2 matches: Allow C:\Code\* for notepad.exe

Step 2: Apply most specific rule
- Rule 2 is more specific (includes process restriction)
- Rule 2 takes priority

Result: ALLOW (Rule 2 wins)
```

**Advanced example - Developer workspace control:**

```yaml
# Developers can write code files
Rule: Developer Code Files
Type: File Creation Control
Write Action: Allow
Path: C:\Development\*
Process: *\devenv.exe      # Visual Studio
Process: *\Code.exe        # VS Code
Process: *\notepad++.exe   # Notepad++
Policy: Developer Policy

# Block everything else
Rule: Block Other Development Writes
Type: File Creation Control
Write Action: Block
Path: C:\Development\*
Policy: Developer Policy
```

**Wildcard patterns:**

```
Path patterns:
C:\Folder\*              → All files in Folder (not subdirectories)
C:\Folder\*\*            → All files in Folder and all subdirectories
C:\Folder\*.exe          → All .exe files in Folder
*\System32\*             → System32 on any drive

Process patterns:
C:\Windows\notepad.exe   → Exact path
*\notepad.exe            → Any location
C:\Program Files\*\app.exe → App.exe in any Program Files subfolder
```

### 6.5 Execution Control Rules

**Purpose:** Force allow or block execution regardless of file state

**Use cases:**

```
1. Block execution of specific file types
   Example: Block .vbs scripts even if approved
   
2. Block execution from specific locations
   Example: Block execution from user Temp folders
   
3. Force-allow critical admin tools
   Example: Always allow antivirus, even if unapproved
```

**Configuration:**

```yaml
# Block Scripts in User Temp
Rule Name: Block Temp Script Execution
Type: Execution Control
Execute Action: Block
Path: C:\Users\*\AppData\Local\Temp\*.vbs
Path: C:\Users\*\AppData\Local\Temp\*.ps1
Policy: All Policies

# Always Allow Admin Tools
Rule Name: Force Allow Admin Tools
Type: Execution Control
Execute Action: Allow
Path: C:\AdminTools\*
Policy: All Policies
```

**Execution Control vs. Trusted Path:**

```
Execution Control:
- Can Block or Allow
- Doesn't change file state
- Useful for blocking risky patterns

Trusted Path:
- Only Allows
- Can "Promote" (locally approve)
- Makes file approved for future use
```

### 6.6 Rule Priority and Conflict Resolution

**How CB App Control resolves conflicts:**

```
Priority Order (Highest to Lowest):

1. Most specific path
   C:\Windows\System32\cmd.exe
   beats
   C:\Windows\System32\*

2. With process restriction
   C:\Code\* (for notepad.exe only)
   beats
   C:\Code\* (for all processes)

3. Block over Allow
   If equal specificity, Block wins
   
4. Policy-specific over Global
   Policy-assigned rule beats global rule
```

**Example conflict resolution:**

```
Rule A: Block all writes to C:\Windows\*
Rule B: Allow writes to C:\Windows\Temp\* by installers

File: installer.exe writes to C:\Windows\Temp\file.tmp

Resolution:
1. Both rules match
2. Rule B is more specific (deeper path)
3. Rule B has process restriction (more specific)
4. Rule B wins → ALLOW
```

**Support troubleshooting:**

```
Customer: "My custom rule isn't working"
You: "Debug systematically:
      1. Verify rule is Enabled
      2. Check rule applies to correct policy
      3. Check path/process syntax (wildcards correct?)
      4. Look for conflicting rules (more specific rule?)
      5. Test with Events page:
         - Filter for the file
         - Click event → View Rule Evaluation
         - See which rule actually matched
      6. Check computer's policy is up-to-date:
         Assets → Computers → Policy Status"
```

---

## 7. Advanced Rules and Expert Rules

### 7.1 Advanced Rules (Multi-Operation Control)

**What makes a rule "Advanced":**

```
Standard rules: Single operation (Execute OR Write)
Advanced rules: Multiple operations (Execute AND Write)
```

**Lab 3 scenario:**

```
Requirement: Developers can:
1. Write code to C:\Code\
2. Execute compiled code from C:\Code\
But: Code is not globally approved

Solution: Advanced Rule with Execute + Write
```

**Configuration:**

```yaml
Rule Name: Developer Code Workspace
Type: Advanced
Operation: Execute and Write
Execute Action: Allow
Write Action: Allow
Path: C:\Code\*
Process: C:\Windows\System32\notepad.exe
Process: C:\Windows\Explorer.exe
Policy: Developer Policy
```

**What happens:**

```
Developer writes test.bat in C:\Code\ using Notepad
         ↓
Advanced rule matches:
- Operation: Write
- Process: notepad.exe
- Path: C:\Code\*
         ↓
Write ALLOWED
         ↓
Developer double-clicks test.bat from Explorer
         ↓
Advanced rule matches:
- Operation: Execute
- Process: explorer.exe (launching the .bat)
- Path: C:\Code\*
         ↓
Execute ALLOWED (even in High Enforcement)
```

**Why Process: Explorer.exe matters:**

```
When you double-click a file:
1. explorer.exe (Windows Explorer) handles the action
2. explorer.exe launches the file
3. CB App Control checks: "Who is starting this process?"
4. Answer: explorer.exe

Therefore:
- Process: explorer.exe allows user double-click
- Process: cmd.exe would allow command-line execution
- Process: powershell.exe would allow PowerShell execution
```

**Common process patterns:**

```yaml
# Allow user interactive execution
Process: C:\Windows\Explorer.exe

# Allow command-line execution
Process: C:\Windows\System32\cmd.exe

# Allow scripted execution
Process: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

# Allow scheduled task execution
Process: C:\Windows\System32\svchost.exe

# Allow service execution
Process: C:\Windows\System32\services.exe
```

### 7.2 Advanced Rule - Real-World Examples

**Example 1: Compiler/Development Tools**

```yaml
Rule: Visual Studio Compiler Workspace
Type: Advanced
Operation: Execute and Write
Execute Action: Allow
Write Action: Allow
Path: C:\Projects\Build\*
Process: *\devenv.exe
Process: *\MSBuild.exe
Process: *\csc.exe
Process: C:\Windows\Explorer.exe
Policy: Developer Workstations

Use case:
- Developers compile code in Build directory
- Compiled executables can run for testing
- Only development tools and user can interact
```

**Example 2: Automated Script Execution**

```yaml
Rule: PowerShell Script Repository
Type: Advanced
Operation: Execute and Write
Execute Action: Allow
Write Action: Allow
Path: C:\Scripts\Scheduled\*
Process: *\powershell.exe
Process: C:\Windows\System32\svchost.exe
Policy: Script Servers

Use case:
- Scripts stored in repository
- PowerShell can execute them
- Scheduled tasks (via svchost) can run them
- Scripts can modify log files in same directory
```

**Example 3: Application Update Mechanism**

```yaml
Rule: Application Auto-Update
Type: Advanced
Operation: Execute and Write
Execute Action: Allow
Write Action: Allow
Path: C:\Program Files\MyApp\Updates\*
Process: C:\Program Files\MyApp\MyApp.exe
Policy: MyApp Deployment

Use case:
- Application downloads updates to Updates folder
- Application can write downloaded files
- Application can execute installer from Updates folder
```

### 7.3 Expert Rules (Maximum Flexibility, Maximum Risk)

**WARNING:** Expert Rules bypass standard validation. Use with extreme caution.

**Differences from Advanced Rules:**

```
Advanced Rules:
✓ User-friendly interface
✓ Validation and error checking
✓ Limited to common scenarios
✓ Recommended for most use cases

Expert Rules:
✓ XML-based configuration
✓ No validation (anything goes)
✓ Access to all kernel filter operations
✓ Can create complex logic
✗ Easy to misconfigure
✗ Can impact performance
✗ Typically requires CB Support guidance
```

**When you might use Expert Rules:**

```
1. Monitoring without enforcement
   Example: Log file operations without blocking

2. Complex conditional logic
   Example: Allow if (condition A AND condition B) OR condition C

3. Performance tuning
   Example: Exclude specific file types from scanning

4. Workarounds for specific issues
   Example: Temporary fix pending product update
```

**Example Expert Rule (monitoring only):**

```xml
<CustomRule>
  <Name>Monitor Sensitive File Access</Name>
  <Description>Log all reads to sensitive files</Description>
  <RuleType>Expert</RuleType>
  <Operations>
    <Operation>Read</Operation>
  </Operations>
  <Paths>
    <Path>C:\Confidential\*</Path>
  </Paths>
  <Actions>
    <ReadAction>Monitor</ReadAction>
  </Actions>
  <Policies>
    <Policy>All Policies</Policy>
  </Policies>
</CustomRule>
```

**Support guidance for Expert Rules:**

```
Customer: "Can you help me create an Expert Rule?"
You: "Expert Rules should be last resort:
      1. Can this be done with Advanced Rule? (Usually yes)
      2. What's the specific requirement?
      3. Let me check KB articles for similar scenarios
      4. For complex Expert Rules, engage CB Support
      5. Always test in non-production first
      6. Document thoroughly (no interface hints)"
```

---

## 8. Event Analysis and Troubleshooting

### 8.1 Understanding the Events Page

**The Events page is your primary troubleshooting tool.**

**Event structure:**

```
Event Components:
1. Timestamp - When it happened
2. Computer - Which endpoint
3. User - Who was involved (if applicable)
4. Type - Broad category
5. Subtype - Specific action
6. Description - Details
7. File Info - Associated file (if applicable)
```

**Critical Event Subtypes (your daily focus):**

```
Execution Events:
- "Execution blocked" → File blocked by policy
- "Execution monitored" → File ran in Low enforcement
- "Execution prompted, user allowed" → Medium enforcement, user clicked Allow
- "Execution prompted, user blocked" → Medium enforcement, user clicked Block

File State Events:
- "File approval" → File approved
- "File ban" → File banned
- "File local approval" → File locally approved
- "File local ban" → File locally banned

Policy Events:
- "Policy assigned" → Computer moved to policy
- "Policy updated" → Policy configuration changed

System Events:
- "Agent initialized" → Agent completed first scan
- "Agent connected" → Agent connected to server
- "Agent disconnected" → Agent lost connection

File Integrity Events:
- "File write blocked" → Custom rule blocked write
- "File delete blocked" → Custom rule blocked delete
```

### 8.2 Using Filters Effectively

**Basic filtering workflow:**

```
Problem: User can't run application
         ↓
1. Filter by Computer
   Computer is <hostname>
         ↓
2. Filter by Time
   Max Age: 1 hour
         ↓
3. Filter by Subtype
   Subtype: Execution blocked
         ↓
4. Identify the blocked file
         ↓
5. Click '+' to see file details
```

**Common filter combinations:**

```
Troubleshooting Blocked Files:
- Subtype: Execution blocked
- Computer: <specific computer>
- Max Age: 1 hour
- Group By: File Name

Finding Approval Patterns:
- Subtype: File local approval
- User: <specific user>
- Max Age: 1 day
- Group By: User

Monitoring New Software:
- Subtype: Execution monitored
- Policy: Low Enforcement Policy
- Max Age: 1 week
- Group By: Publisher

Security Investigation:
- Threat Level: >= 50 (Medium-High risk)
- Trust Level: <= 50 (Low trust)
- Max Age: 1 day
```

**Advanced filtering (multiple conditions):**

```
Example: Find suspicious blocked files

Add Filter: Subtype is Execution blocked
Add Filter: Threat Level greater than 50
Add Filter: Trust Level less than 30
Add Filter: Publisher is Unknown

Click Apply → High-risk blocked files
```

### 8.3 Saved Views (Pre-Configured Queries)

**Built-in Saved Views:**

```
Console Access:
- Shows all admin actions in console
- Use for: Audit trail, "who changed what"

Blocked Files (All):
- All execution blocks across environment
- Use for: Understanding what's being blocked

New Installations:
- Files executed for first time
- Use for: Software change tracking

Threat Indicators:
- High-risk file activity
- Use for: Security monitoring

Device Control:
- USB/device insertion events
- Use for: Data loss prevention
```

**Creating Custom Saved Views:**

```
Example: Daily Security Review

1. Reports → Events
2. Add Filters:
   - Subtype: Execution blocked
   - Threat Level: >= 70
   - Max Age: 1 day
3. Click "Save As View"
4. Name: "Daily High-Risk Blocks"
5. Share with: Security Team

Result: One-click access to daily security events
```

### 8.4 Event Analysis Workflow (Troubleshooting Method)

**Standard troubleshooting process:**

```
Step 1: Identify the Event
Customer: "Application X won't run on Computer Y"
         ↓
Events → Filter:
- Computer: Y
- Subtype: Execution blocked
- File Name: contains "Application X"
         ↓
Find the specific event

Step 2: Gather Context
Click '+' next to event → View Details
         ↓
Record:
- Exact file name and path
- File hash (SHA-256)
- User attempting to run
- Timestamp
- Policy assignment

Step 3: Check File State
Click file name link → File Details page
         ↓
Check:
- Global State: ? (Approved/Unapproved/Banned)
- Local State on Computer Y: ?
- Publisher: ?
- Trust Level: ?
- Threat Level: ?

Step 4: Check Policy
Assets → Computers → Computer Y → View Details
         ↓
Check:
- Current Policy: ?
- Enforcement Level: ?
- Last Policy Update: ?
- Policy Status: Up to date?

Step 5: Identify Root Cause
Combine information:

If Global State = Banned:
   → File is explicitly banned (security decision)
   → Check Rules → Software Rules → File Rules for ban rule

If Global State = Unapproved AND Enforcement = High:
   → File needs approval
   → Decide: Approve globally, locally, or deny

If Local State = Banned (but Global = Approved):
   → Computer-specific ban
   → Check File Details → All Instances → Local State

If Policy Status = Out of date:
   → Agent hasn't received latest policy
   → Check agent connectivity

Step 6: Resolve
Choose appropriate solution:
- Approve file (if legitimate)
- Explain why banned (if security risk)
- Fix policy assignment (if wrong policy)
- Fix agent connectivity (if offline)
```

### 8.5 Real Support Scenarios

**Scenario 1: "Chrome won't install"**

```
Customer Report:
"Users can't install Google Chrome on their laptops"

Your Analysis:
1. Events → Computer: <laptop name>
2. Filter: File Name contains "chrome"
3. Find: "Execution blocked - ChromeSetup.exe"
4. Click '+' → File Details:
   - Global State: Unapproved
   - Publisher: Google LLC (valid signature)
   - Trust Level: 95 (very trustworthy)
   - Threat Level: 0 (clean)
5. Check Policy:
   - Policy: Corporate Laptops
   - Enforcement: High

Root Cause:
Chrome is legitimate but unapproved in High Enforcement

Resolution Options:
A. Approve Chrome globally (if corporate standard)
   → All users can install

B. Approve by publisher (Google LLC)
   → All Google-signed software allowed

C. Add Chrome to software deployment
   → Push via SCCM/Intune

D. Use Trusted Users
   → Specific users can install

Recommendation: Option A or B
```

**Scenario 2: "File keeps getting blocked even after approval"**

```
Customer Report:
"I approved application.exe but it's still blocked"

Your Analysis:
1. Tools → Find Files → Search "application.exe"
2. View Details:
   - Global State: Approved ✓
   - Multiple instances shown
3. Click "All File Instances"
4. Check hashes:
   - Instance on Server A: Hash abc123... (Approved)
   - Instance on Server B: Hash xyz789... (Unapproved)

Root Cause:
Different versions of the file
Customer approved version 1, users have version 2

Resolution:
"You have multiple versions:
1. Version on Server A is approved (hash: abc123)
2. Version on Server B is different (hash: xyz789)
3. Each unique hash needs separate approval
4. Options:
   - Approve all versions individually
   - Approve by publisher (covers all versions)
   - Standardize to single version"
```

**Scenario 3: "Everything was working, now everything's blocked"**

```
Customer Report:
"Suddenly all applications are blocked on Server X"

Your Analysis:
1. Events → Computer: Server X
2. Filter: Max Age: 2 hours
3. Group By: Subtype
4. Find: "Policy assigned" event at 2:00 PM
   - Old Policy: Standard Servers (Low)
   - New Policy: DMZ Servers (High)

Root Cause:
Computer accidentally moved to wrong policy

Resolution:
1. Assets → Computers → Server X
2. Action → Move Computer to Policy: Standard Servers
3. Wait for policy update (check Policy Status)
4. Inform customer:
   "Server was moved to DMZ policy (High Enforcement)
    I've moved it back to Standard Servers
    Check Events page for who made the change:
    Events → Subtype: Policy assigned → User column"
```

**Scenario 4: "Custom rule not working"**

```
Customer Report:
"I created a Trusted Path rule for C:\Apps but files still blocked"

Your Analysis:
1. Rules → Software Rules → Custom
2. Find the rule:
   - Name: Apps Trusted Path
   - Type: Trusted Path
   - Path: C:\Apps
   - Execute Action: Allow
   - Policy: Production Servers
   - Status: Enabled ✓
3. Check computer:
   - Computer: Server Y
   - Policy: Test Environment (not Production Servers)

Root Cause:
Rule applies to wrong policy

Resolution:
"Your rule applies to 'Production Servers' policy
But Server Y is in 'Test Environment' policy
Options:
1. Move Server Y to Production Servers policy
2. Edit rule to include Test Environment policy
3. Create separate rule for Test Environment

Also note: Path should be C:\Apps\* (with wildcard)
Current path matches only C:\Apps directory itself,
not files inside it"
```

---

## 9. Agent Diagnostics and DASCLI

### 9.1 DASCLI - Your Command-Line Diagnostic Tool

**DASCLI (Digital Adversary Script Command Line Interface)**

**Location:**
```
Windows: C:\Program Files (x86)\Bit9\Parity Agent\dascli.exe
Linux: /opt/bit9/b9cli
macOS: /Library/Application Support/Bit9/b9cli
```

**Essential DASCLI Commands:**

#### Command: `dascli status`

**Most important command for troubleshooting**

```powershell
C:\> cd "C:\Program Files (x86)\Bit9\Parity Agent"
C:\> dascli status

Output:
Version:                     8.11.2.100
Server:                      appcontrol.company.com:41002
Last Server Communication:   2024-11-16 14:32:15
Connection Status:           Connected
Policy:                      Production Servers
Policy Status:               Up to date
Enforcement Level:           High (Connected), High (Disconnected)
Approved Files:              15,234
Banned Files:                42
Unapproved Files:            3
Local Files:                 15,279
Tamper Protection:           Enabled
Initialization:              Complete
Certificate Validation:      Passed
```

**What to look for:**

```
Connection Status: Connected
✓ Good: Agent communicating with server
✗ Bad: Disconnected → Network/firewall issue

Policy Status: Up to date
✓ Good: Agent has latest policy
✗ Bad: Out of date → Sync issue

Certificate Validation: Passed
✓ Good: Trust chain valid
✗ Bad: Failed → Certificate issue

Initialization: Complete
✓ Good: Initial scan done
? In Progress: Still scanning (normal for new agent)
```

**Support usage:**

```
Customer: "Agent shows offline in console"
You: "Run this command on the endpoint:
     dascli status
     
     Send me:
     1. Connection Status line
     2. Last Server Communication line
     3. Certificate Validation line
     
     This tells us if it's network or certificate issue"
```

#### Command: `dascli connect`

**Tests connection to server**

```powershell
C:\> dascli connect

Output (Success):
Connecting to appcontrol.company.com:41002...
DNS resolution: Success (10.0.1.50)
TCP connection: Success
TLS handshake: Success
Certificate validation: Success
Authentication: Success
Connected to server version 8.11.2

Output (Failure - Network):
Connecting to appcontrol.company.com:41002...
DNS resolution: Success (10.0.1.50)
TCP connection: Failed (Connection timed out)
Cannot connect to server

Output (Failure - Certificate):
Connecting to appcontrol.company.com:41002...
DNS resolution: Success (10.0.1.50)
TCP connection: Success
TLS handshake: Failed
Certificate validation: Failed (Untrusted root CA)
Cannot establish secure connection
```

**Troubleshooting with this command:**

```
1. DNS resolution failed:
   → Check DNS configuration
   → Try connecting by IP: dascli connect --server 10.0.1.50:41002

2. TCP connection failed:
   → Check firewall (port 41002)
   → Check network routing
   → Verify server service running

3. Certificate validation failed:
   → Check certificate trust on endpoint
   → Verify cert not expired
   → Check certificate chain

4. Authentication failed:
   → Check Communication Key matches
   → Verify agent not disabled in console
```

#### Command: `dascli certinfo <filepath>`

**Displays certificate information for signed files**

```powershell
C:\> dascli certinfo "C:\Program Files\Mozilla Firefox\firefox.exe"

Output:
File: C:\Program Files\Mozilla Firefox\firefox.exe
Signed: Yes
Signature Valid: Yes

Signing Certificate:
  Subject: CN=Mozilla Corporation, O=Mozilla Corporation, L=San Francisco, S=California, C=US
  Issuer: CN=DigiCert SHA2 Assured ID Code Signing CA, OU=www.digicert.com, O=DigiCert Inc, C=US
  Serial Number: 0C:4D:2E:F7:B9:2C:7F:D9:B7:1D:7E:4F:4E:5D:9F:8C
  Valid From: 2023-01-15 00:00:00
  Valid To: 2026-01-19 23:59:59
  Thumbprint: A1:B2:C3:D4:E5:F6:07:18:29:3A:4B:5C:6D:7E:8F:90:A1:B2:C3:D4

Certificate Chain:
  Root CA: DigiCert Assured ID Root CA
  Intermediate: DigiCert SHA2 Assured ID Code Signing CA
  Leaf: Mozilla Corporation

Trust Status: Trusted
```

**When to use:**

```
1. File blocked, need to verify signature:
   → Check if file actually signed
   → Verify certificate valid
   → Check certificate chain

2. Publisher approval not working:
   → Compare certificate details with approval rule
   → Verify exact certificate match

3. Malware investigation:
   → Check if malware is signed (some is!)
   → Identify stolen/compromised certificates
```

**Support scenario:**

```
Customer: "Publisher approval rule not working for File.exe"
You: "Run: dascli certinfo C:\Path\To\File.exe
     
     Compare output with your publisher rule:
     - Rules → Software Rules → View rule
     - Certificate tab → Compare fields
     
     Common issues:
     - File not signed (can't use publisher rule)
     - Certificate expired
     - Certificate doesn't match rule exactly
     - Intermediate CA not trusted on endpoint"
```

#### Command: `dascli showpathpolicies`

**Shows all custom rules applied to agent**

```powershell
C:\> dascli showpathpolicies

Output:
Custom Rules Applied: 12

Rule 1:
  Name: Block Temp Script Execution
  Type: Execution Control
  Operation: Execute
  Action: Block
  Path: C:\Users\*\AppData\Local\Temp\*.vbs
  Process: *

Rule 2:
  Name: Trusted Path - Code Directory
  Type: Trusted Path
  Operation: Execute
  Action: Allow and Promote
  Path: C:\Code\*
  Process: C:\Windows\Explorer.exe

Rule 3:
  Name: Protect Hosts File
  Type: File Integrity Control
  Operation: Write
  Action: Block
  Path: C:\Windows\System32\drivers\etc\hosts
  Process: *

[... etc for all 12 rules ...]
```

**Critical for troubleshooting:**

```
Use when:
1. Customer: "Why is this file blocked?"
   → Check if custom rule is blocking it
   → Verify rule logic

2. Customer: "My rule isn't working"
   → Verify rule is actually deployed to agent
   → Check rule syntax

3. Performance issues:
   → Count number of rules
   → Too many rules (>50) can impact performance
```

**Support usage:**

```
Customer: "Custom rule for C:\Code not working"
You: "On the endpoint, run:
     dascli showpathpolicies > rules.txt
     
     Open rules.txt and search for 'Code'
     
     If rule is listed:
     - Rule deployed successfully
     - Check Path and Process fields exactly
     - May be lower priority than conflicting rule
     
     If rule NOT listed:
     - Check console: Assets → Computers → Policy Status
     - If 'Out of date': Agent hasn't received update
     - Force sync: Computer Details → Other Actions → Synchronize"
```

#### Command: `dascli analyze <filepath>`

**Analyzes why file is allowed or blocked**

```powershell
C:\> dascli analyze "C:\Code\test.exe"

Output:
File: C:\Code\test.exe
Hash: 7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b

File State:
  Global State: Unapproved
  Local State: Unapproved

Policy: Development Policy
  Enforcement Level: High (Connected)
  
Rule Evaluation:
  Rule Match: Trusted Path - Code Directory
    Type: Trusted Path
    Path Match: Yes (C:\Code\*)
    Process Match: Pending (depends on launching process)
    Action: Allow and Promote
    
Result: File will be ALLOWED if launched by:
  - C:\Windows\Explorer.exe
  - C:\Windows\System32\cmd.exe
  
Result: File will be BLOCKED if launched by other processes

Recommendation: File is unapproved but Trusted Path rule will allow execution from authorized processes
```

**When to use this command:**

```
Perfect for:
1. "Why is this file blocked?"
   → See exact rule evaluation
   
2. "Why is this file allowed?"
   → See which rule permits it
   
3. "I approved the file but it's still blocked"
   → Check if local ban overriding approval
   
4. Testing rules before deployment
   → See what would happen
```

#### Command: `dascli password <CLI_password>`

**Unlocks advanced DASCLI commands**

```powershell
C:\> dascli password RAWF-TUHT-SUWG-TUHM

Output:
CLI password accepted. Advanced commands enabled.
```

**Where to find CLI password:**

```
Console:
Assets → Computers → Select computer → View Details
→ Carbon Black App Control Agent section
→ "Show CLI Password"

Note: Password unique per agent (security feature)
```

**Advanced commands unlocked:**

```powershell

# Start/stop agent service
dascli stop
dascli start

# Force immediate policy update
dascli refresh

# Clear local cache (dangerous!)
dascli clearcache

# Disable agent temporarily (very dangerous!)
dascli disable

# Re-enable agent
dascli enable
```

**Support scenario:**

```
Customer: "Need to restart agent service"
You: "Get CLI password from console:
     1. Assets → Computers → <computer>
     2. Carbon Black App Control Agent section
     3. Show CLI Password
     
     On endpoint:
     dascli password <password>
     dascli stop
     [wait 5 seconds]
     dascli start
     
     Verify:
     dascli status
     → Check Connection Status"
```

#### Command: `dascli capture <output_path>`

**Captures diagnostic data for support**

```powershell
C:\> dascli capture C:\temp\diagnostics.zip

Output:
Collecting diagnostic information...
- Agent configuration files
- Recent logs (last 7 days)
- Current cache state
- Policy configuration
- Network diagnostic data
- Performance metrics

Created: C:\temp\diagnostics.zip (15.3 MB)

This file can be uploaded to Carbon Black Support for analysis.
```

**When to create capture:**

```
1. Before engaging CB Support:
   → Support will ask for this file
   → Contains everything they need
   
2. Intermittent issues:
   → Captures state at problem time
   → Includes recent activity
   
3. Performance problems:
   → Includes metrics and timings
   
4. Complex troubleshooting:
   → More data than manual review
```

**Support workflow:**

```
You: "This issue requires detailed analysis.
     Please run:
     dascli capture C:\diagnostics.zip
     
     Then upload to:
     - Support ticket, or
     - Console → Support → Upload Diagnostic File
     
     This includes:
     - Last 7 days of logs
     - Current configuration
     - No personally identifiable information"
```

### 9.2 Windows Native Commands for CB Troubleshooting

#### Command: `fltmc` (Filter Manager Control)

**Shows kernel filter drivers**

```powershell
C:\> fltmc

Output:
Filter Name                     Num Instances    Altitude    Frame
------------------------------  -------------  ------------  -----
Parity                                    3     328100         0
luafv                                     1     135000         0
FileInfo                                  3     45000          0
bindflt                                   1     409800         0
storqosflt                                0     244000         0
wcifs                                     0     189900         0
npsvctrig                                 1     46000          0
```

**What you're looking for:**

```
Parity driver:
- Name: Parity (CB App Control's kernel driver)
- Instances: Should match number of volumes
  Example: C:\ and D:\ = 2 volumes + 1 = 3 instances
- Altitude: 328100 (driver loading order)
- Frame: 0 (active)

If Parity NOT listed:
→ Driver not loaded (severe problem)
→ Agent service may not be running
→ Installation issue
```

**Support troubleshooting:**

```
Customer: "Agent not blocking files even in High enforcement"
You: "Check if kernel driver loaded:
     fltmc | findstr Parity
     
     If no output:
     1. Check agent service: sc query dashost
     2. If running: Driver load failure
     3. Check Event Viewer:
        System log → Source: fltmgr
        Look for Parity driver errors
     4. May require agent reinstall"
```

#### Command: `netstat` (Network Statistics)

**Verify agent-to-server connection**

```powershell
C:\> netstat -ano | findstr 41002

Output:
TCP    10.0.5.100:54123    10.0.1.50:41002    ESTABLISHED    1234
TCP    10.0.5.100:54124    10.0.1.50:41002    ESTABLISHED    1234

PID 1234 = DasHost.exe (agent process)
```

**Connection states:**

```
ESTABLISHED:
✓ Good: Active connection to server

TIME_WAIT:
? Transitional: Connection recently closed
? Normal if brief

SYN_SENT:
✗ Bad: Trying to connect but no response
→ Firewall blocking or server not responding

No output:
✗ Bad: No connection attempt
→ Agent not trying to connect
→ Check agent configuration
```

**Support workflow:**

```
Customer: "Agent shows disconnected"
You: "Run: netstat -ano | findstr 41002
     
     No output:
     → Agent not attempting connection
     → Check: dascli status (Server field)
     → Verify server address correct
     
     SYN_SENT state:
     → Connection blocked
     → Check firewall: Test-NetConnection -Port 41002
     
     TIME_WAIT (persistent):
     → Connection unstable
     → Check network quality/VPN"
```

#### Command: `sc` (Service Control)

**Manage agent service**

```powershell
# Query service status
C:\> sc query dashost

Output:
SERVICE_NAME: dashost
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

# Query service configuration
C:\> sc qc dashost

Output:
SERVICE_NAME: dashost
        TYPE               : 10  WIN32_OWN_PROCESS
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : "C:\Program Files (x86)\Bit9\Parity Agent\DasHost.exe"
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Carbon Black App Control Agent
        DEPENDENCIES       :
        SERVICE_START_NAME : LocalSystem
```

**Key fields:**

```
STATE: 4 RUNNING
✓ Good: Service active
✗ Bad: STOPPED → Agent not working

START_TYPE: 2 AUTO_START
✓ Good: Starts at boot
✗ Bad: DISABLED → Manually disabled

BINARY_PATH_NAME:
→ Verify correct installation path
→ Check file exists
```

**Service commands:**

```powershell
# Stop service
sc stop dashost

# Start service
sc start dashost

# Restart service (stop then start)
sc stop dashost
timeout /t 5
sc start dashost

# Set to automatic startup
sc config dashost start= auto

# Query startup type
sc qc dashost | findstr START_TYPE
```

**Support scenario:**

```
Customer: "Agent stopped working after reboot"
You: "Check service:
     sc query dashost
     
     If STOPPED:
     1. Try starting: sc start dashost
     2. If fails: Check Event Viewer
        Application log → Source: DasHost
        Look for startup errors
     3. If starts but stops again:
        → Check for conflicting security software
        → Check for driver conflicts (fltmc)
     
     If service missing entirely:
     → Agent uninstalled or corrupted
     → Requires reinstall"
```

### 9.3 Agent Log Files

**Log locations:**

```
Windows:
C:\ProgramData\Bit9\Parity Agent\Logs\
  ├── DasHost.log         ← Main agent log
  ├── DasHost.old.log     ← Previous log (rotated)
  ├── Notifier.log        ← User notification log
  └── Diagnostic_*.log    ← Created by dascli capture

Linux:
/var/log/bit9/
  ├── b9daemon.log
  └── b9daemon.old.log

macOS:
/Library/Application Support/Bit9/logs/
  ├── b9daemon.log
  └── b9daemon.old.log
```

**What to look for in logs:**

```
Connection Issues:
[ERROR] Failed to connect to server: Connection timeout
[ERROR] Certificate validation failed: Untrusted root CA
[WARN] Server unreachable, retrying in 60 seconds

Policy Issues:
[INFO] Policy update received: Production Servers (v142)
[INFO] Policy applied successfully
[ERROR] Policy update failed: Invalid signature

File Blocking:
[INFO] Execution blocked: C:\malware.exe (Hash: abc123..., Reason: Unapproved file in High enforcement)
[INFO] Write blocked: C:\Windows\system32\drivers\etc\hosts (Reason: File Integrity Control rule)

Performance Issues:
[WARN] Cache size exceeded recommended limit
[WARN] High CPU usage during scan (45%)
[ERROR] Database lock timeout
```

**Reading logs with PowerShell:**

```powershell
# View last 50 lines
Get-Content "C:\ProgramData\Bit9\Parity Agent\Logs\DasHost.log" -Tail 50

# Search for errors in last hour
$TimeAgo = (Get-Date).AddHours(-1)
Get-Content "C:\ProgramData\Bit9\Parity Agent\Logs\DasHost.log" | 
    Where-Object { $_ -match "\[ERROR\]" } |
    Select-String -Pattern $TimeAgo.ToString("yyyy-MM-dd HH:")

# Search for specific file
Get-Content "C:\ProgramData\Bit9\Parity Agent\Logs\DasHost.log" |
    Select-String -Pattern "chrome.exe"

# Export recent errors to file
Get-Content "C:\ProgramData\Bit9\Parity Agent\Logs\DasHost.log" |
    Where-Object { $_ -match "\[ERROR\]|\[WARN\]" } |
    Out-File C:\temp\agent-errors.txt
```

### 9.4 Agent Health Check Events

**Triggering a health check:**

```
Console:
1. Assets → Computers → Select computer
2. Other Actions → Run health check
3. Wait 1-2 minutes
4. Related Views → Health Check Events
```

**Health check validates:**

```
1. Service Status
   ✓ DasHost service running
   ✓ Service responding to commands
   
2. Driver Status
   ✓ Kernel driver loaded
   ✓ Driver filtering file operations
   
3. Server Communication
   ✓ Can resolve server DNS
   ✓ Can connect to port 41002
   ✓ Certificate trust valid
   ✓ Authentication successful
   
4. Policy Status
   ✓ Policy downloaded
   ✓ Policy version current
   ✓ Policy applied correctly
   
5. Cache Status
   ✓ Local cache valid
   ✓ Cache size within limits
   ✓ Cache not corrupted
   
6. File Inventory
   ✓ Initialization complete
   ✓ No pending file analysis
   ✓ File catalog synchronized
```

**Health check results:**

```
Events → Filter: Subtype = "Health check"

Green (Passed):
Description: "Health check passed: All components operational"

Yellow (Warning):
Description: "Health check warning: Cache size approaching limit"
Action: Monitor, not critical yet

Red (Failed):
Description: "Health check failed: Cannot connect to server"
Action: Immediate attention required
```

**Support usage:**

```
Customer: "Agent seems slow/unresponsive"
You: "I'll run a health check:
     1. Initiated from console
     2. Results show: [analyze results]
     
     Example findings:
     
     'Cache corrupted':
     → Need to rebuild cache
     → dascli password <pwd>
     → dascli clearcache
     → Agent will rescan (may take time)
     
     'Cannot connect to server':
     → Network/firewall issue
     → Run: dascli connect
     → Troubleshoot connectivity
     
     'Policy out of date':
     → Sync issue
     → Force update: dascli refresh
     → Check Events for errors"
```

---

## 10. Real-World Troubleshooting Scenarios

### 10.1 Scenario: "Everything was working, now everything is blocked"

**Customer complaint:**
"Suddenly all applications stopped working on our web server. Users getting errors."

**Initial information gathering:**

```
You: "Help me understand the situation:
     1. When did this start? (exact time if possible)
     2. Which server/computer?
     3. Were any changes made recently?
     4. Is this affecting all apps or specific ones?"

Customer: "Started at 2:00 PM today, server name WEBSRV01, 
          all applications blocked, no recent changes."
```

**Troubleshooting steps:**

```
Step 1: Check Events for policy changes
───────────────────────────────────────
Console → Reports → Events
Add Filter:
  - Computer: WEBSRV01
  - Max Age: 4 hours
  - Subtype: Policy assigned

Finding: At 2:00 PM
  Event: "Policy assigned"
  Old Policy: Web Servers (Low enforcement)
  New Policy: DMZ Servers (High enforcement)
  User: admin@company.com

Root Cause Identified: Policy change
```

```
Step 2: Verify current policy
──────────────────────────────
Assets → Computers → WEBSRV01 → View Details
Current Policy: DMZ Servers
Enforcement: High (Connected), High (Disconnected)

Explanation: Server moved from Low to High enforcement
Result: All unapproved files now blocked
```

```
Step 3: Determine why policy changed
─────────────────────────────────────
Check automated assignment:
Rules → Policies → DMZ Servers → AD Rules

Finding: AD Rule configured:
  "OU=DMZ,OU=Servers,DC=company,DC=com"

Check server's AD location:
  Computer Object: CN=WEBSRV01,OU=DMZ,OU=Servers,DC=company,DC=com

Root Cause: Server was moved in Active Directory
  Someone moved WEBSRV01 into DMZ OU → Policy auto-assigned
```

```
Step 4: Resolution options
───────────────────────────
Option A: Move server back to correct policy
  Assets → Computers → WEBSRV01
  Action → Move Computer to Policy: Web Servers
  
Option B: Move server out of DMZ OU in AD
  Active Directory → Move WEBSRV01 to correct OU
  Wait for next AD sync
  
Option C: Approve applications (if DMZ policy is correct)
  If server should be in High enforcement:
  1. Events → Filter: Computer = WEBSRV01, Subtype = Execution blocked
  2. Identify all blocked apps
  3. Approve each (or approve by publisher)
```

**Customer communication:**

```
"I found the issue. At 2:00 PM, WEBSRV01 was moved to the DMZ 
organizational unit in Active Directory. CB App Control has a 
rule that automatically assigns the 'DMZ Servers' policy to 
computers in that OU.

The DMZ policy uses High enforcement for security, which blocks 
all unapproved applications.

Question: Should WEBSRV01 be in the DMZ OU?

If NO: I'll move it back to the Web Servers policy [do Option A]
If YES: We need to approve your web applications [do Option C]"
```

### 10.2 Scenario: "Agent offline but computer is online"

**Customer complaint:**
"Agent shows offline in console but the server is definitely online and we can ping it."

**Troubleshooting steps:**

```
Step 1: Verify console status
──────────────────────────────
Assets → Computers → Search for computer
Computer: DBSRV02
Status: Offline (red icon)
Last Communication: 2024-11-15 23:45:00
Current Time: 2024-11-16 08:30:00
Offline Duration: ~9 hours
```

```
Step 2: Test basic connectivity from your workstation
──────────────────────────────────────────────────────
PowerShell:
PS> Test-NetConnection DBSRV02

Result: Success (computer reachable)
```

```
Step 3: Check agent service on endpoint
────────────────────────────────────────
Remote PowerShell or RDP to DBSRV02:

PS> sc query dashost

Result:
SERVICE_NAME: dashost
    STATE              : 4  RUNNING
    WIN32_EXIT_CODE    : 0  (0x0)

Finding: Service is running (not a service issue)
```

```
Step 4: Check agent status
───────────────────────────
On DBSRV02:
C:\> cd "C:\Program Files (x86)\Bit9\Parity Agent"
C:\> dascli status

Output:
Server:                      appcontrol-old.company.com:41002
Connection Status:           Disconnected
Last Server Communication:   2024-11-15 23:45:00
Certificate Validation:      Failed (Cannot resolve hostname)

Root Cause Found: Agent configured with old server name
```

```
Step 5: Verify server name resolution
──────────────────────────────────────
C:\> nslookup appcontrol-old.company.com

Result:
DNS request timed out
Server:  UnKnown

Root Cause Confirmed: DNS name doesn't resolve
```

```
Step 6: Determine correct server name
──────────────────────────────────────
Check console:
Settings → Server Settings
Server URL: https://appcontrol.company.com

Finding: Server was renamed
Old: appcontrol-old.company.com
New: appcontrol.company.com
```

```
Step 7: Resolution
───────────────────
Option A: Update agent configuration
  Cannot do remotely (requires agent connection)
  Must update locally on DBSRV02
  
Option B: Reinstall agent with correct server name
  msiexec /i CBAppControl-Agent.msi /quiet SERVER=appcontrol.company.com KEY=<ComKey>
  
Option C: Update configuration file manually (advanced)
  Edit: C:\Program Files (x86)\Bit9\Parity Agent\parity.config
  Change server name
  Restart service: sc stop dashost && sc start dashost
```

**Chosen solution:**

```
On DBSRV02:

1. Stop service:
   sc stop dashost

2. Edit configuration:
   notepad "C:\Program Files (x86)\Bit9\Parity Agent\parity.config"
   
   Find: <Server>appcontrol-old.company.com:41002</Server>
   Change to: <Server>appcontrol.company.com:41002</Server>
   Save

3. Start service:
   sc start dashost

4. Verify:
   dascli connect
   
   Output:
   Connecting to appcontrol.company.com:41002...
   DNS resolution: Success (10.0.1.50)
   TCP connection: Success
   TLS handshake: Success
   Certificate validation: Success
   Authentication: Success
   Connected to server version 8.11.2

5. Check status:
   dascli status
   
   Connection Status: Connected ✓
```

**Prevention recommendation:**

```
"To prevent this in future server migrations:

1. Create DNS alias:
   appcontrol.company.com (CNAME) → current-server.company.com
   Configure agents with: appcontrol.company.com
   When migrating: Update CNAME, agents follow automatically

2. Use Group Policy or deployment tool to update agent configs:
   Create GPO to update parity.config if server name changes

3. Before server rename:
   Check how many agents configured with old name:
   Console → Assets → Computers → Search or filter"
```

### 10.3 Scenario: "Publisher approval not working"

**Customer complaint:**
"I approved Microsoft Corporation as publisher but Windows updates are still getting blocked."

**Troubleshooting steps:**

```
Step 1: Verify the publisher rule exists
─────────────────────────────────────────
Console → Rules → Software Rules → File Rules
Search: "Microsoft"

Finding: Rule exists
  Name: Approve Microsoft Publisher
  Type: Approval
  Rule Type: Publisher
  Publisher: CN=Microsoft Corporation, ...
  Status: Enabled
  Policy: All Policies
```

```
Step 2: Check blocked file
───────────────────────────
Reports → Events
Filter:
  - Subtype: Execution blocked
  - File Name: contains "KB"  (Windows update pattern)
  
Recent event:
  File: Windows10.0-KB5001234-x64.msu
  Computer: DESKTOP-001
  Time: 5 minutes ago
```

```
Step 3: Examine file details
─────────────────────────────
Click '+' next to event → View file details

Finding:
  File Name: Windows10.0-KB5001234-x64.msu
  Publisher: Microsoft Corporation ✓
  Signature: Valid ✓
  Certificate: [View Certificate]
  Global State: Unapproved
  Local State: Unapproved
```

```
Step 4: Check certificate details
──────────────────────────────────
Click "View Certificate" on file details page

Certificate Chain:
  Subject: CN=Microsoft Windows, O=Microsoft Corporation, L=Redmond, S=Washington, C=US
  Issuer: CN=Microsoft Code Signing PCA, ...
  
Finding: Certificate subject is "Microsoft Windows" not "Microsoft Corporation"
Root Cause: Rule matches "Microsoft Corporation" but file signed by "Microsoft Windows"
```

```
Step 5: Verify with dascli (optional deep dive)
────────────────────────────────────────────────
On endpoint:
dascli certinfo "C:\Path\To\Windows10.0-KB5001234-x64.msu"

Output:
Signing Certificate:
  Subject: CN=Microsoft Windows, O=Microsoft Corporation, ...
  
Rule matching requires EXACT certificate match
"Microsoft Windows" ≠ "Microsoft Corporation"
```

```
Step 6: Resolution
───────────────────
Option A: Update existing rule to match actual certificate
  Rules → Software Rules → Edit "Approve Microsoft Publisher"
  Certificate: Change to match "Microsoft Windows"
  
Option B: Create additional rule for Windows updates
  Rules → Software Rules → Add File Rule
  Type: Approval
  Rule Type: Publisher
  Certificate: Select from file → Choose the .msu file
  Name: Approve Microsoft Windows Updates
  
Option C: Approve intermediate CA (covers multiple MS certs)
  Rules → Software Rules → Add File Rule
  Certificate: Select "Microsoft Code Signing PCA" (intermediate)
  This approves anything signed by this CA
```

**Chosen solution: Option B**

```
1. Tools → Find Files
2. Search: Windows10.0-KB5001234-x64.msu
3. Select file → Click '+'
4. View Details → Certificate tab
5. Click "Approve Publisher"
6. Name: Approve Microsoft Windows Certificates
7. Create & Exit
8. Test: Attempt to install update
9. Verify: Check Events (should see "Execution monitored" or allowed)
```

**Customer education:**

```
"Microsoft uses multiple signing certificates:
- 'Microsoft Corporation' - Some apps
- 'Microsoft Windows' - Windows components/updates  
- 'Microsoft Corporation II' - Some newer apps
- Others...

When creating publisher rules, always verify the EXACT certificate 
on the files you want to approve.

Recommendation: Create multiple rules for different MS certificates,
or approve the intermediate CA to cover all Microsoft-signed files."
```

### 10.4 Scenario: "Custom rule not working"

**Customer complaint:**
"I created a Trusted Path rule for C:\CompanyApps\ but files are still blocked."

**Troubleshooting steps:**

```
Step 1: Verify rule configuration
──────────────────────────────────
Console → Rules → Software Rules → Custom
Find rule: "CompanyApps Trusted Path"

Current configuration:
  Name: CompanyApps Trusted Path
  Type: Trusted Path
  Path: C:\CompanyApps
  Execute Action: Allow and Promote
  Status: Enabled
  Policy: Production Servers
  
Finding: Path is "C:\CompanyApps" (no asterisk)
```

```
Step 2: Test file execution
────────────────────────────
Events → Filter:
  - Computer: APPSRV01
  - Subtype: Execution blocked
  - Max Age: 1 hour
  
Event found:
  File: C:\CompanyApps\Tool.exe
  Reason: Unapproved file in High enforcement
  Time: 2 minutes ago
```

```
Step 3: Check rule on endpoint
───────────────────────────────
On APPSRV01:
dascli showpathpolicies | findstr CompanyApps

Output:
Rule: CompanyApps Trusted Path
  Path: C:\CompanyApps
  Type: Trusted Path
  
Finding: Rule deployed to endpoint correctly
```

```
Step 4: Understand the problem
───────────────────────────────
Path: C:\CompanyApps
  Matches: The directory C:\CompanyApps itself
  Does NOT match: Files inside C:\CompanyApps\
  
File: C:\CompanyApps\Tool.exe
  Path is: C:\CompanyApps\Tool.exe
  Rule path: C:\CompanyApps
  Match: NO (file is inside, not the directory itself)
```

```
Step 5: Resolution
───────────────────
Fix the path pattern:

Console → Rules → Software Rules → Custom
Edit: CompanyApps Trusted Path
Change Path: C:\CompanyApps\*
Save

Wait for policy update:
Assets → Computers → APPSRV01
Policy Status: Out of date → (wait) → Up to date
```

```
Step 6: Verify fix
───────────────────
On APPSRV01:
dascli showpathpolicies | findstr -A 5 CompanyApps

Output:
Rule: CompanyApps Trusted Path
  Path: C:\CompanyApps\*
  
Test execution:
C:\CompanyApps\Tool.exe

Result: Executes successfully
```

**Additional considerations checked:**

```
Other common issues with custom rules:

1. Wrong policy assignment
   Rule applies to: Policy A
   Computer is in: Policy B
   → Computer doesn't get the rule
   
2. Case sensitivity
   Rule path: C:\companyapps\*
   Actual path: C:\CompanyApps\*
   → Windows paths are case-insensitive, this is OK
   
3. Wildcard confusion
   Path\*      = Files in Path (not subdirectories)
   Path\*\*    = Files in Path and all subdirectories
   Path\*.exe  = Only .exe files in Path
   
4. Process restrictions
   Path: C:\Tools\*
   Process: notepad.exe
   → Tool.exe can only run if launched by notepad
   → Probably not intended
   
5. Rule disabled
   Status: Disabled
   → Rule exists but not active
```

### 10.5 Scenario: "Performance issues after deployment"

**Customer complaint:**
"Since deploying CB App Control, our file server is very slow. Users complaining about delays accessing files."

**Initial assessment:**

```
Environment:
- File Server: FILESVR01
- Role: Central file repository
- Users: 500
- Files: ~2 million
- Agent installed: 2 days ago
```

**Troubleshooting steps:**

```
Step 1: Check initialization status
────────────────────────────────────
Console → Assets → Computers → FILESVR01

dascli status on FILESVR01:
  Initialization: In Progress
  Percent Complete: 67%
  
Finding: Agent still scanning 2 million files
Impact: High CPU/disk usage during initialization
```

```
Step 2: Check resource utilization
───────────────────────────────────
On FILESVR01:
Task Manager → Performance

CPU: 45% (DasHost.exe using 40%)
Disk: 95% active time
Memory: Normal

Finding: Agent initialization causing resource contention
```

```
Step 3: Review agent logs
──────────────────────────
C:\ProgramData\Bit9\Parity Agent\Logs\DasHost.log

[INFO] Scanning: E:\Shares\Department\... (584,392 files processed)
[INFO] Scanning: E:\Shares\Archive\... (queue: 1,205,438 files)
[WARN] Hash computation queue backlog: 450,000 files

Finding: Agent overwhelmed by file volume
```

```
Step 4: Immediate mitigation
─────────────────────────────
Option A: Let initialization complete
  - Takes time but necessary
  - One-time process
  - Performance returns to normal after
  
Option B: Throttle initialization
  - Not directly configurable per computer
  - Can adjust server-wide throttling settings
  
Option C: Exclude certain paths (if appropriate)
  - Don't scan unimportant directories
  - Reduces workload
```

```
Step 5: Check policy configuration
───────────────────────────────────
Assets → Computers → FILESVR01 → Current Policy
Policy: File Servers
Mode: Control
Track File Changes: Enabled
Memory Scan: Enabled

Consideration: File server doesn't execute files
Question: Does it need Control mode?
```

```
Step 6: Long-term optimization
───────────────────────────────
Recommendation: Change to Visibility mode

Why:
- File server stores files, doesn't execute them
- Users execute from workstations (already protected)
- Visibility mode: No enforcement, less overhead
- Still tracks files, just doesn't hash everything

Implementation:
1. Create new policy: "File Servers - Visibility Only"
   Mode: Visibility
   Track File Changes: No (reduce overhead)
   
2. Move FILESVR01 to new policy
   
3. Result:
   - Agent stops deep inspection
   - Still monitors activity
   - Performance improves
```

```
Step 7: Alternative optimization (if Control mode required)
────────────────────────────────────────────────────────────
If file server must be in Control mode:

1. Exclude non-executable paths
   Settings → File Catalog Settings
   Excluded Paths: Add:
   - E:\Shares\Archive\*     (old files, never executed)
   - E:\Shares\*\*.pdf       (PDFs aren't executable)
   - E:\Shares\*\*.docx      (Documents aren't executable)
   
2. Adjust scan priority
   Computer Details → Advanced → Scan Priority: Low
   (If available in version)
   
3. Schedule intensive operations
   Computer Details → Other Actions → Schedule operations
   Schedule initialization/scan during off-hours
```

**Resolution implemented:**

```
Decision: Change to Visibility mode
Reasoning: File server is storage, not execution environment

Steps taken:
1. Created policy: "File Servers - Visibility"
   Mode: Visibility
   Track File Changes: No
   
2. Moved FILESVR01 to new policy

3. Waited 5 minutes for policy update

4. Verified on endpoint:
   dascli status
   Mode: Visibility
   Enforcement Level: N/A (Visibility mode)
   
5. Monitored performance:
   CPU dropped from 45% to 8%
   Disk activity normalized
   User complaints stopped

6. Confirmed functionality:
   Events still logged
   File inventory still maintained
   Visibility preserved
```

**Customer communication:**

```
"The performance issue was caused by the agent's initialization process.
CB App Control was scanning and hashing 2 million files on your file server.

I've changed FILESVR01 to Visibility mode because:
1. It's a file storage server, not an execution environment
2. Users execute files from their workstations (which have agents)
3. Visibility mode still tracks activity but without enforcement overhead

Performance is now normal. Users are protected at their workstations,
and you still have visibility into file server activity.

Alternative: If you need Control mode on file servers, we can:
- Exclude archive directories from scanning
- Schedule scans during off-hours
- Adjust initialization settings

Let me know if you want to discuss further hardening."
```

---

## 11. Performance Optimization

### 11.1 Understanding Performance Factors

**What impacts CB App Control performance:**

```
1. Agent Operations
   - File scanning during initialization
   - Hash computation (SHA-256)
   - Policy evaluation
   - Cache updates
   - Server synchronization

2. Server Operations
   - Database queries
   - File reputation lookups
   - Policy distribution
   - Event processing
   - Report generation


3. Environment Factors
   - Number of endpoints
   - File inventory size
   - Network bandwidth
   - SQL Server performance
   - Disk I/O capacity
```

### 11.2 Agent Performance Optimization

#### Initialization Optimization

**Problem: Agent initialization impacting production systems**

```
Symptom:
- High CPU usage after agent install
- Slow file server performance
- Users reporting delays

Cause:
Agent scanning all files on fixed drives
```

**Solutions:**

```
Solution 1: Exclude non-executable paths
─────────────────────────────────────────
Settings → File Catalog Settings → Excluded Paths

Add paths that don't contain executables:
  E:\Shares\Documents\*     (Office files)
  E:\Shares\Media\*         (Videos, images)
  D:\Backups\*              (Backup files)
  C:\Windows\Logs\*         (Log files)

Impact:
- Reduces files to scan
- Faster initialization
- Lower resource usage

Caution:
- Don't exclude paths with executables
- Malware could hide in excluded areas
- Document exclusions for audit
```

```
Solution 2: Use Visibility mode for non-execution servers
──────────────────────────────────────────────────────────
Policy Settings:
  Mode: Visibility
  Purpose: Monitoring without enforcement

Appropriate for:
- File servers (pure storage)
- Database servers (run only DB engine)
- Print servers
- Backup servers

Benefits:
- Lighter resource footprint
- Still collects events
- No execution blocking overhead

Not appropriate for:
- Application servers
- Web servers
- Workstations
- Jump boxes
```

```
Solution 3: Schedule initialization during maintenance windows
───────────────────────────────────────────────────────────────
For critical production servers:

1. Install agent with initialization disabled
   msiexec /i Agent.msi /quiet SERVER=... KEY=... INITIALIZE=0

2. Schedule initialization for off-hours
   Computer Details → Other Actions → Schedule Initialization
   Set: Saturday 2:00 AM - 6:00 AM

3. Monitor progress
   Check initialization percentage during window
   Ensure completes before business hours

Alternative:
Deploy agents gradually (10% of servers per weekend)
```

#### Runtime Performance Optimization

**Problem: Ongoing performance impact during normal operations**

```
Solution 1: Optimize cache size
────────────────────────────────
Agent maintains local cache of file states

Check cache size:
dascli status
  Local Files: 15,279

If cache very large (>50,000 files):
1. Check for unnecessary files
2. Clean up temp directories
3. Remove old software

If cache corrupted:
dascli password <pwd>
dascli clearcache
(Rebuilds cache, temporary performance impact)
```

```
Solution 2: Reduce custom rule count
─────────────────────────────────────
Each rule adds evaluation overhead

Check rule count:
dascli showpathpolicies | find /c "Rule"

Best practices:
- Consolidate similar rules
- Remove unused rules
- Use specific paths (not broad wildcards)

Example - Bad (many rules):
  Rule 1: Allow C:\App1\*.exe
  Rule 2: Allow C:\App2\*.exe
  Rule 3: Allow C:\App3\*.exe
  ...50 more rules...

Example - Good (consolidated):
  Rule 1: Allow C:\Applications\*\*.exe
  (Single rule, organized structure)
```

```
Solution 3: Disable unnecessary features
─────────────────────────────────────────
Policy Settings:
  Track File Changes: No (if not needed)
  Memory Scan: No (if not needed)
  Device Control: No (if not needed)

Each feature adds overhead:
- Track File Changes: Monitors all file modifications
- Memory Scan: Scans process memory
- Device Control: Monitors USB/device events

Recommendation:
Enable only features you actively use
```

### 11.3 Server Performance Optimization

#### Database Optimization

**Problem: Slow console response, report generation delays**

```
Solution 1: SQL Server configuration
─────────────────────────────────────
Verify SQL settings meet CB App Control requirements

Recommended settings:
1. Memory allocation:
   Min: 4 GB (small deployments)
   Max: 16 GB+ (large deployments)

2. Tempdb configuration:
   Multiple data files (1 per CPU core, max 8)
   Appropriate size (10GB+)

3. Database maintenance:
   Regular index maintenance
   Statistics updates
   
Check:
SQL Management Studio → Server Properties → Memory
  Minimum: 4096 MB
  Maximum: 16384 MB (for 32GB server)
```

```
Solution 2: Database maintenance schedule
──────────────────────────────────────────
Create SQL maintenance plan:

Weekly tasks:
- Index rebuild (fragmentation >30%)
- Index reorganize (fragmentation 10-30%)
- Update statistics
- Check database integrity

Example SQL script:
-- Update statistics on CB App Control database
USE AppControl
GO
EXEC sp_updatestats
GO

-- Rebuild indexes with high fragmentation
DECLARE @TableName VARCHAR(255)
DECLARE @sql NVARCHAR(500)
DECLARE TableCursor CURSOR FOR
    SELECT DISTINCT OBJECT_NAME(ips.object_id)
    FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
    WHERE ips.avg_fragmentation_in_percent > 30

OPEN TableCursor
FETCH NEXT FROM TableCursor INTO @TableName
WHILE @@FETCH_STATUS = 0
BEGIN
    SET @sql = N'ALTER INDEX ALL ON ' + @TableName + N' REBUILD'
    EXEC sp_executesql @sql
    FETCH NEXT FROM TableCursor INTO @TableName
END
CLOSE TableCursor
DEALLOCATE TableCursor
```

```
Solution 3: Archive old events
───────────────────────────────
Database grows with events

Retention settings:
Console → Settings → Event Retention
  Keep events for: 90 days (adjust based on needs)
  Archive to external SIEM: Yes (if available)

Manual cleanup:
Support page → Database Maintenance
  Run: Archive events older than 90 days
  Run: Purge archived events

Impact:
- Smaller database size
- Faster queries
- Improved performance
```

#### Server Resource Optimization

```
Solution 1: Dedicated SQL Server (large deployments)
─────────────────────────────────────────────────────
For >10,000 endpoints:

Architecture:
┌─────────────────┐     ┌─────────────────┐
│  CB App Control │────→│   SQL Server    │
│     Server      │     │   (Dedicated)   │
│  - IIS Console  │     │  - Database only│
│  - Parity Svc   │     │  - Optimized    │
└─────────────────┘     └─────────────────┘

Benefits:
- Isolate resource usage
- Optimize each server independently
- Better performance scaling
- Easier troubleshooting
```

```
Solution 2: Load balancing (very large deployments)
────────────────────────────────────────────────────
For >50,000 endpoints:

Architecture:
                    ┌─────────────┐
                    │Load Balancer│
                    └──────┬──────┘
              ┌────────────┼────────────┐
              ↓            ↓            ↓
        ┌─────────┐  ┌─────────┐  ┌─────────┐
        │ CB Svr1 │  │ CB Svr2 │  │ CB Svr3 │
        └────┬────┘  └────┬────┘  └────┬────┘
             └───────────┬─────────────┘
                         ↓
                 ┌───────────────┐
                 │  SQL Cluster  │
                 └───────────────┘

Agents distribute across servers
Console access load balanced
Shared database backend
```

### 11.4 Network Optimization

**Problem: Agent communication causing network congestion**

```
Solution 1: Adjust synchronization interval
────────────────────────────────────────────
Settings → Agent Settings → Synchronization

Default: Every 30 seconds
Large deployment: Every 60 seconds

Calculation:
  10,000 agents × 30-second sync = ~333 connections/sec
  10,000 agents × 60-second sync = ~167 connections/sec

Impact:
- Reduces server load
- Less network traffic
- Minimal delay in policy updates

Recommendation:
- Critical servers: 30 seconds
- Workstations: 60-120 seconds
- Remote sites: Consider on-demand sync
```

```
Solution 2: File reputation caching
────────────────────────────────────
Settings → File Reputation → Caching

Enable local reputation cache:
  Cache Size: 10,000 entries
  Cache Duration: 7 days

Benefits:
- Reduces cloud lookups
- Faster file decisions
- Less internet bandwidth

Appropriate for:
- Large deployments
- Bandwidth-constrained sites
- Air-gapped environments (with periodic cache updates)
```

```
Solution 3: Remote site optimization
─────────────────────────────────────
For offices with slow WAN links:

Option A: Distributed server architecture
  Deploy CB App Control server at each site
  Agents connect to local server
  Replicate policies between servers

Option B: Disconnected operation
  Agents configured for offline enforcement
  Periodic synchronization during off-hours
  Higher disconnected enforcement

Option C: WAN optimization
  Deploy WAN accelerator
  Enable compression for port 41002 traffic
  QoS prioritization if needed
```

### 11.5 Policy Optimization

**Problem: Complex policies causing slow enforcement**

```
Solution 1: Simplify approval rules
────────────────────────────────────
Instead of thousands of hash-based approvals:

Bad approach:
  15,000 individual file approvals
  Each file checked against all rules
  Slow rule evaluation

Good approach:
  Approve by publisher (100 rules)
  Use reputation rules (auto-approve trusted)
  Trusted directories for managed software

Example optimization:
Before:
  5,000 Microsoft file approvals (individual hashes)
After:
  5 Microsoft publisher approvals (by certificate)
  Result: 99.9% fewer rules, same coverage
```

```
Solution 2: Policy hierarchy
─────────────────────────────
Organize policies logically:

Structure:
├── Base Policy (minimal rules, High enforcement)
├── Standard Workstations (adds office apps)
├── Developer Workstations (adds dev tools)
└── Servers
    ├── Web Servers (web-specific rules)
    ├── Database Servers (DB-specific rules)
    └── File Servers (storage-specific rules)

Benefits:
- Clear organization
- Fewer rules per policy
- Easier maintenance
- Better performance
```

```
Solution 3: Regular cleanup
────────────────────────────
Scheduled maintenance:

Monthly:
1. Review unused rules
   Reports → Events → No matches in 90 days
   Disable or remove

2. Consolidate duplicate rules
   Find rules with same effect
   Merge into single rule

3. Review ban list
   Remove bans for uninstalled software
   Reduces rule count

4. Clean up local approvals
   Find files approved locally but unused
   Consider global approval or removal
```

### 11.6 Monitoring Performance

**Key metrics to watch:**

```
Server Metrics
──────────────
Console → Support → Reports → System Performance

Metrics:
- Agent backlog: <1,000 (good), >10,000 (problem)
- Event processing lag: <5 minutes (good)
- SQL response time: <100ms average
- CPU usage: <70% average
- Memory usage: <80% of available
- Disk queue length: <2 average

Thresholds for alerts:
CREATE ALERT "High Agent Backlog"
  WHEN backlog > 5,000
  SEND EMAIL TO: admins@company.com
```

```
Agent Metrics
─────────────
Per-computer monitoring:

Key indicators:
- Policy status: Should be "Up to date"
- Last communication: Should be <5 minutes
- Initialization: Should be "Complete"
- Cache size: <50,000 files typically

Query to find problematic agents:
Console → Assets → Computers → Add Filters:
  Policy Status: Out of date
  Last Communication: >1 hour ago
  
Export list for investigation
```

```
Database Metrics
────────────────
SQL Server monitoring:

Important queries:
-- Database size growth
SELECT 
    name,
    size * 8 / 1024 AS SizeMB,
    max_size * 8 / 1024 AS MaxSizeMB
FROM sys.database_files

-- Top queries by CPU
SELECT TOP 10
    qs.execution_count,
    qs.total_worker_time / qs.execution_count AS AvgCPU,
    SUBSTRING(qt.text, qs.statement_start_offset/2, 
        (CASE WHEN qs.statement_end_offset = -1 
        THEN LEN(CONVERT(nvarchar(max), qt.text)) * 2 
        ELSE qs.statement_end_offset END - qs.statement_start_offset)/2) 
        AS QueryText
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
ORDER BY AvgCPU DESC

-- Index fragmentation
SELECT 
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 30
ORDER BY ips.avg_fragmentation_in_percent DESC
```

---

## 12. Integration and API

### 12.1 Active Directory Integration

**Purpose: Automate policy assignment and user rights**

#### Configuration Steps

```
Step 1: Configure AD connection
────────────────────────────────
Console → Settings → Active Directory

LDAP Settings:
  Domain: company.com
  LDAP Server: dc01.company.com
  Port: 389 (LDAP) or 636 (LDAPS - recommended)
  
Authentication:
  Bind DN: CN=CB Service Account,OU=Service Accounts,DC=company,DC=com
  Password: [service account password]
  
Options:
  ☑ Use SSL (LDAPS)
  ☑ Validate certificate
  ☑ Follow referrals
```

```
Step 2: Enable AD synchronization
──────────────────────────────────
Settings → Active Directory → Synchronization

Schedule:
  Frequency: Every 1 hour (adjust based on needs)
  Full sync: Daily at 2:00 AM
  Incremental: Every hour

What synchronizes:
- Computer objects
- User accounts
- Group memberships
- OU structure

Test connection:
  Click "Test Connection"
  Should show: "Successfully connected to dc01.company.com"
```

```
Step 3: Configure policy assignment by OU
──────────────────────────────────────────
Rules → Policies → Select Policy → AD Rules tab

Example: Web Servers Policy
  AD Rule: OU=Web Servers,OU=Servers,DC=company,DC=com
  
Example: Developer Workstations
  AD Rule: OU=Developers,OU=Workstations,DC=company,DC=com

Behavior:
- Agent queries AD for computer's OU
- Server matches OU to policy rules
- Policy auto-assigned when match found
```

#### Troubleshooting AD Integration

```
Problem: Computers not auto-assigning to policies
──────────────────────────────────────────────────

Check 1: AD synchronization status
  Settings → Active Directory → Last Sync
  Should be recent (within last hour)
  
Check 2: Computer object location
  Active Directory Users and Computers
  Find computer → Properties → Location
  Verify in expected OU
  
Check 3: AD Rule syntax
  Rules → Policies → Policy → AD Rules
  Format: OU=Name,OU=Parent,DC=domain,DC=com
  Case-sensitive: Must match exactly
  
Check 4: Events log
  Reports → Events → Filter: Subtype = "Policy assigned"
  Look for AD-based assignments
  Check for errors
```

```
Problem: AD sync failing
────────────────────────

Error: "Cannot connect to LDAP server"
Fix:
  1. Verify LDAP server reachable:
     telnet dc01.company.com 389
  2. Check firewall (port 389/636)
  3. Verify DNS resolution
  
Error: "Authentication failed"
Fix:
  1. Verify service account credentials
  2. Check account not locked/expired
  3. Verify account has Read permissions on AD
  4. Test with: ldp.exe (AD testing tool)
  
Error: "SSL certificate validation failed"
Fix:
  1. Import DC's certificate to server trust store
  2. Verify certificate not expired
  3. Ensure full chain trusted
```

### 12.2 API Integration

**Purpose: Automate administrative tasks via REST API**

#### API Basics

```
API Endpoint:
https://appcontrol.company.com/api/bit9platform/v1/

Authentication:
Header: X-Auth-Token: <API_Token>

Content-Type:
application/json
```

#### Generating API Token

```
Console:
1. User dropdown (top right) → API Token
2. Click "Generate New Token"
3. Copy token immediately (shown once)
4. Store securely

Token example:
7A8B9C0D1E2F3A4B5C6D7E8F9A0B1C2D3E4F5A6B7C8D9E0F
```

#### Common API Operations

**Example 1: Get list of computers**

```powershell
# PowerShell
$token = "YOUR_API_TOKEN"
$headers = @{
    "X-Auth-Token" = $token
}
$url = "https://appcontrol.company.com/api/bit9platform/v1/computer"

$response = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
$response | Format-Table -Property name, policyName, connected
```

**Example 2: Approve a file globally**

```powershell
# Get file by hash
$hash = "7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b"
$url = "https://appcontrol.company.com/api/bit9platform/v1/fileInstance?q=sha256:$hash"

$file = Invoke-RestMethod -Uri $url -Headers $headers -Method Get

# Approve the file
$approvalUrl = "https://appcontrol.company.com/api/bit9platform/v1/fileInstance/$($file[0].id)"
$body = @{
    "localState" = 2  # 2 = Approved
} | ConvertTo-Json

Invoke-RestMethod -Uri $approvalUrl -Headers $headers -Method PUT -Body $body -ContentType "application/json"
```

**Example 3: Move computer to policy**

```powershell
# Find computer
$computerName = "WEBSRV01"
$url = "https://appcontrol.company.com/api/bit9platform/v1/computer?q=name:$computerName"
$computer = Invoke-RestMethod -Uri $url -Headers $headers -Method Get

# Find policy
$policyName = "Web Servers"
$url = "https://appcontrol.company.com/api/bit9platform/v1/policy?q=name:$policyName"
$policy = Invoke-RestMethod -Uri $url -Headers $headers -Method Get

# Move computer to policy
$updateUrl = "https://appcontrol.company.com/api/bit9platform/v1/computer/$($computer[0].id)"
$body = @{
    "policyId" = $policy[0].id
} | ConvertTo-Json

Invoke-RestMethod -Uri $updateUrl -Headers $headers -Method PUT -Body $body -ContentType "application/json"
```

#### Automation Scripts for Common Tasks

**Script: Daily report of blocked files**

```powershell
# daily-blocked-report.ps1
param(
    [string]$Server = "appcontrol.company.com",
    [string]$Token = "YOUR_TOKEN",
    [string]$EmailTo = "security@company.com"
)

$headers = @{"X-Auth-Token" = $Token}
$baseUrl = "https://$Server/api/bit9platform/v1"

# Get blocked events from last 24 hours
$yesterday = (Get-Date).AddDays(-1).ToString("yyyy-MM-ddTHH:mm:ss")
$url = "$baseUrl/event?q=subtype:8&timestamp>$yesterday"  # subtype:8 = Execution blocked

$events = Invoke-RestMethod -Uri $url -Headers $headers -Method Get

# Group by file
$grouped = $events | Group-Object -Property fileName | 
    Select-Object Count, Name | 
    Sort-Object -Property Count -Descending

# Create HTML report
$html = @"
<html>
<head><title>Daily Blocked Files Report</title></head>
<body>
<h1>Blocked Files - Last 24 Hours</h1>
<p>Total blocks: $($events.Count)</p>
<table border='1'>
<tr><th>File Name</th><th>Block Count</th></tr>
"@

foreach ($item in $grouped) {
    $html += "<tr><td>$($item.Name)</td><td>$($item.Count)</td></tr>"
}

$html += "</table></body></html>"

# Send email
Send-MailMessage -To $EmailTo -From "cbappcontrol@company.com" `
    -Subject "CB App Control: Daily Blocked Files Report" `
    -Body $html -BodyAsHtml -SmtpServer "smtp.company.com"
```

**Script: Bulk approve files from CSV**

```powershell
# bulk-approve.ps1
param(
    [string]$CsvPath = "files-to-approve.csv",
    [string]$Server = "appcontrol.company.com",
    [string]$Token = "YOUR_TOKEN"
)

$headers = @{"X-Auth-Token" = $Token}
$baseUrl = "https://$Server/api/bit9platform/v1"

# CSV format: FileName,SHA256Hash,Reason
$files = Import-Csv -Path $CsvPath

foreach ($file in $files) {
    Write-Host "Processing: $($file.FileName)"
    
    # Find file by hash
    $url = "$baseUrl/fileInstance?q=sha256:$($file.SHA256Hash)"
    $fileInstance = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
    
    if ($fileInstance.Count -eq 0) {
        Write-Warning "File not found in catalog: $($file.FileName)"
        continue
    }
    
    # Approve globally
    $fileId = $fileInstance[0].id
    $updateUrl = "$baseUrl/file/Instance/$fileId"
    $body = @{
        "localState" = 2  # Approved
    } | ConvertTo-Json
    
    try {
        Invoke-RestMethod -Uri $updateUrl -Headers $headers -Method PUT -Body $body -ContentType "application/json"
        Write-Host "✓ Approved: $($file.FileName)" -ForegroundColor Green
    } catch {
        Write-Error "✗ Failed to approve: $($file.FileName) - $($_.Exception.Message)"
    }
}
```

### 12.3 SIEM Integration

**Purpose: Send CB App Control events to external SIEM**

#### Syslog Configuration

```
Console → Settings → External Logging

Syslog Settings:
  Server: siem.company.com
  Port: 514 (UDP) or 6514 (TCP)
  Protocol: TCP recommended (reliable delivery)
  Format: CEF (Common Event Format) or JSON
  
Facility: Local0 (or as required by SIEM)
Severity Mapping:
  Critical events → Emergency
  High priority → Alert
  Medium priority → Warning
  Low priority → Informational
```

#### Event Filtering

```
Select events to forward:
☑ Execution blocked
☑ File banned
☑ High-risk file activity
☑ Threshold alerts
☐ Execution monitored (too verbose)
☐ Policy assigned (administrative)

Rate limiting:
  Max events per second: 100
  Burst limit: 500
```

#### Sample Syslog Events

```
CEF Format:
CEF:0|VMware|Carbon Black App Control|8.11.2|8|Execution blocked|8|
src=10.0.5.100 suser=COMPANY\jsmith fname=malware.exe 
fhash=7a8b9c0d... msg=File blocked in High enforcement

JSON Format:
{
  "timestamp": "2024-11-16T14:30:00Z",
  "computer": "DESKTOP-001",
  "user": "COMPANY\\jsmith",
  "event_type": "execution_blocked",
  "file_name": "malware.exe",
  "file_hash": "7a8b9c0d...",
  "policy": "Corporate Workstations",
  "enforcement": "High",
  "threat_level": 85,
  "trust_level": 15
}
```

#### Splunk Integration Example

```
Splunk App for CB App Control:
1. Install app from Splunkbase
2. Configure data input:
   Settings → Data Inputs → TCP/UDP
   Port: 514
   Source type: cb_app_control
   
3. Create alerts:
   Search: sourcetype=cb_app_control event_type=execution_blocked
   Alert condition: When results > 100 in 5 minutes
   Action: Send email to security team
   
4. Dashboard queries:
   # Top blocked files
   sourcetype=cb_app_control event_type=execution_blocked
   | stats count by file_name
   | sort -count
   | head 20
   
   # Blocked files by computer
   sourcetype=cb_app_control event_type=execution_blocked
   | stats count by computer
   | sort -count
   
   # High-risk activity
   sourcetype=cb_app_control threat_level>70
   | timechart count by event_type
```

---

## 13. Daily Technical Support Tasks

### 13.1 Morning Routine Checklist

```
☐ 1. Check System Health Dashboard
   Console → Support → System Health
   - All indicators green?
   - Any new alerts overnight?
   
☐ 2. Review High-Priority Alerts
   Top of console → Alert icons
   - Critical/High priority items
   - Address immediately
   
☐ 3. Check Agent Connectivity
   Assets → Computers → Add Filter: Last Communication >1 hour
   - Investigate offline agents
   - Check for patterns (site/network issue?)
   
☐ 4. Review Blocked Files (Security)
   Reports → Events → Saved Views: "Blocked Files (All)"
   - Group By: File Name
   - Max Age: Last 24 hours
   - Identify unusual blocks
   - Check threat levels
   
☐ 5. Check Approval Requests
   Tools → Approval Requests
   - Pending requests from users
   - Prioritize by business impact
   - Approve/deny based on policy
   
☐ 6. Review Server Performance
   Support → Reports → System Performance
   - Agent backlog normal?
   - Event processing lag acceptable?
   - Resource utilization healthy?
```

### 13.2 Ticket Handling Workflow

**Standard troubleshooting process:**

```
Step 1: Information Gathering (5 min)
──────────────────────────────────────
Questions to ask:
- What exactly is happening? (specific error/behavior)
- Which computer/user affected?
- When did it start? (exact time if possible)
- Has anything changed recently?
- Is this impacting business operations?

Document in ticket:
- Computer name
- User name
- File/application name
- Exact error message
- Business impact level
```

```
Step 2: Initial Assessment (10 min)
────────────────────────────────────
Console checks:
1. Assets → Computers → Find computer
   - Online/Offline status
   - Current policy
   - Last communication time
   - Policy status (up to date?)

2. Reports → Events → Filter by computer
   - Recent events (last 2 hours)
   - Any errors/blocks?
   - Pattern of activity

3. If file-related:
   - Tools → Find Files → Search filename
   - Check global/local state
   - Check publisher/certificate
   - Check threat/trust levels
```

```
Step 3: Root Cause Analysis (15-30 min)
────────────────────────────────────────
Based on symptoms, follow specific troubleshooting tree:

File Blocked:
  → Check enforcement level
  → Check file state (approved/banned/unapproved)
  → Check custom rules (showpathpolicies)
  → Determine if security or config issue

Agent Offline:
  → dascli status (connection status)
  → dascli connect (connectivity test)
  → Check network/firewall
  → Check certificate trust

Rule Not Working:
  → Verify rule enabled and applies to correct policy
  → Check rule syntax (paths, wildcards)
  → Check for conflicting rules
  → Verify policy up to date on endpoint

Performance Issue:
  → Check initialization status
  → Check resource usage (CPU/disk)
  → Check agent logs for errors
  → Review policy complexity
```

```
Step 4: Resolution (varies)
───────────────────────────
Choose appropriate fix:
- Approve file (if legitimate)
- Fix agent connectivity
- Correct policy assignment
- Update/fix custom rule
- Optimize performance

Test fix:
- Reproduce original issue
- Verify resolution
- Check for side effects
```

```
Step 5: Documentation and Follow-up (10 min)
─────────────────────────────────────────────
Document in ticket:
- Root cause identified
- Actions taken
- Configuration changes made
- Testing performed
- Customer notification

Follow-up:
- Schedule check-in (1 day later)
- Verify issue hasn't recurred
- Close ticket with summary
```

### 13.3 Common Ticket Types

#### Type 1: "Can't run/install application"

```
Quick diagnostic script:
──────────────────────────
# Run on endpoint or get from customer
dascli status | findstr "Policy Enforcement"
dascli analyze "C:\path\to\blocked\file.exe"

Common causes:
1. High enforcement + unapproved file (90%)
2. File explicitly banned (5%)
3. Custom rule blocking (3%)
4. Certificate validation failure (2%)

Quick fixes:
- Approve file globally (if corporate standard)
- Approve file locally (if computer-specific)
- Approve by publisher (if signed, multiple versions)
- Add trusted path rule (if testing/dev scenario)
```

#### Type 2: "Agent showing offline"

```
Diagnostic checklist:
─────────────────────
On endpoint:
1. sc query dashost
   → Service running?
   
2. dascli connect
   → Can reach server?
   
3. dascli status | findstr "Certificate"
   → Certificate valid?
   
4. netstat -ano | findstr 41002
   → TCP connection established?

Common causes:
1. Network/firewall blocking port 41002 (60%)
2. Certificate trust issue (25%)
3. Service stopped (10%)
4. Server name/IP changed (5%)

Quick fixes:
- Open firewall port 41002
- Import certificate to trusted root
- Start dashost service
- Update agent configuration (server address)
```

#### Type 3: "Everything was working, now it's not"

```
Timeline investigation:
───────────────────────
Events → Computer filter → Max Age: 4 hours → Group by: Subtype

Look for:
- "Policy assigned" (policy change )
- "Rule modified" (rule change)
- "Agent disconnected" (connectivity loss)
- "Policy updated" (configuration change)

Common causes:
1. Accidental policy change (40%)
2. AD-based policy reassignment (30%)
3. Agent disconnected from server (20%)
4. Rule modification (10%)

Quick fixes:
- Move computer back to correct policy
- Fix AD OU placement
- Restore network connectivity
- Revert rule changes (if available in change log)
```

#### Type 4: "Performance issues after agent install"

```
Performance assessment:
───────────────────────
On endpoint:
1. dascli status | findstr "Initialization"
   → Still initializing? (normal for new agents)
   
2. Task Manager → Performance
   → DasHost.exe CPU/Disk usage?
   
3. Check file count:
   dascli status | findstr "Local Files"
   → Unusually high? (>100,000)

Common causes:
1. Initialization in progress (70%)
2. File server with millions of files (15%)
3. Too many custom rules (10%)
4. Corrupted cache (5%)

Quick fixes:
- Wait for initialization to complete
- Consider Visibility mode for file servers
- Reduce custom rule count
- Clear and rebuild cache (last resort)
```

### 13.4 Escalation Criteria

**When to escalate to Carbon Black Support:**

```
Immediate escalation:
─────────────────────
1. Server service won't start
   Error: "Parity Server failed to start"
   Impact: All agents disconnected
   
2. Database corruption
   Error: SQL integrity check failures
   Impact: Cannot query/update data
   
3. Agent causing system crash/BSOD
   Error: Parity.sys driver crash
   Impact: Computer unusable
   
4. Licensing issues
   Error: "License limit exceeded" but count incorrect
   Impact: Cannot deploy to new computers
   
5. Data loss/security breach
   Issue: Unauthorized data access via CB App Control
   Impact: Security incident
```

```
Standard escalation (within 24 hours):
──────────────────────────────────────
1. Complex Expert Rule needed
   Issue: Advanced rule logic required
   Reason: Incorrect rule could impact security
   
2. Performance issues unresolved
   Issue: Optimization steps didn't help
   Reason: May require deeper analysis
   
3. Integration failures
   Issue: API/AD/SIEM integration not working
   Reason: May require product team involvement
   
4. Unusual agent behavior
   Issue: Agent behaving inconsistently
   Reason: Possible product defect
```

```
Escalation process:
───────────────────
1. Collect diagnostics:
   - dascli capture on affected endpoint
   - Server support bundle (Support page)
   - Screenshots of errors
   - Timeline of events
   
2. Create support ticket:
   - https://support.carbonblack.com
   - Include all diagnostics
   - Clear problem statement
   - Business impact description
   - Troubleshooting already performed
   
3. Set severity appropriately:
   Sev 1: Production down, business impact
   Sev 2: Major functionality impaired
   Sev 3: Minor issue, workaround available
   Sev 4: Question or feature request
```

### 13.5 Proactive Monitoring Tasks

**Weekly tasks:**

```
☐ Review policy compliance
  Reports → Baseline Drift → Run reports
  - Identify computers with unexpected changes
  - Investigate unauthorized software installations
  - Review new files in environment
  
☐ Check system health trends
  Support → Reports → Agent Backlog Statistics
  - Look for growing backlogs
  - Identify performance trends
  - Plan capacity if needed
  
☐ Review approval request patterns
  Tools → Approval Requests → Export to Excel
  - Most requested applications
  - Consider global approval if appropriate
  - Identify training opportunities
  
☐ Audit administrative actions
  Reports → Events → Saved Views: Console Access
  - Review policy changes
  - Review user account modifications
  - Review approval/ban actions
  - Ensure all changes authorized
```

**Monthly tasks:**

```
☐ Database maintenance
  SQL Management Studio → Maintenance Plans
  - Index rebuild/reorganize
  - Update statistics
  - Check database size growth
  - Archive old events if needed
  
☐ Certificate expiration check
  Settings → Communication → SSL Certificate
  - Check expiration date
  - If <90 days: Plan renewal
  - If <30 days: Urgent renewal needed
  
☐ Review and optimize rules
  Rules → Software Rules → All rules
  - Identify unused rules (no matches in 90 days)
  - Consolidate similar rules
  - Remove obsolete rules
  - Document rule purposes
  
☐ Agent version audit
  Assets → Computers → Group by: Agent Version
  - Identify outdated agents
  - Plan agent upgrades
  - Test new agent version in lab first
  
☐ Security review
  Reports → Events → Threat Indicators
  - Review high-threat activity
  - Investigate suspicious patterns
  - Update security policies if needed
  - Share findings with security team
```

**Quarterly tasks:**

```
☐ Disaster recovery test
  - Backup server configuration
  - Backup database
  - Document restore procedure
  - Test restore in lab environment
  
☐ Capacity planning review
  - Current endpoint count vs. license limit
  - Database size growth rate
  - Server resource utilization trends
  - Plan upgrades if needed
  
☐ Policy review with stakeholders
  - Meet with application owners
  - Review enforcement levels
  - Discuss new applications/requirements
  - Update policies as needed
  
☐ Training needs assessment
  - Review common support tickets
  - Identify knowledge gaps
  - Schedule refresher training
  - Update documentation
```

### 13.6 Knowledge Base Article Creation

**When to create a KB article:**

```
Criteria:
- Issue occurred 3+ times
- Resolution not documented
- Complex troubleshooting steps
- Workaround for known issue
```

**KB article template:**

```markdown
# [Issue Title]

## Symptoms
- Bullet list of observable symptoms
- Error messages (exact text)
- Affected versions/configurations

## Environment
- CB App Control version: 8.11.2
- Operating system: Windows Server 2019
- Policy configuration: High enforcement
- Other relevant details

## Cause
Clear explanation of root cause

## Resolution

### Option 1: [Preferred Solution]
Step-by-step instructions:
1. Step one with command/screenshot
2. Step two
3. Verification step

### Option 2: [Alternative Solution]
When to use this option:
[Steps]

### Option 3: [Workaround]
Temporary fix while permanent solution deployed:
[Steps]

## Prevention
How to avoid this issue in future:
- Configuration recommendation
- Monitoring to implement
- Best practices

## Related Articles
- Link to related KB articles
- Link to official documentation

## Keywords
searchable, terms, for, this, issue
```

---

## 14. Advanced Troubleshooting Techniques

### 14.1 Event Correlation Analysis

**Purpose: Identify patterns across multiple events**

**Scenario: Widespread blocking across environment**

```
Step 1: Identify scope
──────────────────────
Reports → Events → Filter:
  Subtype: Execution blocked
  Max Age: 1 hour
  
Group By: Computer
  → See which computers affected
  
Group By: File Name
  → See which files blocked
  
Group By: Policy
  → See which policies involved
```

```
Step 2: Look for common factors
────────────────────────────────
Common patterns:

Pattern 1: Same file, multiple computers
  File: "update.exe"
  Computers: 45 different computers
  Time: All within 10-minute window
  → Likely: Software update pushed via SCCM/GPO
  → Action: Approve file if legitimate

Pattern 2: Same computer, multiple files
  Computer: APPSRV01
  Files: Various .dll files
  Time: Started at 14:30
  → Likely: Policy change or agent reconnection
  → Action: Check policy assignment history

Pattern 3: Same user, multiple computers
  User: jsmith
  Computers: DESKTOP-001, LAPTOP-042
  Files: Same application
  → Likely: User moved between computers
  → Action: Approve for user's policy

Pattern 4: Same time, multiple computers
  Time: 14:00 exactly
  Computers: All web servers
  Files: Various
  → Likely: Scheduled task or automated process
  → Action: Investigate automated deployment
```

```
Step 3: Export for analysis
────────────────────────────
Reports → Events → Apply filters → Export

Export to Excel/CSV:
- Timestamp
- Computer
- User
- File name
- File hash
- Policy

Analyze with Excel:
- Pivot tables
- Timeline graphs
- Identify outliers
```

### 14.2 File Analysis Workflow

**Purpose: Deep investigation of suspicious files**

```
Step 1: Basic file information
───────────────────────────────
Tools → Find Files → Search filename

Check:
☐ File name and path
☐ File size
☐ SHA-256 hash
☐ First seen date (in environment)
☐ Certificate/publisher
☐ Trust level (0-100)
☐ Threat level (0-100)
☐ Prevalence (how common)
```

```
Step 2: Certificate validation
───────────────────────────────
On endpoint:
dascli certinfo "C:\path\to\file.exe"

Verify:
☐ Is file signed? (Many legitimate apps are)
☐ Is signature valid? (Not tampered)
☐ Who is publisher? (Known company?)
☐ Certificate chain complete? (Root CA trusted)
☐ Certificate current? (Not expired)

Red flags:
⚠ Unsigned executable
⚠ Invalid signature (file modified after signing)
⚠ Unknown/suspicious publisher
⚠ Certificate expired
⚠ Self-signed certificate
```

```
Step 3: Reputation research
────────────────────────────
If File Reputation enabled:
  Console → File details → Reputation tab
  
  Trust Factor: 0-100
    90-100: Known good (Microsoft, Adobe, etc.)
    70-89: Likely legitimate
    30-69: Unknown/neutral
    10-29: Suspicious
    0-9: Known malicious

External research:
☐ VirusTotal: virustotal.com
  - Upload hash (not file! Privacy)
  - Check detection ratio
  - Review sandbox behavior
  
☐ Hybrid Analysis: hybrid-analysis.com
  - Behavioral analysis
  - Network connections
  - File modifications
  
☐ Google search: "[filename] [publisher]"
  - Is this a known application?
  - Any security advisories?
  - User reports/forums
```

```
Step 4: File instance analysis
───────────────────────────────
Console → File details → All File Instances

Check:
☐ How many computers have this file?
  - 100+ computers: Likely legitimate
  - 1-2 computers: Investigate further
  
☐ Where is file located?
  - C:\Program Files\: Likely installed app
  - C:\Users\*\Downloads\: Downloaded file
  - C:\Windows\Temp\: Possible malware
  - Unusual location: Suspicious
  
☐ When was it first seen?
  - Known date of software deployment: Matches?
  - Random date: Investigate origin
  
☐ Local approval status:
  - Locally approved during init: Pre-existing file
  - Unapproved everywhere: New file
```

```
Step 5: Process behavior analysis
──────────────────────────────────
If file has executed:
Reports → Events → Filter: File Name = [filename]
  Subtype: Execution monitored (if in Low enforcement)
  
Check associated events:
☐ What other files did it create?
☐ Did it modify system files?
☐ Did it spawn other processes?
☐ Network activity recorded?

Memory scan results (if enabled):
☐ Suspicious memory modifications?
☐ Code injection detected?
☐ Unexpected DLL loads?
```

```
Step 6: Decision matrix
───────────────────────

APPROVE if:
✓ Signed by known publisher
✓ High trust level (>70)
✓ Low threat level (<30)
✓ Legitimate business need
✓ Prevalent in environment
✓ Clean VirusTotal results

BAN if:
✗ Known malware (threat level >70)
✗ Suspicious behavior observed
✗ No legitimate business purpose
✗ Unsigned from unusual location
✗ High VirusTotal detection rate

INVESTIGATE FURTHER if:
? Medium trust/threat levels
? Unknown publisher
? Unusual file location
? Conflicting indicators
? Unable to verify legitimacy
```

### 14.3 Network Connectivity Deep Dive

**Purpose: Diagnose agent communication failures**

```
Layer 1: Physical/Link
──────────────────────
On endpoint:
ping appcontrol.company.com

If fails:
- Check network cable
- Check network adapter enabled
- Check link light on network port
- Verify not in airplane mode (laptops)

If succeeds:
→ Network path exists, continue to Layer 3
```

```
Layer 2: DNS Resolution
────────────────────────
On endpoint:
nslookup appcontrol.company.com

Expected output:
Server:  dc01.company.com
Address:  10.0.1.10

Name:    appcontrol.company.com
Address:  10.0.1.50

If fails:
- Check DNS server configuration: ipconfig /all
- Try alternative DNS: nslookup appcontrol.company.com 8.8.8.8
- Check DNS server reachability: ping <DNS_server>
- Verify DNS record exists (on DNS server)

If succeeds:
→ Name resolution working, continue to Layer 4
```

```
Layer 3: IP Routing
───────────────────
On endpoint:
tracert appcontrol.company.com

Expected: Direct path to server (few hops)

If fails partway:
- Note where tracert stops
- Check routing table: route print
- Check for network segmentation/firewalls
- Verify endpoint in correct VLAN/subnet

If succeeds:
→ Routing works, continue to Layer 4
```

```
Layer 4: Port Connectivity
──────────────────────────
On endpoint:
Test-NetConnection -ComputerName appcontrol.company.com -Port 41002

Expected output:
ComputerName     : appcontrol.company.com
RemoteAddress    : 10.0.1.50
RemotePort       : 41002
InterfaceAlias   : Ethernet
SourceAddress    : 10.0.5.100
TcpTestSucceeded : True

If TcpTestSucceeded: False
- Check firewall on endpoint: Get-NetFirewallRule | Where {$_.Enabled -eq 'True'}
- Check firewall on server: Same command
- Check network firewalls: Contact network team
- Verify server service listening: netstat -ano | findstr 41002

Alternative test (if Test-NetConnection not available):
telnet appcontrol.company.com 41002
(If connects: Port is open)
```

```
Layer 5: SSL/TLS
────────────────
On endpoint:
dascli connect

Expected output:
TLS handshake: Success
Certificate validation: Success

If TLS handshake fails:
- Check SSL/TLS version compatibility
- Verify no SSL inspection interfering
- Check for SSL proxy in network path
- Test with: openssl s_client -connect appcontrol.company.com:41002

If Certificate validation fails:
- Check certificate trust:
  certutil -verify -urlfetch "C:\Program Files (x86)\Bit9\Parity Agent\servercert.cer"
- Check certificate not expired:
  certutil -dump "C:\Program Files (x86)\Bit9\Parity Agent\servercert.cer"
- Import certificate to trusted root:
  certutil -addstore Root "C:\path\to\server.cer"
```

```
Layer 6: Authentication
───────────────────────
If all above succeeds but agent still offline:

Check Communication Key:
On endpoint:
type "C:\Program Files (x86)\Bit9\Parity Agent\parity.config" | findstr CommunicationKey

Compare with server:
Console → Settings → Communication Key

If mismatch:
- Agent has wrong key
- Update key in config file or reinstall agent

Check agent not disabled:
Console → Assets → Computers → Find computer
Agent Status: Check not "Disabled"
```

### 14.4 Performance Profiling

**Purpose: Identify performance bottlenecks**

```
Agent Performance Profiling
───────────────────────────
On affected endpoint:

1. Capture baseline metrics:
   Performance Monitor (perfmon)
   Add counters:
   - Processor: % Processor Time: DasHost
   - Process: Handle Count: DasHost
   - Process: Thread Count: DasHost
   - PhysicalDisk: % Disk Time: C:
   - Memory: Available MBytes

2. Collect for 1 hour during normal operations

3. Analyze results:
   - CPU spikes: When? Doing what?
   - Disk activity: Continuous or sporadic?
   - Memory growth: Stable or increasing?

4. Correlate with agent logs:
   Match timestamps of high resource usage with log entries
   Example:
   15:23:45 - CPU spike to 80%
   DasHost.log: [15:23:45] [INFO] Scanning: E:\LargeDirectory\...
```

```
Server Performance Profiling
─────────────────────────────
On CB App Control Server:

1. SQL Server performance:
   SQL Management Studio → Activity Monitor
   Check:
   - Active connections
   - Long-running queries (>10 seconds)
   - Blocking/deadlocks
   - Database I/O wait times

2. IIS performance:
   IIS Manager → Application Pools → CB App Control
   Check:
   - Request queue length (should be <10)
   - Active requests
   - Memory usage

3. System resources:
   Task Manager → Performance
   - CPU usage (should be <70% average)
   - Memory usage (should be <80%)
   - Disk queue length (should be <2)

4. CB App Control metrics:
   Console → Support → Reports → System Performance
   - Agent backlog statistics
   - Event processing rate
   - File analysis queue
```

```
Network Performance Profiling
──────────────────────────────
Check agent-to-server communication efficiency:

1. Packet capture (Wireshark):
   Filter: tcp.port==41002
   Duration: 15 minutes
   
   Analyze:
   - Retransmissions (should be <1%)
   - Round-trip time (should be <100ms)
   - Connection resets
   - Throughput patterns

2. Agent synchronization timing:
   Agent logs: Look for sync durations
   [INFO] Synchronization started
   [INFO] Sent 145 file hashes
   [INFO] Received 12 policy updates
   [INFO] Synchronization completed (Duration: 2.3 seconds)
   
   Normal: <5 seconds
   Slow: >10 seconds
   Problem: >30 seconds or timeouts

3. Bandwidth utilization:
   netstat -e (check bytes sent/received over time)
   Estimate: ~1-2 KB per file hash sent
   
   Example:
   1,000 new files × 2 KB = 2 MB upload
   Normal for initialization, problem if continuous
```

### 14.5 Using Support Diagnostic Bundle

**Purpose: Collect comprehensive diagnostic data**

```
When to collect:
────────────────
- Before escalating to CB Support
- Complex performance issues
- Intermittent problems (capture during issue)
- Pre/post configuration changes (comparison)
```

```
Server-side collection:
───────────────────────
Console → Support → Generate Support Bundle

Includes:
- Server configuration files
- Database schema and statistics
- IIS logs
- Parity Server logs
- System event logs
- Performance metrics
- Policy configurations
- Rule definitions

Output: support-bundle-YYYY-MM-DD-HHMMSS.zip
Size: Typically 50-200 MB
```

```
Agent-side collection:
──────────────────────
On endpoint:
dascli capture C:\temp\agent-diagnostics.zip

Includes:
- Agent configuration files
- Agent logs (last 7 days)
- Local cache snapshot
- Current policy configuration
- Recent file analysis results
- System information
- Network diagnostics

Output: agent-diagnostics.zip
Size: Typically 10-50 MB
```

```
Analyzing diagnostic bundle:
────────────────────────────
Extract and review:

1. Check log timestamps:
   Verify logs cover the timeframe of the issue

2. Search for errors:
   grep -i "error\|exception\|failed" *.log
   (or use text editor search)

3. Check configuration consistency:
   - Server URL matches between config files
   - Certificates align
   - Policy versions match

4. Review performance metrics:
   - Resource utilization during issue
   - Queue depths
   - Processing times

5. Look for patterns:
   - Recurring errors
   - Specific files/processes
   - Time-based patterns
```

---

## 15. Essential Linux/Windows Skills for Support

### 15.1 Windows Command Line Essentials

**File system navigation:**

```cmd
# Change directory
cd "C:\Program Files (x86)\Bit9\Parity Agent"

# List files
dir
dir /a  (show hidden files)

# View file contents
type filename.txt
more filename.txt  (paginated)

# Search file contents
findstr "error" logfile.txt
findstr /i "connection failed" *.log  (case-insensitive, all logs)

# Copy files
copy source.txt destination.txt
xcopy /s /e folder1 folder2  (copy directories)

# File properties
attrib filename.txt
```

**Service management:**

```cmd
# Query service status
sc query dashost
sc query "Carbon Black App Control Agent"

# Start/stop service
sc start dashost
sc stop dashost

# Configure service
sc config dashost start= auto
sc config dashost start= disabled

# Query all CB-related services
sc query state= all | findstr -i "bit9 parity carbon"
```

**Network troubleshooting:**

```cmd
# Test connectivity
ping appcontrol.company.com
ping -t appcontrol.company.com  (continuous)

# DNS resolution
nslookup appcontrol.company.com
nslookup appcontrol.company.com 8.8.8.8  (use specific DNS)

# Trace route
tracert appcontrol.company.com

# Show network configuration
ipconfig /all

# Show active connections
netstat -ano  (all connections, numerical, process ID)
netstat -ano | findstr 41002  (filter for CB port)

# Test port connectivity (PowerShell)
Test-NetConnection -ComputerName appcontrol.company.com -Port 41002
```

**Process management:**

```cmd
# List processes
tasklist
tasklist | findstr -i dashost

# Kill process
taskkill /IM DasHost.exe /F  (by name, force)
taskkill /PID 1234 /F  (by process ID)

# Process details (PowerShell)
Get-Process DasHost | Format-List *
```

**Registry operations (use with caution):**

```cmd
# Query registry value
reg query "HKLM\SOFTWARE\Bit9\Parity Agent" /v ServerAddress

# Export registry key
reg export "HKLM\SOFTWARE\Bit9\Parity Agent" backup.reg

# Import registry key
reg import backup.reg
```

### 15.2 PowerShell Essentials

**Basic operations:**

```powershell
# Get command help
Get-Help Get-Service -Examples
Get-Command *network*  (find commands)

# Service management
Get-Service dashost
Start-Service dashost
Stop-Service dashost
Restart-Service dashost

# File operations
Get-Content "C:\ProgramData\Bit9\Parity Agent\Logs\DasHost.log" -Tail 50
Get-ChildItem "C:\Program Files (x86)\Bit9" -Recurse  (list all files)
```

**Filtering and searching:**

```powershell
# Search log files
Get-Content DasHost.log | Select-String -Pattern "error|warning"

# Get recent errors
Get-Content DasHost.log | 
    Select-String -Pattern "ERROR" | 
    Select-Object -Last 20

# Search multiple files
Get-ChildItem *.log | Select-String -Pattern "connection failed"
```

**Remote operations:**

```powershell
# Enable PS Remoting (run once on target)
Enable-PSRemoting -Force

# Remote command execution
Invoke-Command -ComputerName DESKTOP-001 -ScriptBlock {
    Get-Service dashost
}

# Remote session
$session = New-PSSession -ComputerName DESKTOP-001
Enter-PSSession $session
# ... run commands ...
Exit-PSSession
```

**Object manipulation:**

```powershell
# Get services and filter
Get-Service | Where-Object {$_.Status -eq "Running"}

# Select specific properties
Get-Process | Select-Object Name, CPU, WorkingSet | Sort-Object CPU -Descending

# Export to CSV
Get-Service | Export-Csv services.csv -NoTypeInformation

# Group and count
Get-Process | Group-Object ProcessName | Sort-Object Count -Descending
```

### 15.3 Linux Command Line Essentials

**File system navigation:**

```bash
# Change directory
cd /opt/bit9

# List files
ls -la  (long format, show hidden)
ls -lh  (human-readable sizes)

# View file contents
cat filename.txt
less filename.txt  (paginated, searchable with /)
tail -f daemon.log  (follow log in real-time)
tail -n 50 daemon.log  (last 50 lines)

# Search file contents
grep "error" logfile.txt
grep -i "connection failed" *.log  (case-insensitive)
grep -r "pattern" /opt/bit9/  (recursive search)
```

**Service management:**

```bash
# SystemD (modern Linux)
systemctl status b9daemon
systemctl start b9daemon
systemctl stop b9daemon
systemctl restart b9daemon
systemctl enable b9daemon  (start at boot)

# SysV init (older Linux)
service b9daemon status
service b9daemon start
/etc/init.d/b9daemon restart
```

**Network troubleshooting:**

```bash
# Test connectivity
ping appcontrol.company.com
ping -c 4 appcontrol.company.com  (send 4 packets)

# DNS resolution
nslookup appcontrol.company.com
dig appcontrol.company.com  (more detailed)

# Show network configuration
ifconfig  (older)
ip addr show  (newer)

# Show active connections
netstat -tupln  (TCP/UDP, process, listening, numerical)
netstat -tupln | grep 41002

# Test port connectivity
telnet appcontrol.company.com 41002
nc -zv appcontrol.company.com 41002  (if netcat installed)
```

**Process management:**

```bash
# List processes
ps aux | grep b9daemon
top  (interactive, refresh every 2 seconds)
htop  (better interface, if installed)

# Kill process
kill 1234  (by PID)
killall b9daemon  (by name)
kill -9 1234  (force kill)

# Process tree
pstree -p | grep b9
```

**File permissions:**

```bash
# View permissions
ls -l filename

# Change permissions
chmod 755 script.sh  (rwxr-xr-x)
chmod +x script.sh  (make executable)

# Change ownership
chown user:group filename
sudo chown root:root /opt/bit9/config
```

**Log analysis:**

```bash
# System logs
tail -f /var/log/syslog  (Debian/Ubuntu)
tail -f /var/log/messages  (RHEL/CentOS)

# CB App Control logs
tail -f /var/log/bit9/b9daemon.log

# Search with context
grep -A 5 -B 5 "error" logfile.txt  (5 lines before/after)

# Count occurrences
grep -c "connection failed" logfile.txt

# Unique entries
grep "blocked" logfile.txt | sort | uniq -c | sort -rn
```

### 15.4 SQL Query Essentials

**Basic queries for troubleshooting:**

```sql
-- Check agent connectivity
SELECT 
    name AS ComputerName,
    online AS IsOnline,
    DATEDIFF(MINUTE, lastPollDate, GETDATE()) AS MinutesSinceLastContact
FROM Computer
WHERE online = 0
ORDER BY lastPollDate ASC

-- Find computers with outdated policies
SELECT 
    c.name AS ComputerName,
    p.name AS PolicyName,
    c.policyStatusDetails AS PolicyStatus
FROM Computer c
JOIN Policy p ON c.policyId = p.id
WHERE c.policyStatusDetails != 'Up to date'

-- Count files by state
SELECT 
    localState,
    COUNT(*) AS FileCount
FROM FileInstance
GROUP BY localState

-- Find recently blocked files
SELECT TOP 100
    timestamp,
    c.name AS ComputerName,
    userName,
    fileName,
    filePath
FROM Event e
JOIN Computer c ON e.computerId = c.id
WHERE subtype = 8  -- Execution blocked
    AND timestamp > DATEADD(HOUR, -24, GETDATE())
ORDER BY timestamp DESC

-- Database size and growth
EXEC sp_spaceused
```

**Performance queries:**

```sql
-- Find slow queries
SELECT TOP 10
    qs.execution_count,
    qs.total_worker_time / qs.execution_count AS AvgCPU,
    qs.total_elapsed_time / qs.execution_count AS AvgDuration,
    SUBSTRING(qt.text, (qs.statement_start_offset/2)+1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(qt.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset)/2) + 1) AS QueryText
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
ORDER BY AvgCPU DESC

-- Check index fragmentation
SELECT 
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.avg_fragmentation_in_percent,
    ips.page_count
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 30
    AND ips.page_count > 1000
ORDER BY ips.avg_fragmentation_in_percent DESC
```

---

## 16. Study Plan and Practice Exercises

### 16.1 Recommended Learning Path

**Week 1-2: Foundation**
```
□ Read sections 1-4 (Philosophy, Architecture, Installation, Policies)
□ Set up lab environment (virtual machines)
□ Install server and agents
□ Create test policies (High, Medium, Low)
□ Practice moving computers between policies
□ Document everything you do

Daily practice: 2-3 hours
Focus: Understanding "why" not just "how"
```

**Week 3-4: File Management**
```
□ Read section 5 (File State Management)
□ Practice all approval methods
□ Test publisher-based approval
□ Create trusted directories
□ Configure trusted users
□ Understand hash-based vs. certificate-based

Exercises:
- Approve 10 different applications using different methods
- Create approval workflow diagram
- Practice explaining each method to imaginary customer
```

**Week 5-6: Custom Rules**
```
□ Read sections 6-7 (Custom Rules, Advanced Rules)
□ Create each rule type
□ Test rule interactions and priorities
□ Build developer workspace scenario
□ Create file integrity controls

Exercises:
- Lab scenarios from student guide
- Create 5 real-world rule scenarios
- Document when to use each rule type

```

**Week 7-8: Event Analysis & Troubleshooting**
```
□ Read sections 8-10 (Events, DASCLI, Troubleshooting Scenarios)
□ Practice using Events page filters
□ Create custom saved views
□ Master DASCLI commands on test agents
□ Work through all troubleshooting scenarios

Exercises:
- Break things intentionally, then fix them
- Create 10 different blocked file scenarios
- Practice explaining root causes
- Time yourself: How fast can you diagnose?

Daily practice: 3-4 hours
Focus: Pattern recognition, systematic approach
```

**Week 9-10: Performance & Integration**
```
□ Read sections 11-12 (Performance, Integration)
□ Set up Active Directory integration
□ Configure SIEM/Syslog output
□ Practice API calls
□ Load test your environment

Exercises:
- Create performance baseline
- Optimize a slow agent
- Write automation scripts
- Build monitoring dashboard
```

**Week 11-12: Advanced Topics & Real-World**
```
□ Read sections 13-15 (Daily Tasks, Advanced Techniques, Skills)
□ Practice daily support workflows
□ Role-play customer scenarios
□ Learn command-line tools deeply
□ Create troubleshooting flowcharts

Exercises:
- 20 mock support tickets (create and solve)
- Teach concepts to someone else
- Create personal KB article collection
- Build troubleshooting cheat sheets
```

### 16.2 Hands-On Lab Exercises

**Lab Environment Setup:**

```
Minimum lab configuration:

1. CB App Control Server (VM)
   - Windows Server 2019
   - 4 vCPU, 16 GB RAM, 100 GB disk
   - SQL Server 2019 Express
   - Static IP: 192.168.1.10
   
2. Windows Client (VM)
   - Windows 10/11
   - 2 vCPU, 4 GB RAM, 60 GB disk
   - IP: DHCP
   
3. Additional test systems (optional)
   - Linux client (Ubuntu/CentOS)
   - Additional Windows servers
   - Domain controller (for AD integration)

Network: All on same subnet, Internet access for reputation lookups
```

**Exercise 1: Policy Enforcement Testing**

```
Objective: Understand enforcement levels through hands-on testing

Setup:
1. Create three policies:
   - Policy A: High enforcement
   - Policy B: Medium enforcement
   - Policy C: Low enforcement

2. Download test applications:
   - 7-Zip installer
   - Notepad++ installer
   - PuTTY executable
   - WinSCP installer

Tasks:
1. Assign Windows client to Policy A (High)
2. Attempt to run each test application
3. Document what happens
4. Take screenshots of notifiers

5. Move client to Policy B (Medium)
6. Attempt same applications
7. Document user experience
8. Test clicking "Allow" vs "Block"

9. Move client to Policy C (Low)
10. Repeat tests
11. Check Events page for each scenario

Questions to answer:
- What's the exact difference in user experience?
- Which events are logged for each enforcement level?
- When does file state change vs. when it doesn't?
- What information appears in the notifier?

Expected time: 2-3 hours
Success criteria: Can explain all three enforcement levels to a customer
```

**Exercise 2: Approval Methods Master Class**

```
Objective: Master all approval methods

Setup:
1. Windows client in High enforcement
2. Five unapproved applications ready

Tasks:

Method 1: Global Approval by Hash
1. Block application A
2. Find in Events page
3. Approve globally via Events
4. Verify on Windows client
5. Check file state in console

Method 2: Local Approval
1. Block application B
2. Approve locally on this computer only
3. Verify file works on this computer
4. Verify still blocked on another computer (if available)

Method 3: Publisher-Based Approval
1. Find signed application C
2. View certificate details (dascli certinfo)
3. Create publisher approval rule
4. Test application C works
5. Test other apps from same publisher

Method 4: Trusted Directory
1. Create C:\ApprovedApps
2. Create Trusted Directory rule
3. Copy application D to this folder
4. Verify auto-approval
5. Check file state changes

Method 5: Trusted User
1. Add your user to Trusted Users
2. Install application E as this user
3. Verify local approval
4. Try installing as different user

Documentation:
Create a decision tree: "Customer needs to approve file → Which method?"

Expected time: 4-5 hours
Success criteria: Can recommend appropriate approval method for any scenario
```

**Exercise 3: Custom Rules Workshop**

```
Objective: Master custom rule creation and troubleshooting

Setup:
1. Windows client in High enforcement
2. Create directory structure:
   C:\Development\
   C:\Production\
   C:\Temp\

Scenario 1: Trusted Path
Task: Allow execution from C:\Development\ only
1. Create Trusted Path rule for C:\Development\*
2. Test file executes from Development
3. Test file blocked from Production
4. Test file blocked from Temp

Challenge: Make it work with double-click AND command line
Solution: Add explorer.exe AND cmd.exe as processes

Scenario 2: File Integrity Control
Task: Protect C:\Production\ from modifications
1. Copy important file to C:\Production\data.txt
2. Create File Integrity Control rule (Block writes)
3. Try to modify file → Should block
4. Try to delete file → Should block
5. Try to rename file → Should block

Challenge: Allow specific application to modify
Solution: Add Process Exclusion for that app

Scenario 3: File Creation Control
Task: Allow only Visual Studio to write to C:\Development\
1. Block all writes to C:\Development\*
2. Create exception for devenv.exe
3. Test: Notepad cannot save → Blocked
4. Test: VS Code cannot save → Blocked
5. Test: Visual Studio can save → Allowed

Scenario 4: Advanced Rule - Full Control
Task: Developers can write AND execute in C:\Development\
1. Create Advanced Rule: Execute + Write
2. Specify C:\Development\*
3. Specify processes: notepad.exe, explorer.exe
4. Test writing new script
5. Test executing new script
6. Verify both work

Troubleshooting practice:
1. Intentionally misconfigure each rule
2. Practice diagnosing why it doesn't work
3. Use dascli showpathpolicies to verify deployment
4. Check Events for rule evaluation

Expected time: 6-8 hours
Success criteria: Can create any custom rule scenario, troubleshoot when it doesn't work
```

**Exercise 4: Troubleshooting Boot Camp**

```
Objective: Build systematic troubleshooting skills

Setup:
1. Functioning lab environment
2. Notepad for documentation

Break and fix exercises:

Scenario 1: "Agent Offline"
1. Intentionally break agent connectivity:
   - Stop dashost service
   OR
   - Change server name in config
   OR
   - Block port 41002 in firewall
2. Attempt to diagnose without looking at what you broke
3. Use only diagnostic commands and console
4. Document troubleshooting steps
5. Time yourself

Scenario 2: "File Blocked Unexpectedly"
1. Create conflicting rules:
   - Global approval for file
   - Policy-specific ban for same file
2. Test which takes precedence
3. Document rule evaluation logic
4. Clear environment and test reverse scenario

Scenario 3: "Rule Not Working"
1. Create rule with common mistakes:
   - Missing wildcard (C:\Apps instead of C:\Apps\*)
   - Wrong policy assignment
   - Rule disabled
   - Process restriction incorrect
2. Try to identify issue from customer perspective
3. Practice explaining to "customer" what's wrong

Scenario 4: "Performance Issue"
1. Generate performance problem:
   - Create 100+ custom rules (use script)
   - Or simulate large file inventory
2. Diagnose using performance tools
3. Optimize and measure improvement

Scenario 5: "Everything Was Working..."
1. Make configuration change
2. Wait 1 hour
3. Try to remember what changed
4. Practice checking Events for changes
5. Document change control importance

Practice script:
"Walk me through your troubleshooting process for [scenario]"
Explain each step out loud as if to a colleague

Expected time: 8-10 hours (1 hour per scenario, repeat multiple times)
Success criteria: Can diagnose problems systematically without guessing
```

**Exercise 5: DASCLI Mastery**

```
Objective: Become fluent with agent command-line tool

Setup:
1. Windows client with agent installed
2. Command prompt as administrator
3. Reference documentation open

Basic commands (practice until muscle memory):
cd "C:\Program Files (x86)\Bit9\Parity Agent"
dascli status
dascli connect
dascli certinfo <file>
dascli showpathpolicies
dascli analyze <file>

Advanced challenges:

Challenge 1: Diagnostic Script
Create batch script that:
- Checks service status
- Tests connectivity
- Outputs status summary
- Saves to text file

Example output:
Service: Running
Connection: Connected
Last Sync: 2 minutes ago
Policy: Up to date
Enforcement: High
Local Files: 15,234

Challenge 2: File Analysis Script
Given a file path, create script that:
- Checks if file is signed (certinfo)
- Shows file state (analyze)
- Displays matching rules (showpathpolicies filtered)
- Outputs recommendation (approve/ban/investigate)

Challenge 3: Health Check Script
Script that runs on schedule:
- Checks agent health
- Logs results
- Emails alert if issues found

Challenge 4: Remote Diagnostics
Using PowerShell remoting:
- Run dascli status on 10 remote computers
- Collect results
- Generate summary report
- Identify computers with issues

Expected time: 4-6 hours
Success criteria: Can use DASCLI commands without reference, write diagnostic scripts
```

**Exercise 6: Event Analysis Skills**

```
Objective: Master event filtering and pattern recognition

Setup:
1. Populated environment (agents running for days/weeks)
2. Various test activities generating events

Tasks:

Task 1: Find the Needle
Using only Events page, find:
- All files blocked on specific computer in last hour
- All files approved by specific admin today
- All policy changes in last week
- All high-threat files in environment
- All approval requests awaiting action

Task 2: Pattern Recognition
Generate events then identify patterns:
1. Push software update to 10 computers
2. Identify in Events: Same file, multiple computers, same time
3. Practice: "This is a software deployment"

4. Move computer to wrong policy
5. Identify in Events: Sudden multiple blocks
6. Practice: "Policy assignment changed"

7. Disconnect agent from network
8. Identify in Events: Connection events
9. Practice: "Network/connectivity issue"

Task 3: Security Investigation
Scenario: Suspicious file detected
1. File blocked on computer
2. Threat level: 85 (high)
3. Publisher: Unknown
4. Using Events and Find Files:
   - When first seen?
   - How many computers have it?
   - Where is it located?
   - What process created it?
   - Any related file activity?

Task 4: Create Saved Views
Create custom saved views for:
- Daily security review (high-threat blocks)
- Software change tracking (new installations)
- Policy compliance (unapproved executions in High enforcement)
- Administrative audit (console access)
- User behavior (by specific user or group)

Task 5: Export and Analyze
1. Export week of blocked file events
2. Open in Excel
3. Create pivot tables:
   - Top 10 blocked files
   - Blocks by computer
   - Blocks by time of day
   - Blocks by policy
4. Identify trends and recommendations

Expected time: 6-8 hours
Success criteria: Can find any event quickly, recognize patterns, build investigative narratives
```

### 16.3 Self-Assessment Checklist

**After completing studies, verify you can:**

```
Foundation Knowledge:
☐ Explain default-deny vs. default-allow models
☐ Describe agent-to-server communication flow
☐ Draw architecture diagram from memory
☐ Explain file state (approved/unapproved/banned)
☐ Describe initialization process
☐ List all ports and their purposes

Policy Management:
☐ Explain High/Medium/Low enforcement differences
☐ Describe connected vs. disconnected enforcement
☐ Create policies for different use cases
☐ Assign policies manually and via AD
☐ Troubleshoot policy assignment issues
☐ Explain policy precedence rules

Approval Methods:
☐ List all approval methods
☐ Recommend appropriate method for any scenario
☐ Approve files five different ways
☐ Explain hash-based vs. certificate-based approval
☐ Configure trusted directories securely
☐ Set up trusted users/groups
☐ Troubleshoot approval not working

Custom Rules:
☐ Create each rule type from scratch
☐ Explain when to use each rule type
☐ Troubleshoot rule conflicts
☐ Understand rule priority/precedence
☐ Use wildcards correctly
☐ Configure process restrictions
☐ Create advanced multi-operation rules

Event Analysis:
☐ Navigate Events page efficiently
☐ Create complex filters
☐ Build custom saved views
☐ Recognize common event patterns
☐ Correlate events for investigation
☐ Export and analyze event data

DASCLI:
☐ Use all basic commands from memory
☐ Interpret dascli status output
☐ Troubleshoot connectivity with dascli connect
☐ Analyze files with dascli certinfo
☐ View deployed rules with showpathpolicies
☐ Understand analyze output
☐ Use advanced commands with CLI password

Troubleshooting:
☐ Systematically diagnose "file blocked" issues
☐ Troubleshoot agent offline scenarios
☐ Debug custom rule problems
☐ Investigate performance issues
☐ Analyze network connectivity problems
☐ Interpret log files
☐ Know when to escalate

Command Line:
☐ Navigate Windows/Linux file systems
☐ Manage services (start/stop/status)
☐ Use network diagnostic tools
☐ Search log files efficiently
☐ Write basic troubleshooting scripts
☐ Use PowerShell for remote diagnostics

Communication:
☐ Explain technical concepts to non-technical users
☐ Write clear KB articles
☐ Document troubleshooting steps
☐ Provide clear recommendations
☐ Escalate effectively with complete information
☐ Manage customer expectations
```

### 16.4 Mock Support Tickets

**Practice with realistic scenarios:**

**Ticket 1:**
```
Subject: Users can't access Excel files
Priority: High
Description: "Since this morning, none of our users can open Excel files. 
They get an error that the file is blocked. This is affecting all departments. 
We need this fixed immediately as we have reports due today."

Customer environment:
- 500 Windows 10 workstations
- Policy: Corporate Workstations (High enforcement)
- No recent changes (according to customer)

Your response should include:
1. Initial questions to ask
2. Troubleshooting steps
3. Root cause analysis
4. Resolution
5. Prevention recommendations
6. Estimated time to resolve
```

**Ticket 2:**
```
Subject: Agent showing offline on critical server
Priority: Critical
Description: "Our primary application server shows offline in the CB console. 
The server is online and functioning normally. We can ping it and RDP to it. 
But CB shows it hasn't communicated in 6 hours."

Server details:
- Windows Server 2019
- Application server (IIS)
- Remote site connected via VPN
- Agent version 8.11.2

Your response should include:
1. Diagnostic commands to run remotely
2. Likely causes (ranked by probability)
3. Step-by-step troubleshooting plan
4. Resolution for each possible cause
```

**Ticket 3:**
```
Subject: Need to approve software for developers only
Priority: Medium
Description: "We have a development team that needs to install various tools 
for their work. We want them to be able to install software on their workstations 
without IT approval, but we still want High enforcement for security. 
Other users should not be able to install anything. How do we set this up?"

Environment:
- 20 developers
- Developer AD group exists: DEV_USERS
- Developer computers in AD OU: OU=Developers
- Current policy: Corporate Workstations (High enforcement)

Your response should include:
1. Recommended approach
2. Step-by-step configuration
3. Testing plan
4. Security considerations
5. Monitoring recommendations
```

**Ticket 4:**
```
Subject: Custom rule not working as expected
Priority: Medium  
Description: "I created a Trusted Path rule for C:\CompanyApps but files still 
get blocked when users try to run them. I've verified the rule is enabled and 
applied to the correct policy. What am I missing?"

Rule configuration (as described by customer):
- Name: CompanyApps Trust
- Type: Trusted Path
- Path: C:\CompanyApps
- Execute Action: Allow and Promote
- Policy: Production Servers
- Status: Enabled

Your response should include:
1. Questions to clarify the issue
2. Common mistakes to check
3. Diagnostic steps
4. Likely root cause
5. Corrected configuration
```

**Ticket 5:**
```
Subject: Performance degradation after agent deployment
Priority: High
Description: "We deployed CB App Control to our file server two days ago. 
Since then, users are experiencing very slow file access. Opening files takes 
30+ seconds. Before the agent, it was instant. The server has plenty of resources 
(CPU at 40%, RAM at 60%) but the disk is constantly at 100% activity."

Server details:
- Windows Server 2019
- File server with 5TB of data (~3 million files)
- 200 simultaneous users
- Agent still shows "Initializing: 45%"

Your response should include:
1. Explanation of what's happening
2. Expected duration
3. Immediate mitigation options
4. Long-term optimization recommendations
5. Prevention for future deployments
```

### 16.5 Additional Resources

**Official Documentation:**
```
Primary sources to bookmark:

1. VMware CB App Control Documentation
   https://docs.vmware.com/en/VMware-Carbon-Black-App-Control/

2. CB App Control Knowledge Base
   Search for known issues, configurations, best practices

3. VMware Community Forums
   Real-world scenarios and solutions from other admins

4. Release Notes
   New features, bug fixes, known issues for each version
```

**Recommended Study Materials:**
```
1. Official Training Courses:
   - CB App Control Administrator (covered in student guide)
   - CB App Control Advanced Administrator
   - CB App Control Architect

2. Video Resources:
   - Product demos
   - Configuration walkthroughs
   - Troubleshooting sessions

3. Lab Guides:
   - Use provided student guides
   - Create your own scenarios
   - Document everything

4. Third-Party Resources:
   - Windows sysinternals tools documentation
   - PowerShell in a Month of Lunches (book)
   - SQL fundamentals courses
```

**Practice Platforms:**
```
1. Home Lab:
   - VirtualBox or VMware Workstation
   - 3-4 VMs minimum
   - Snapshot before/after changes
   - Break things intentionally

2. Cloud Lab:
   - AWS/Azure free tier
   - Deploy CB App Control server
   - Test remote scenarios
   - Clean up to avoid charges

3. Work Environment:
   - Non-production systems
   - Dedicated test policy
   - Controlled testing
   - Document for knowledge sharing
```

---

## 17. Final Exam - Comprehensive Scenarios

**Test your complete understanding:**

### Scenario A: New Deployment Consultation

```
A customer is planning to deploy CB App Control to their environment:
- 5,000 endpoints (3,000 workstations, 2,000 servers)
- Mixed environment: Windows, Linux, macOS
- Active Directory infrastructure
- Must meet compliance requirements (PCI DSS)

They ask you:

1. How should we structure our policies?
   - Provide policy structure
   - Explain enforcement levels for each
   - Justify your recommendations

2. What's the rollout plan?
   - Phased approach
   - Testing strategy
   - Rollback plan

3. How do we handle approvals?
   - Recommended approval methods
   - Workflow design
   - Automation opportunities

4. What performance impact should we expect?
   - During initialization
   - Normal operations
   - Optimization strategies

5. How do we integrate with existing security tools?
   - SIEM integration
   - Ticketing system integration
   - AD integration details

Provide comprehensive answers demonstrating expert knowledge.
```

### Scenario B: Complex Troubleshooting

```
Customer reports:
"Random applications are being blocked across different computers. 
It's not consistent - sometimes the app works, sometimes it doesn't. 
This started yesterday afternoon around 3 PM."

Your investigation reveals:
- 50+ computers affected
- Different applications (Office, browsers, custom apps)
- No pattern by computer, user, or location
- Events show "Execution blocked" then minutes later same file allowed
- Server CPU at 85%
- Database showing long-running queries
- Agent backlog: 25,000 files

Diagnose and resolve:

1. What's the root cause?
2. Why is it intermittent?
3. What's the relationship between symptoms?
4. Step-by-step resolution
5. How to prevent recurrence?

Provide detailed analysis demonstrating systematic troubleshooting.
```

### Scenario C: Security Incident Response

```
Security team alerts you:
"We detected potential malware on workstation DESK-042. 
CB App Control blocked it, but we need detailed information 
for our incident response process."

File details from alert:
- Filename: svchost32.exe
- Path: C:\Users\jdoe\AppData\Local\Temp\
- First seen: 2024-11-16 14:23:15
- Threat level: 95
- Trust level: 5
- Publisher: Unsigned

They need:

1. Complete file analysis
   - Certificate status
   - Reputation information
   - Behavioral indicators
   - Related files/processes

2. Scope assessment
   - Is this file on other computers?
   - Any successful executions?
   - Lateral movement indicators?
   - Patient zero identification

3. Response actions
   - Immediate containment steps
   - Investigation procedures
   - Evidence collection
   - Remediation plan

4. Post-incident
   - Prevention recommendations
   - Policy improvements
   - Monitoring enhancements
   - Documentation requirements

Provide complete incident response demonstrating security expertise.
```

---

## Conclusion

You now have a comprehensive roadmap from zero to expert CB App Control technical support engineer. Remember:

**Key Success Principles:**

1. **Understand "Why" Not Just "How"**
   - Don't memorize commands, understand concepts
   - Know the reasoning behind each feature
   - Explain to others to solidify knowledge

2. **Practice Systematically**
   - Follow the learning path in order
   - Complete all hands-on exercises
   - Don't skip fundamentals
   - Break things and fix them

3. **Document Everything**
   - Keep a personal wiki/notebook
   - Create troubleshooting flowcharts
   - Build KB article collection
   - Document lessons learned

4. **Think Like a Customer**
   - They don't know the product
   - They're under pressure
   - They need clear explanations
   - They want solutions, not excuses

5. **Stay Current**
   - Read release notes
   - Test new features
   - Join community forums
   - Learn from other cases

6. **Build Intuition**
   - Pattern recognition comes with experience
   - Keep mental library of scenarios
   - Trust your troubleshooting process
   - Know when to escalate

**Your Journey:**
- **Weeks 1-4:** Foundation (understand product completely)
- **Weeks 5-8:** Rules and troubleshooting (hands-on mastery)
- **Weeks 9-12:** Advanced topics and real-world practice
- **Ongoing:** Continuous learning and skill refinement

**You're ready when:**
- You can explain any concept to a non-technical person
- You can troubleshoot without documentation
- You recognize patterns instantly
- You know what questions to ask
- You can estimate time to resolution
- You document systematically
- You help others learn
