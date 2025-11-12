# VMware Carbon Black App Control - Complete Beginner's Guide

## Table of Contents
1. [Introduction & Core Concepts](#1-introduction--core-concepts)
2. [Getting Started - First Steps](#2-getting-started---first-steps)
3. [Understanding the Architecture](#3-understanding-the-architecture)
4. [User Management & Access Control](#4-user-management--access-control)
5. [Policies - The Foundation of Control](#5-policies---the-foundation-of-control)
6. [Working with Computers (Endpoints)](#6-working-with-computers-endpoints)
7. [Rules - Approving and Blocking Software](#7-rules---approving-and-blocking-software)
8. [Monitoring Tools - Meters & Alerts](#8-monitoring-tools---meters--alerts)
9. [Events - Understanding What Happened](#9-events---understanding-what-happened)
10. [Baseline Drift Reports](#10-baseline-drift-reports)
11. [Troubleshooting Fundamentals](#11-troubleshooting-fundamentals)

---

## 1. Introduction & Core Concepts

### What is Carbon Black App Control?

Carbon Black App Control is an **application whitelisting solution** that prevents unauthorized software from running on your endpoints (servers, desktops, laptops).

**Think of it like a nightclub bouncer:**
- Only people on the list get in (approved software runs)
- Everyone else is turned away (unapproved software is blocked)
- The bouncer keeps a record of who tried to get in (event logging)

### The "No Trust" Philosophy

The most important concept to understand:

> **EVERYTHING IS BLOCKED BY DEFAULT**

When you install Carbon Black App Control in **High Enforcement** mode:
- No software can run unless it's explicitly approved
- This is the opposite of traditional antivirus (which blocks known bad things)
- App Control says "I only allow known good things"

**Real-world analogy:** 
It's like a castle with the drawbridge up. Nothing gets in unless you specifically lower the bridge for them. Traditional antivirus is like having the drawbridge down and guards who try to spot bad guys coming in.

### Key Terminology

| Term | Simple Explanation | Example |
|------|-------------------|---------|
| **Agent** | Software installed on each computer you want to protect | Like a security guard stationed at each building |
| **Server** | Central management console where you control everything | Like the security headquarters |
| **Policy** | A set of rules that determine how strict protection is | Like different security levels for different buildings |
| **Enforcement Level** | How strict the protection is (High/Medium/Low/None) | High = Maximum security, None = No protection |
| **Approved File** | Software allowed to run | Your ID badge that gets you through security |
| **Unapproved File** | Software not yet approved (blocked in High Enforcement) | No badge = you're not getting in |
| **Banned File** | Software explicitly blocked | You're on the "do not admit" list |

### File States - The Traffic Light System

Think of file states like a traffic light:

```
🟢 APPROVED (Green Light)
   - File can run without issues
   - Example: Microsoft Word approved by IT

🟡 UNAPPROVED (Yellow Light)
   - File is not approved or banned yet
   - In High Enforcement: BLOCKED
   - In Low Enforcement: Allowed but monitored
   - Example: A new game you just downloaded

🔴 BANNED (Red Light)
   - File is explicitly blocked
   - Never allowed to run
   - Example: Known malware or unauthorized software
```

---

## 2. Getting Started - First Steps

### Your Learning Path

```
Step 1: Set up lab environment
   ↓
Step 2: Learn the console basics
   ↓
Step 3: Practice with hands-on labs
   ↓
Step 4: Understand approval methods
   ↓
Step 5: Master troubleshooting
```

### Setting Up Your Lab Environment

**Why you need a lab:**
- Safe place to test without breaking production systems
- Learn by doing, not just reading
- Practice troubleshooting scenarios

**Basic Lab Setup:**
1. **Virtual Machine #1**: Install App Control Server
2. **Virtual Machine #2**: Install App Control Agent (Windows Client)
3. Connect them together

**Important Note:** Starting with version 8.1.4, you must separately download:
- Agent installer
- Rules Installer (includes Rapid Configs)

### First Login to the Console

**Default Credentials (Lab Only):**
```
URL: https://appcontrol/
Username: admin
Password: admin123
```

⚠️ **Security Warning:** In production, immediately change default passwords!

**What you'll see:** A web-based console with several main sections:
- **Assets** (Computers)
- **Rules** (Software control)
- **Reports** (Events and analysis)
- **Tools** (Alerts and meters)
- **Admin** (Settings)

---

## 3. Understanding the Architecture

### The Two Main Components

```
┌─────────────────────────────────────────┐
│     Carbon Black App Control Server      │
│  (Central Management & File Database)    │
└────────────────┬────────────────────────┘
                 │
                 │ Communication
                 │
     ┌───────────┴───────────┐
     │                       │
┌────▼─────┐          ┌─────▼────┐
│  Agent   │          │  Agent   │
│(Desktop) │          │ (Server) │
└──────────┘          └──────────┘
```

### How It Works: The Initialization Process

When you first install an agent on a computer:

**Step 1: Discovery**
```
Agent scans the computer's hard drives
↓
Finds all "interesting files" (executables, scripts, DLLs)
↓
Creates a cryptographic hash (unique fingerprint) of each file
```

**Example:** 
- Agent finds: `C:\Program Files\Microsoft Office\WINWORD.EXE`
- Creates hash: `ABC123DEF456...` (unique identifier)

**Step 2: Reporting**
```
Agent sends file hashes to server
↓
Server updates central file inventory
↓
Server checks if files are known/approved
```

**Step 3: Local Approval During Initialization**
```
All files found during initialization are LOCALLY APPROVED
(This prevents breaking the system on first install)
```

**Important:** Files discovered during initialization get a special status:
- They can run on that specific computer
- But they're not globally approved for other computers

**Example Scenario:**
```
Computer: WORKSTATION-01
During initialization, agent finds:
- Windows OS files → Locally approved
- Microsoft Office → Locally approved
- PuTTY.exe in Downloads folder → Locally approved

Later, you copy PuTTY.exe to Desktop:
- Still runs (same file, same computer)

If you try to run PuTTY on WORKSTATION-02:
- BLOCKED (not locally approved on that computer)
```

---

## 4. User Management & Access Control

### Why Multiple User Roles?

Different people need different levels of access:
- **Full Admin**: Can do everything
- **Power User**: Can manage files/policies but not users
- **Help Desk**: Can view and handle user requests
- **Read Only**: Can only view information

### Lab Exercise 1: Creating User Accounts

**Scenario:** You need to create accounts for your security team:

#### User 1: Security Administrator (Full Access)

```
Purpose: Jamie needs same rights as you
Role: Administrator

Steps:
1. Navigate to: Gear Icon → Login Accounts → Users
2. Click: Add User
3. Enter:
   - Username: jappleton
   - Password: $3cur!tY
   - First name: Jamie
   - Last name: Appleton
   - Title: Security Administrator
4. Select: Administrator User Role
5. Click: Create & Exit
```

**What can Jamie do?**
- ✅ Everything you can do
- ✅ Create/delete users
- ✅ Modify system settings
- ✅ Manage all policies and rules

#### User 2: Assistant Security Administrator (Limited Admin)

```
Purpose: Daryl needs policy management but not system admin
Role: Power User

Steps:
1. Navigate to: Gear Icon → Login Accounts → Users
2. Click: Add User
3. Enter:
   - Username: djohnson
   - Password: $3cur!tY
   - Email: djohnson@localhost.com
   - First name: Daryl
   - Last name: Johnson
   - Title: Assistant Security Administrator
4. Select: Power User User Role
5. Click: Create & Exit
```

**What can Daryl do?**
- ✅ Approve/ban files
- ✅ Manage policies
- ✅ View events and reports
- ❌ Create users
- ❌ Modify system settings

#### User 3: Help Desk Director (Custom Role)

```
Purpose: Jane needs to view info and manage alerts, nothing else
Role: Custom "Help Desk" role

Steps:
1. Create the Role First:
   - Navigate to: Gear Icon → Login Accounts → User Roles
   - Click: Add User Role
   - Copy Settings From: ReadOnly
   - Name: Help Desk
   - Description: User Role for Help Desk
   - Status: Enabled
   
2. Set Permissions:
   ✅ Tools - Manage meters
   ✅ Tools - Manage Alerts
   ❌ Everything else (inherited from ReadOnly)
   
3. Scope: All Current and Future policies
4. Click: Create & Exit

5. Create Jane's Account:
   - Navigate to: Users
   - Username: jridley
   - Password: $3cur!tY
   - Email: jridley@localhost.com
   - First name: Jane
   - Last name: Ridley
   - Title: Help Desk Director
   - Select: Help Desk User Role
   - Click: Create & Exit
```

**What can Jane do?**
- ✅ View all information
- ✅ Create/manage alerts
- ✅ Create/manage meters
- ❌ Approve files
- ❌ Create policies
- ❌ Modify settings

### Testing Access Limits

**Test Daryl's account:**
```
1. Log out of admin
2. Log in as: djohnson / $3cur!tY
3. Try to create a user → FAILS (no permission)
4. Can manage policies → SUCCESS
```

**Test Jane's account:**
```
1. Log out
2. Log in as: jridley / $3cur!tY
3. Gear icon is hidden → Can't access admin settings
4. Tools → Meters/Alerts available → SUCCESS
```

---

## 5. Policies - The Foundation of Control

### What is a Policy?

A policy is like a security blueprint for a group of computers. It defines:
- How strict the security is (enforcement level)
- What happens when connected vs. disconnected from the server
- Which rules apply to these computers

### Enforcement Levels Explained

```
HIGH ENFORCEMENT
└─ Only approved files can run
└─ Unapproved files are BLOCKED
└─ Use for: Critical servers, DMZ, high-security areas

MEDIUM ENFORCEMENT (Not commonly used)
└─ Unapproved files are TRACKED but allowed
└─ Use for: Transition periods

LOW ENFORCEMENT
└─ Everything runs
└─ All activity is monitored and logged
└─ Use for: Software testing, development machines

NONE (DISABLED)
└─ No enforcement, minimal tracking
└─ Use for: Monitoring mode only
```

### Understanding Connected vs. Disconnected

Computers can have **different enforcement levels** depending on their connection status:

**Example: Laptop User**
```
At office (Connected to server):
└─ Enforcement: LOW
└─ Can install new software
└─ All activity monitored

Away from office (Disconnected):
└─ Enforcement: HIGH
└─ Only approved software runs
└─ Protected even offline
```

### Lab Exercise 2: Creating Policies

#### Policy 1: Security IT Endpoints

**Purpose:** Your testing computers need maximum flexibility

```
Scenario: Your security team tests new software daily
Requirements:
- Must run any software (for testing)
- Must track all activity (for rules creation)
- Low security restrictions

Configuration:
1. Navigate to: Rules → Policies
2. Click: Add Policy
3. Settings:
   - Policy name: Security IT Endpoints
   - Description: Policy for Security IT
   - Mode: Control
   - Connected: Low Enforcement
   - Disconnected: Low Enforcement
   - Initial Settings: Template Policy
   - ✅ Track File Changes
4. Click: Save & Exit
```

**What this achieves:**
- ✅ Any software can run
- ✅ All activity is logged
- ✅ Can create rules based on activity
- ⚠️ Low security (only for trusted internal team)

#### Policy 2: Standard Corporate Endpoints

**Purpose:** Balance security with user flexibility

```
Scenario: Office workers with laptops
Requirements:
- Flexible at office
- Locked down when traveling

Configuration:
1. Navigate to: Rules → Policies
2. Click: Add Policy
3. Settings:
   - Policy name: Standard Corporate
   - Mode: Control (default)
   - Connected: Low Enforcement (default)
   - Disconnected: High Enforcement (default)
   - Initial Settings: Default Policy
   - ✅ Track File Changes
4. Click: Save & Exit
```

**Real-world example:**
```
Sarah's Laptop:

At Office:
- Sarah downloads new PDF reader → Runs successfully
- Server tracks installation → Can approve later
- Sarah is productive

Traveling:
- Disconnected from server
- Enforcement automatically switches to HIGH
- Only pre-approved software runs
- Protected from malware on hotel WiFi
```

#### Policy 3: DMZ (Maximum Security)

**Purpose:** Highest security for internet-facing servers

```
Scenario: Web servers in your DMZ
Requirements:
- Maximum protection
- No unauthorized software ever
- Always locked down

Configuration:
1. Navigate to: Rules → Policies
2. Click: Add Policy
3. Settings:
   - Policy name: DMZ
   - Description: Policy for DMZ Sub Network
   - Mode: Control
   - Connected: High Enforcement
   - Disconnected: High Enforcement
   - ✅ Track File Changes
4. Click: Save & Exit
```

**Why HIGH/HIGH?**
- DMZ servers are internet-facing (high risk)
- Should never need new software
- Any unexpected software = potential breach

### Testing Policies

**Moving a computer to a policy:**

```
1. Navigate to: Assets → Computers
2. Select: WORKGROUP\WINDOWS-CLIENT
3. Action → Move Computer to Policy: DMZ
4. Click: OK
5. Wait for: Policy Status = "Up to date"
```

**Test: Try to install unapproved software**

```
On Windows Client:
1. Navigate to Downloads folder
2. Try to run: npp.7.8.5.Installer.exe (Notepad++)

Result: BLOCKED
- Security notification appears
- File cannot execute
- Option to request approval
```

**Creating an approval request:**
```
In the security notification:
1. Enter reason: "Need for code editing"
2. Enter email: admin@localhost.com
3. Click: Submit

Admin sees the request and can:
- Approve (file runs on this computer)
- Deny (file stays blocked)
- Globally approve (file runs everywhere)
```

---

## 6. Working with Computers (Endpoints)

### Computer Details Page

Every computer in App Control has a details page showing:
- Current policy
- Agent version and status
- Files on the computer
- Recent activity
- Health status

### Lab Exercise 3: Managing Computers

#### Task 1: Adding Descriptions and Tags

**Why:** Helps organize and identify computers

```
Purpose: Label computers for easy identification

Steps:
1. Navigate to: Assets → Computers
2. Click: View Details icon (📄) next to WINDOWS-CLIENT
3. Enter:
   - Description: Pilot Deployment Test Endpoint
   - Computer Tag: 02
4. Click: Save

Use cases:
- Tag: Building number, department code, etc.
- Description: Purpose, owner, special notes
```

#### Task 2: Health Checks

**Purpose:** Verify agent is functioning correctly

```
When to use:
- Agent not checking in
- Policy not applying
- Troubleshooting connection issues

Steps:
1. Navigate to: Computer Details (WINDOWS-CLIENT)
2. Under Advanced → Other Actions
3. Select: Run health check
4. Click: Go
5. View results: Related Views → Health Check Events

What it checks:
✅ Agent service running
✅ Communication with server
✅ Policy up to date
✅ File tracking operational
```

**Example Health Check Results:**
```
✅ PASS: Agent service is running
✅ PASS: Can communicate with server
✅ PASS: Policy synchronized
✅ PASS: File cache up to date
```

#### Task 3: Snapshots

**What is a snapshot?**
- A point-in-time inventory of all files on a computer
- Used for baseline drift reporting
- Helps track what changed over time

```
Use case: Compare file state before and after updates

Steps:
1. Navigate to: Computer Details
2. Under Actions → Add Files to Snapshot
3. Name: Windows Client <today's date>
4. Click: Create

What happens:
- Agent catalogs all tracked files
- Creates baseline for comparison
- Available in Reports → Baseline Drift → Snapshots
```

**Example:**
```
Snapshot: Windows Client 2025-01-15
- Total files: 5,234
- Approved: 5,100
- Unapproved: 134

Later comparison shows:
+ Added: 45 new files
- Removed: 3 files
~ Modified: 12 files
```

#### Task 4: Local Approval

**What is local approval?**
- Approves a file to run on ONE specific computer only
- Does NOT approve it globally
- Useful for one-off situations

```
Scenario: User needs special software on their computer only

Steps:
1. User tries to run blocked file
2. Submits approval request
3. Admin navigates to: Tools → Approval Requests
4. Click: View Details
5. Review: Justification, Trust Level, Threat Level
6. Click: Approve File Locally
7. Click: OK
```

**Local vs. Global Approval:**
```
Local Approval:
- File runs on Computer A only
- Still blocked on Computer B
- Use for: One-time exceptions

Global Approval (Software Rule):
- File runs on all computers (or selected policies)
- Permanent approval
- Use for: Standard business software
```

#### Task 5: Temporary Policy Override

**Use case:** User needs to install something urgently, but it's blocked

```
Scenario: IT needs to install software remotely on locked-down computer

The Problem:
- Computer in HIGH enforcement (DMZ policy)
- Need to install Wireshark for network troubleshooting
- Don't want to permanently change policy

The Solution: Temporary Override Code

Steps:
1. Navigate to: Computer Details (WINDOWS-CLIENT)
2. Click: Policy Override (bottom of page)
3. Configure:
   - Temporary Enforcement: Local Approval
   - Enforcement Level Active For: 5 Minutes
   - Code Valid For: 5 Minutes
4. Click: Generate Code
5. Copy the code (example: ABC-123-XYZ-789)

On the endpoint:
6. Navigate to: C:\Program Files (x86)\Bit9\Parity Agent\
7. Run: TimedOverride.exe
8. Paste code
9. Click: OK

Result:
- For 5 minutes, enforcement drops to Local Approval
- User can install software
- After 5 minutes, HIGH enforcement automatically resumes
```

**Security considerations:**
```
✅ Time-limited (automatic re-lock)
✅ Leaves audit trail
✅ Doesn't permanently weaken security
⚠️ Use sparingly
⚠️ Monitor usage via events
```

---

## 7. Rules - Approving and Blocking Software

### Understanding the Rule Types

Think of rules as different types of keys that unlock the door (allow software to run):

```
🔑 SOFTWARE RULES (Simple)
   └─ Approve by file hash, name, or publisher certificate

🔑 CUSTOM RULES (Flexible)
   └─ Approve by path, behavior, or complex conditions

🔑 UPDATORS (Built-in)
   └─ Pre-made rules for common software updates

🔑 RAPID CONFIGS (Pre-packaged)
   └─ Sets of rules for specific scenarios
```

### Software Rules

#### A. File Rules (Hash-Based Approval)

**Most secure method - approves ONE specific file**

```
How it works:
File → Calculate hash → Approve hash → Only exact file runs

Example:
Hash: ABC123DEF456...
File: putty.exe (version 0.74)

Approved: putty.exe with hash ABC123DEF456
Blocked: putty.exe with different hash (different version)
```

**Lab: Creating a Ban Rule**

```
Scenario: Ban PuTTY on DMZ servers

Steps:
1. Navigate to: Rules → Software Rules
2. Click: Add File Rule
3. Configure:
   - Rule Name: Putty Ban
   - Rule Type: Ban
   - File Type: File Name
   - Platform: Windows
   - Filename: putty.exe
   - Description: Ban putty.exe
   - Rule Applies To: Selected policies → DMZ
4. Click: Save & Exit
5. Click: Yes (confirmation)

Test:
1. On Windows Client (in DMZ policy)
2. Try to run: putty.exe
3. Result: Different notifier appears (BAN message, not just block)
```

**Ban vs. Unapproved:**
```
UNAPPROVED (not approved):
- Blocked in HIGH enforcement
- Can be approved later
- Message: "This file is not approved"

BANNED:
- Blocked in ALL enforcement levels
- Cannot run even if policy is LOW
- Message: "This file is banned"
- Use for: Known malware, prohibited software
```

#### B. Publisher Rules (Certificate-Based)

**Approves all software from a trusted publisher**

```
How it works:
Publisher signs software with digital certificate
→ You approve the certificate
→ All software signed by that certificate is approved

Example:
Approve: Mozilla Corporation certificate
Result: Firefox, Thunderbird, all Mozilla software runs
```

**When to use:**
- Software vendor you trust completely
- Regular updates from same vendor
- Want automatic approval of vendor updates

**Risks:**
```
⚠️ If publisher is compromised, all their signed software is approved
⚠️ Publisher may sign software you don't want
⚠️ More complex troubleshooting (certificate chain issues)
```

#### C. Reputation Approvals

**Automatically approve based on file reputation from Carbon Black cloud**

```
Trust Levels:
- 100: Known good (Microsoft, Google, Adobe)
- 50-99: Likely safe
- 1-49: Unknown or questionable
- 0: Known bad

Configuration:
1. Navigate to: Rules → Software Rules → Reputation Approvals
2. Enable reputation-based approval
3. Set minimum trust level (e.g., 80)
4. Select policies to apply

Example:
- File: Notepad++
- Trust Level: 85
- Minimum required: 80
- Result: Automatically approved
```

### Custom Rules

**Most flexible but requires careful configuration**

#### 1. Trusted Path Rule

**Approve all software in a specific folder**

```
Use case: Dedicated software installation folder

Lab Exercise:
Scenario: Create C:\Repository for approved software installs

Steps:
1. On Windows Client, create: C:\Repository
2. Navigate to: Rules → Software Rules → Custom
3. Click: Add Custom Rule
4. Configure:
   - Rule Name: Software Repository
   - Description: Repository to run software
   - Status: Enabled
   - Platform: Windows
   - Rule Type: Trusted Path
   - Execute Action: Allow and Promote
   - Path: C:\Repository\*
   - Rule Applies To: Selected policies → DMZ
5. Click: Save & Exit

Test:
1. Try to run WinSCP installer from Downloads → BLOCKED
2. Copy WinSCP installer to C:\Repository
3. Try to run from C:\Repository → SUCCESS

Why it works:
- File in trusted path = allowed
- File outside trusted path = blocked
- * (asterisk) = all files in folder
```

**Security consideration:**
```
⚠️ Anyone who can write to C:\Repository can run software
⚠️ Protect folder permissions carefully
⚠️ Better than approving individual hashes for installers
```

#### 2. File Integrity Control

**Protect critical files from being modified**

```
Use case: Prevent tampering with POS transaction files

Lab Exercise:
Scenario: Protect transaction files but allow POS software to update them

File location: C:\Program Files\POSsystem\Transactions\
POS software: C:\Program Files\POSsystem.exe

Steps:
1. Navigate to: Rules → Software Rules → Custom
2. Click: Add Custom Rule
3. Configure:
   - Rule Name: Block Writes to Transaction Files
   - Description: Rule to Block Writes to Transaction Files
   - Status: Enabled
   - Platform: Windows
   - Rule Type: File Integrity Control
   - Write Action: Block
   - Path: C:\Program Files\POSsystem\Transactions\*
   - Process Exclusion: *\Program Files\POSsystem.exe
   - Rule Applies To: Selected policies → DMZ
4. Click: Save & Exit

Test - Unauthorized Changes:
1. Navigate to: C:\Program Files\POSsystem\Transactions\
2. Try to delete first.txt → BLOCKED
3. Try to copy second.txt → BLOCKED
4. Try to edit third.txt and save → BLOCKED

Test - Authorized Changes:
5. Run POSsystem.exe
6. Let it modify transaction files → ALLOWED

Why it works:
- Default: All write actions to protected folder are blocked
- Exception: POSsystem.exe can write (process exclusion)
- Protects against: Ransomware, tampering, accidental deletion
```

**Understanding the asterisk (*):**
```
Path: C:\Program Files\POSsystem\Transactions\*

* means: Match all files in this folder

Examples:
✅ Matches: first.txt
✅ Matches: second.txt
✅ Matches: transaction_20250115.dat
✅ Matches: ANY file in Transactions folder

Process Exclusion: *\Program Files\POSsystem.exe

* at beginning means: From any drive letter

Examples:
✅ Matches: C:\Program Files\POSsystem.exe
✅ Matches: D:\Program Files\POSsystem.exe
```

#### 3. Execution Control

**Control what can execute with fine-grained rules**

```
Use cases:
- Approve installers by name pattern
- Block executables in temp folders
- Allow specific versions only

Example Rule:
- Name: Allow IT Tools
- Type: Execution Control
- Action: Allow and Promote
- Path: C:\ITTools\*
- File Pattern: *.exe
```

### Rule Ranking and Precedence

**Important:** When multiple rules apply, rank determines which executes first

```
Rule Evaluation Order:
1. Rank 1 (highest priority)
2. Rank 2
3. Rank 3
...
n. Rank n (lowest priority)

First matching rule wins!

Example Conflict:
Rule 1 (Rank 1): BAN putty.exe
Rule 2 (Rank 2): APPROVE all files in C:\Tools\

If putty.exe is in C:\Tools\:
→ Rule 1 executes first
→ File is BANNED
→ Rule 2 never evaluates

To fix: Change ranks or adjust rule conditions
```

---

## 8. Monitoring Tools - Meters & Alerts

### Meters: Usage Tracking

**What are meters?**
- Track how many times specific files execute
- Monitor who runs them and where
- Help identify software usage patterns

```
Use cases:
✓ License compliance (is expensive software actually used?)
✓ Identify unauthorized software spreading
✓ Track critical application usage
✓ Justify software purchases
```

**Lab: Creating a Meter**

```
Scenario: Track usage of MS Paint

Steps:
1. Navigate to: Tools → Meters
2. Click: Add Meter
3. Configure:
   - Meter Name: MS Paint
   - Type: File Name
   - Platform: Windows
   - Filename: mspaint.exe
   - Description: Meter for MS Paint
4. Click: Save

Test:
1. On Windows Client, open MS Paint
2. Make an edit and save file
3. Close Paint
4. Navigate to: Tools → Meters
5. Click: Refresh Page
6. View: Executions (count), Computers (which ones), Users (who)

Example Results:
- Executions: 1
- Computers: WORKGROUP\WINDOWS-CLIENT
- Users: Administrator
- Last Execution: 2025-01-15 14:30:22
```

**Real-world example:**
```
Company has 100 Adobe Photoshop licenses ($50,000)

Create meter for photoshop.exe

After 3 months:
- 15 users actively use it
- 85 licenses unused
- Potential savings: $42,500

Action: Reclaim 85 licenses, reassign to users who need them
```

### Alerts: Automated Notifications

**What are alerts?**
- Automated warnings when specific events occur
- Can notify via email or console
- Help catch security issues quickly

```
Alert Types:
1. File Activity: New installations, blocked files, banned files
2. System Events: Agent disconnected, policy changes
3. Threshold: File executed more than X times
4. Custom: Complex conditions
```

**Lab: Creating Installation Alert**

```
Scenario: Get notified when WinSCP is installed

Steps:
1. Navigate to: Reports → Events
2. Saved Views: New Installations
3. Group By: File Name
4. Max Age: 1 day
5. Find: winscp-5.17.3-setup.exe
6. Click: Plus icon (+) next to the file
7. Click: link under Description/File Name/File Hash
8. Under Actions → Add Alert

Alert auto-configures:
- Type: File Activity: Installation
- File: winscp-5.17.3-setup.exe (hash)
- Threshold: 10 (default)

Customize:
9. Change Threshold: 10 → 1
   (Alert on first installation, not tenth)
10. Priority: Medium
11. Click: Create & Exit

Test:
12. Install WinSCP on another computer
13. Navigate to: Console (top of page)
14. Click: Medium Priority Alert icon (🔔)
15. View: Alert details

Results shown:
- When: Timestamp of installation
- Where: Computer name
- Who: User account
- What: File details
```

**Lab: Creating Blocked File Alert**

```
Scenario: Track attempts to run banned PuTTY

Steps:
1. Verify: Windows Client in DMZ policy (HIGH enforcement)
2. Navigate to: Tools → Alerts
3. Click: Add Alert
4. Configure:
   - Alert Name: putty.exe Blocked
   - Message: PuTTy is blocked
   - Priority: High
   - Status: Enabled
   - Type: File Activity: Blocked File
   - Criteria: Exact Number = 1
   - Policies: Selected Policies → DMZ
5. Click: Create & Exit

Test:
6. On Windows Client, try to run putty.exe
7. Wait for ban notifier
8. Navigate to: App Control console
9. Click: High Priority Alert icon (🔔)
10. View: Alert instance details

What you see:
- RED colored alert (high priority)
- Computer that attempted execution
- User who tried to run it
- Timestamp of attempt
- File details
```

**Alert Priority Colors:**
```
🔴 HIGH (Red): Critical security events
🟡 MEDIUM (Yellow): Important events
🔵 LOW (Blue): Informational
⚪ INFO (White): General tracking
```

**Best practices:**
```
✓ Use HIGH for security violations
✓ Use MEDIUM for compliance issues
✓ Use LOW for usage tracking
✓ Don't create too many alerts (alert fatigue)
✓ Test alerts before deploying widely
✓ Configure email notifications for critical alerts
```

---

## 9. Events - Understanding What Happened

### What Are Events?

Events are the **audit trail** of everything that happens in your App Control environment:

```
Events capture:
✓ File executions (what ran)
✓ Blocked attempts (what was prevented)
✓ Installations (what was installed)
✓ Policy changes (what was modified)
✓ Approvals (what was allowed)
✓ User actions (who did what)
```

**Think of events like security camera footage:**
- Always recording
- Can review past events
- Filter to find specific incidents

### Lab Exercise 6: Working with Events

#### Task 1: Using Saved Views and Filters

**Saved Views** are pre-configured event filters for common scenarios.

```
Common Saved Views:
- Blocked Files (All): Files that were prevented from running
- New Installations: Recently installed software
- Console Access: Who logged into the console
- Policy Changes: Modifications to policies
- File Approvals: Files that were approved
```

**Lab: Finding Blocked Files**

```
Scenario: User tried to install Slack, was blocked, needs approval

Steps:
1. On Windows Client: Try to run SlackSetup.exe → BLOCKED
2. Navigate to: Reports → Events
3. Saved Views → Blocked Files (All)
4. Group By: File Name
5. Max Age: 1 day
6. Click: Show Filters

Observe:
- Filters already selected for "Blocked Files" view
- Type: Execution blocked
- Subtype: Various block reasons

7. Add Additional Filter:
   - Add Filter → File Name
   - File Name: Contains
   - Text: Slack
8. Click: Apply

Results:
- See all SlackSetup.exe block events
- Computer: WORKGROUP\WINDOWS-CLIENT
- User: Administrator
- Timestamp: When block occurred
- Reason: Unapproved file (DMZ policy, High enforcement)
```

**Approving from Events:**

```
Method 1: Quick Approval
9. Next to File Name (slacksetup.exe), click: Plus icon (+)
10. This automatically approves the file locally

Method 2: Detailed Approval
9. Click: Link under File Name
10. View: File Instance Details
11. Under Actions → Choose approval method:
    - Approve Locally (this computer only)
    - Approve Globally (all computers)
    - Create Rule (for specific policies)
```

#### Task 2: Removing Local Approval

**Scenario:** Made a mistake, need to revoke approval

```
Why remove approval:
✓ Wrong file approved by accident
✓ Security concerns discovered
✓ Software no longer needed
✓ Testing approval/block cycle

Steps:
1. Log out of admin account
2. Log in as: djohnson / $3cur!tY
   (Testing Power User permissions)
3. Navigate to: Reports → Events
4. Saved Views: Console Access

Observe Console Access Events:
- Type: Console access
- Subtype: Console login / Console logout
- Shows: Who logged in and when

5. Saved Views: (none) to clear current filter
6. Click: Show Filters
7. Add Filter → Subtype
8. Subtype: File local approval
9. Click: Apply

View Results:
- All local approval events
- Shows: When files were approved, by whom, which computer

10. Select checkbox: slacksetup.exe event
11. Action dropdown → Remove Local Approval
12. Click: OK

Result:
- Slack approval revoked
- If user tries to run Slack → BLOCKED again
- Event logged: "Local approval removed"
```

**What happened behind the scenes:**
```
Before:
File: slacksetup.exe
Status: Locally Approved (WINDOWS-CLIENT)
Can run: Yes

After:
File: slacksetup.exe
Status: Unapproved
Can run: No (on HIGH enforcement)

Audit trail preserved:
- Event: Locally approved (timestamp, user)
- Event: Local approval removed (timestamp, user)
```

#### Task 3: Complex Filtering

**Finding specific security events**

```
Scenario 1: Who created policies recently?

Steps:
1. Navigate to: Reports → Events
2. Click: Reset (clear all filters)
3. Show Filters
4. Add Filter → Subtype
5. Subtype: Policy created
6. Max Age: 1 day
7. Click: Apply

Results show:
- All policies created today
- Who created them
- When they were created
```

```
Scenario 2: Track alert creation

Steps:
1. Reset filters
2. Show Filters
3. Add Filter → Subtype
4. Subtype: Alert created
5. Click: Apply

Results show:
- All alerts created
- Who created them
- Alert names and types
```

```
Scenario 3: Multiple conditions (Advanced)

Find: Alerts OR Meters created today

Steps:
1. Reset filters
2. Show Filters
3. Add Filter → Subtype: Alert created
4. Click: Plus icon (+) next to Subtype line
   (This adds another Subtype filter with OR logic)
5. Second Subtype: Meter created
6. Max Age: 1 day
7. Click: Apply

Results show:
- All alerts created today
- AND all meters created today
- Combined in one view
```

### Understanding Event Details

**When you click into an event, you see:**

```
Example: Blocked Execution Event

General Information:
- Event Type: Execution blocked
- Subtype: Unapproved file
- Computer: WORKGROUP\WINDOWS-CLIENT
- User: Administrator
- Timestamp: 2025-01-15 14:30:22

File Information:
- File Name: putty.exe
- File Hash: ABC123...
- File Path: C:\Users\Administrator\Downloads\putty.exe
- Trust Level: 85
- Threat Level: 0
- Publisher: Simon Tatham

Policy Information:
- Policy: DMZ
- Enforcement: High
- Why Blocked: Unapproved + High enforcement

Actions Available:
→ Approve Locally
→ Approve Globally
→ Add to Ban
→ Create Rule
→ View Computer Details
→ View File Details
```

### Why Events Matter for Troubleshooting

**Customer calls: "Software X won't run!"**

**Your investigation process:**

```
Step 1: Find the block event
- Reports → Events
- Saved Views → Blocked Files (All)
- Filter by: Computer name or File name
- Find: The specific block event

Step 2: Analyze WHY it was blocked
Check event details:
✓ What policy is applied?
✓ What enforcement level?
✓ Is file Unapproved or Banned?
✓ Are there conflicting rules?

Step 3: Determine appropriate solution
- Unapproved file → Approve it (if safe)
- Banned file → Understand why banned first
- Wrong policy → Move computer to correct policy
- Bug in rule → Fix rule configuration
```

**Example troubleshooting:**

```
Customer: "Excel won't open documents!"

Events investigation:
1. Filter: Computer = CUSTOMER-PC
2. Filter: File Name contains "excel"
3. Find event: "File write blocked"
4. Details: 
   - Custom rule "File Integrity Control"
   - Path: C:\Users\*\Documents\*
   - Action: Block writes
   - Why: Overly restrictive rule

Solution:
- Rule was meant to protect system files
- Accidentally included Documents folder
- Fix: Update rule path to exclude Documents
- Test: Excel can now save files
```

---

## 10. Baseline Drift Reports

### What is Baseline Drift?

**Baseline Drift** tracks changes to files on your computers over time.

```
Analogy: Like a "before and after" photo

Before (Baseline):
- 5,234 files on computer
- All known and approved

After (Current State):
- 5,289 files on computer
- What's different? That's the "drift"

Drift = Changes from baseline:
+ 55 files added
- 0 files removed
~ 12 files modified
```

### Why Baseline Drift Matters

```
Security Benefits:
✓ Detect unauthorized software installations
✓ Identify malware that was installed
✓ Track changes after maintenance windows
✓ Verify patch deployments
✓ Compliance reporting (prove nothing changed)

Example Use Case:
Server should never change (no updates scheduled)
→ Baseline shows 100 files
→ Weekly check shows 101 files
→ New file: cryptominer.exe
→ Security incident detected!
```

### Creating Snapshots

**Snapshots** are the "before" picture used in baseline drift reports.

```
When to create snapshots:
✓ After fresh OS installation
✓ After major updates
✓ Before maintenance windows
✓ Monthly for regular baseline
✓ After software deployments
```

**We already created snapshots in Lab 3:**

```
Recap from earlier:
1. Computer Details → Add Files to Snapshot
2. Created:
   - "App Control Server <date>"
   - "Windows Client <date>"

What the snapshot contains:
- Complete file inventory
- File hashes
- File paths
- Approval states
- Threat/trust levels
```

### Lab Exercise 7: Baseline Drift Analysis

#### Task 1: Analyze Preconfigured Report

**The "Daily Drift of All Computers" report comes pre-configured**

```
Scenario: Review what changed on all computers in last 5 hours

Steps:
1. Navigate to: Reports → Baseline Drift
2. Find: Daily Drift of all computers report
3. Click: View Details icon (📄)

Current Configuration:
- Status: Enabled
- Target Type: All Computers
- Baseline Type: Same as Target
- Time Range: From 5 Hours ago
- Filter Options:
  ✅ Include approved files
  ✅ Include banned files
- Report Size: Full Details

4. Click: Save
5. Click: Refresh Page (until Status = Available)
6. Click: View Report Results icon (📄)
7. At top of page, click: View Mode

Analyze Results:
```

**What you see in the report:**

```
Example Output:

Computer: WORKGROUP\WINDOWS-CLIENT
Policy: DMZ
Total Changes: 23 files

Added Files:
+ notepad++.exe (Trust: 95, Threat: 0, Approved)
+ wireshark.exe (Trust: 90, Threat: 0, Locally Approved)
+ slack.exe (Trust: 85, Threat: 0, Unapproved)

Modified Files:
~ hosts (Trust: -, Threat: 0, System File)
~ registry.dat (Trust: -, Threat: 0, System File)

Removed Files:
- old_installer.exe (Trust: 70, Threat: 0, Was Approved)

Key Observations:
- Most new files have HIGH trust (85-95)
- Threat levels are 0 (not malicious)
- Mix of approved and unapproved states
```

**Questions to ask when reviewing:**

```
For each changed file:
1. Was this change expected?
   ✅ Notepad++ → IT installed it (expected)
   ❌ slack.exe → User installed without approval (unexpected)

2. Is the trust level acceptable?
   ✅ Trust 85-100 → Generally safe
   ⚠️ Trust 50-84 → Review carefully
   ❌ Trust 0-49 → Investigate immediately

3. What's the threat level?
   ✅ Threat 0-20 → Not malicious
   ⚠️ Threat 21-50 → Potentially unwanted
   ❌ Threat 51-100 → Likely malicious

4. Should this file be approved/banned?
   → Approved: Legitimate business software
   → Banned: Unauthorized or malicious
   → Monitor: Uncertain, watch for now
```

#### Task 2: Create Custom Baseline Drift Reports

**Create targeted reports for specific computers**

**Report 1: App Control Server Changes**

```
Scenario: Track changes on the App Control Server itself

Why: Servers should rarely change, any drift is suspicious

Steps:
1. Navigate to: Reports → Baseline Drift
2. Click: Add Report
3. Configure:
   - Report Name: App Control Server
   - Status: Enabled
   - Target Type: Advanced Filter
   
4. Set Filter:
   - Add filter → Computer
   - Computer: is
   - Text: WORKGROUP\APPCONTROL

5. Baseline Configuration:
   - Baseline Type: Snapshots
   - Available → Selected:
     Move "App Control Server <date>" to Selected
   
6. Show Advanced Options:
   - ✅ Include approved files
   - ✅ Include banned files
   - Report Size: Full Details

7. Click: Save
```

**Report 2: Windows Client Changes**

```
Steps:
1. Click: Add Report
2. Configure:
   - Report Name: Windows Client
   - Status: Enabled
   - Target Type: Advanced Filter
   
3. Set Filter:
   - Add filter → Computer
   - Computer: is
   - Text: WORKGROUP\WINDOWS-CLIENT

4. Baseline Configuration:
   - Baseline Type: Snapshots
   - Available → Selected:
     Move "Windows Client <date>" to Selected
   
5. Show Advanced Options:
   - ✅ Include approved files
   - ✅ Include banned files
   - Report Size: Full Details

6. Click: Save
```

**What these reports tell you:**

```
Comparing against snapshots:

Snapshot Date: 2025-01-15 09:00 AM
Report Run: 2025-01-15 03:00 PM

Changes in 6 hours:
+ 15 files added
- 2 files removed
~ 8 files modified

Example Investigation:

Added: malicious.exe
- Trust: 0 (very low!)
- Threat: 85 (very high!)
- Path: C:\Users\Public\malicious.exe
- First seen: 2025-01-15 14:30 PM

Action: 
→ This is suspicious!
→ Check events at 14:30 PM
→ What process created this file?
→ Ban immediately
→ Investigate security breach
```

### Baseline Drift Best Practices

```
✓ Create snapshots regularly (weekly/monthly)
✓ Review drift reports after any changes
✓ Investigate low trust/high threat files immediately
✓ Use drift reports for compliance audits
✓ Compare before/after major deployments
✓ Train teams to recognize normal vs. suspicious drift

Regular Schedule Example:
- Monday: Create weekly snapshot
- Tuesday-Sunday: Monitor daily drift
- Monthly: Full baseline comparison
- Quarterly: Compliance report
```

---

## 11. Troubleshooting Fundamentals

### The Core Troubleshooting Mindset

**The #1 rule of App Control troubleshooting:**

> Everything is blocked by default. The question is never "why is it blocked?" 
> The real question is: "Why isn't my approval working?"

```
Customer: "Why is this file blocked?"
Wrong answer: "Because it's unapproved"
Right answer: "Let me check why your approval isn't working"
```

### Common Troubleshooting Scenarios

#### Scenario 1: File Won't Run (Most Common)

**Customer:** "I approved this file but it still won't run!"

**Your diagnostic process:**

```
Step 1: Verify the block
- Navigate to: Reports → Events
- Filter: Blocked Files (All)
- Find: The specific file and computer
- Confirm: Yes, it's actually blocked

Step 2: Check the file state
- Click: File name link in event
- View: File Instance Details
- Check: Approval status

Possible states you'll see:
✅ Globally Approved → Should run everywhere
✅ Locally Approved → Should run on that computer only
⚠️ Unapproved → Blocked in HIGH enforcement
❌ Banned → Blocked everywhere
```

**Example Investigation:**

```
File: notepad++.exe
Computer: WORKGROUP\SALES-PC
Policy: DMZ (High Enforcement)
Event: Execution blocked

Checking file details:
- Hash: ABC123DEF456...
- Approval Status: Locally Approved
- Approved Computer: WORKGROUP\IT-PC (different computer!)

The Problem:
✗ File is locally approved on IT-PC
✗ User is on SALES-PC
✗ Local approval doesn't transfer between computers

The Solution:
1. Option A: Approve globally (if appropriate)
2. Option B: Locally approve on SALES-PC
3. Option C: Create software rule for DMZ policy
```

#### Scenario 2: File Hash Mismatch

**Customer:** "I approved this file yesterday, now it's blocked again!"

```
What happened:
Yesterday: 
- File: program.exe (hash: ABC123)
- Status: Approved

Today:
- File updated/patched
- New file: program.exe (hash: XYZ789)
- Status: Unapproved (different hash!)

App Control behavior:
- Approval is tied to specific hash
- Different version = different hash = different file
- New version needs new approval
```

**The fix:**

```
Solution 1: Approve the new version
- Find new hash in events
- Approve new version

Solution 2: Use Publisher approval instead
- Approve the publisher certificate
- All versions from publisher are approved
- Automatic for future updates

Solution 3: Use Custom Rule (Trusted Path)
- Create rule for installation folder
- All files in folder are approved
- Be careful with folder permissions!

Best Practice:
- Hash approval: Most secure, requires re-approval for updates
- Publisher approval: Easier maintenance, slight security tradeoff
- Path approval: Easiest, highest risk (folder must be protected)
```

#### Scenario 3: Custom Rule Not Working

**Customer:** "I created a Trusted Path rule but files are still blocked!"

**Common mistakes:**

```
Mistake 1: Path syntax error
Wrong: C:\Repository
Right: C:\Repository\*

The asterisk (*) is critical!
- Without *: Only the folder itself (useless)
- With *: All files in the folder (what you want)

Mistake 2: Wrong policy applied
- Rule applies to: Standard Corporate
- Computer is in: DMZ policy
- Rule doesn't apply to that computer!

Check: Rules → Software Rules → Custom
- View rule details
- Check "Rule Applies To" section
- Verify computer's policy is listed

Mistake 3: Rule not enabled
- Status: Disabled
- Rule won't execute even if everything else is right

Fix: Edit rule, Status → Enabled

Mistake 4: Rule rank too low
- Rule 1 (Rank 1): BAN *.exe in Repository
- Rule 2 (Rank 10): ALLOW *.exe in Repository
- BAN executes first, ALLOW never runs

Fix: Change rank so ALLOW is higher priority (lower rank number)
```

**Diagnostic steps:**

```
1. Verify the rule exists:
   - Navigate to: Rules → Software Rules → Custom
   - Find: Your rule in the list
   - Status: Should be "Enabled"

2. Check rule configuration:
   - Click: View Details
   - Path: Verify syntax (C:\Repository\*)
   - Action: Should be "Allow" or "Allow and Promote"
   - Policies: Verify computer's policy is selected

3. Check computer policy:
   - Navigate to: Assets → Computers
   - Find: Customer's computer
   - Policy column: Note the policy name
   - Does it match rule's "Applies To"?

4. Test with events:
   - Have customer try to run file again
   - Reports → Events → Blocked Files
   - Find the new block event
   - Details will show:
     → Which rules were evaluated
     → Why the file was blocked
     → If custom rule was considered

5. Check rule rank:
   - If multiple rules could apply
   - Lower rank number = higher priority
   - Adjust if needed
```

#### Scenario 4: Policy Not Applying

**Customer:** "I moved the computer to a new policy but nothing changed!"

```
Check Policy Status:
1. Navigate to: Assets → Computers
2. Find: Computer
3. Look at: Policy Status column

Possible statuses:
✅ Up to date: Policy applied successfully
⏳ Pending: Policy change queued, waiting for agent
⚠️ Out of sync: Agent hasn't checked in recently
❌ Error: Problem applying policy

If "Pending" or "Out of sync":
- Agent might be offline
- Network connectivity issue
- Agent service might be stopped

Troubleshooting steps:
1. On the endpoint computer:
   - Check: Is agent service running?
   - Windows: Services.msc → Parity Agent
   - Should be: Running, Automatic startup

2. Check connectivity:
   - Can computer reach server?
   - Ping: appcontrol.domain.com
   - HTTP: https://appcontrol/

3. Force agent check-in:
   - Computer Details → Other Actions
   - Select: Run health check
   - This forces immediate communication

4. Review agent logs:
   - Computer: C:\Program Files (x86)\Bit9\Parity Agent\Logs\
   - Check for errors
   - Common issues: Certificate problems, network timeouts
```

#### Scenario 5: PuTTY Mystery (From Lab 2)

**Customer:** "PuTTY runs even though I'm in HIGH enforcement and it's not approved!"

**This is from the lab - let's solve the mystery:**

```
The Question:
- Computer: WINDOWS-CLIENT
- Policy: DMZ (High Enforcement)
- File: putty.exe
- Status: Should be blocked...but it runs!
- Why?

Investigation:
1. Navigate to: Computer Details (WINDOWS-CLIENT)
2. Related Views → Files on this Computer
3. Add filter → File Name: is → putty.exe
4. Click: Apply
5. Click: View Details (📄) for putty.exe

What you discover:
- General Information shows:
  - First Seen Name: putty.exe
  - First Seen Date: 2025-01-14 (during initialization)
  - First Seen Path: C:\Program Files\PuTTY\putty.exe
  
- File Approval:
  - Status: Locally Approved
  - Why: Discovered during initialization

- File History:
  - Event: Discovery during initialization
  - Result: Automatically locally approved

6. Related Views → All File Instances
7. Observe:
   - Instance 1: C:\Program Files\PuTTY\putty.exe (original)
   - Instance 2: C:\Users\Admin\Downloads\putty.exe (copy)

The Answer:
→ During agent initialization, putty.exe existed in Program Files
→ Initialization process automatically locally approves all existing files
→ This prevents breaking the system on first install
→ When you copy putty.exe to Downloads, it's the SAME file (same hash)
→ Local approval follows the file
→ That's why it runs even in HIGH enforcement!

Key Learning:
- Files found during initialization are special
- They get local approval automatically
- This approval follows the file (same hash)
- BUT: Only on that specific computer
- On other computers: Would be blocked
```

### Troubleshooting Workflow Chart

```
Customer reports: "File won't run"
           ↓
    Find block event
    (Reports → Events)
           ↓
    Check file details
           ↓
   ┌────────┴────────┐
   │                 │
Unapproved        Banned
   │                 │
   ↓                 ↓
Check policy    Why banned?
enforcement     Check ban rule
   │                 │
   ↓                 ↓
High?           Legitimate ban?
   │                 │
Yes│  No         Yes│  No
   │  │            │  │
   ↓  ↓            ↓  ↓
Approve → OK   Keep ban → Remove ban
   
Additional checks:
→ Is computer in correct policy?
→ Are custom rules configured correctly?
→ Is agent online and synced?
→ Are there conflicting rules?
```

### Understanding "Why" vs "Why Not"

**The fundamental shift in thinking:**

```
Traditional Support:
Customer: "Why is X happening?"
You: Explain why X happens

App Control Support:
Customer: "Why is X blocked?"
You: "That's the default. Let me find out why your approval isn't working."

The psychology:
- Don't defend the block
- The block is normal and expected
- Focus on fixing the approval mechanism
- This shifts from "App Control is broken" to "Let's configure it correctly"
```

### Most Important Troubleshooting Tools

```
1. Events (Reports → Events)
   - Your #1 tool
   - Shows exactly what happened
   - Filter by computer, file, user, time
   - Practice filtering efficiently

2. File Details
   - Click any file link in events
   - Shows approval status, trust, threat
   - Shows all instances across computers
   - Shows file history

3. Computer Details
   - Shows current policy and status
   - Shows files on that computer
   - Shows recent events for that computer
   - Health check capability

4. Rule Details
   - Check configuration
   - Check which policies it applies to
   - Check rank and precedence
   - Verify enabled status

5. Your Lab Environment
   - Practice reproducing issues
   - Test solutions safely
   - Understand normal vs abnormal behavior
   - Build troubleshooting muscle memory
```

---

## Practice Exercises

### Exercise Set 1: User Account Management

```
1. Create a user account for your Help Desk team
   - What permissions should they have?
   - What should they NOT be able to do?

2. Test the account:
   - Log in as Help Desk user
   - Try to approve a file
   - Try to create another user
   - Try to view events

3. Create a custom role for "Application Approvers"
   - Can approve/ban files
   - Can create software rules
   - Cannot manage policies or users
```

### Exercise Set 2: Policy Configuration

```
1. Create three policies:
   a. Development (Low/Low enforcement)
   b. Standard Office (Low/High enforcement)  
   c. Production Server (High/High enforcement)

2. Move test computers to each policy

3. Test enforcement:
   - Try to run unapproved software on each
   - Document results
   - Explain why each behaves differently

4. Challenge: Create a policy for contractors
   - Moderate security when on-site
   - Maximum security when remote
```

### Exercise Set 3: Rules Mastery

```
1. Create a Publisher approval for Mozilla

2. Create a Custom Rule:
   - Trusted Path: C:\ApprovedSoftware\*
   - Applies to: Production Server policy
   
3. Create a File Integrity Rule:
   - Protect: C:\CriticalData\*
   - Exclusion: C:\CriticalApp\app.exe

4. Test all three rules:
   - Verify they work as expected
   - Try to circumvent them (ethical testing)
   - Document any issues

5. Create a BAN rule:
   - Target: Any game executable
   - Applies to: All policies except Development
```

### Exercise Set 4: Monitoring and Alerting

```
1. Create meters for:
   - Microsoft Word usage
   - PowerShell usage
   - Any executable in C:\Temp\

2. Create alerts for:
   - Any file blocked 5+ times
   - Any banned file execution attempt
   - Any new installation on production servers

3. Generate test events:
   - Trigger each alert
   - Verify they fire correctly
   - Check alert details

4. Analysis challenge:
   - Review a week of events
   - Identify top 10 most-run applications
   - Identify top 10 most-blocked applications
   - Present findings
```

### Exercise Set 5: Troubleshooting Scenarios

```
Scenario 1:
- User can't run Excel on their laptop
- Excel works on other computers
- Excel is globally approved
→ Diagnose and fix

Scenario 2:
- Custom Trusted Path rule not working
- Path: C:\Tools\*
- Computer in correct policy
- Rule is enabled
→ What could be wrong?

Scenario 3:
- File was approved yesterday
- File won't run today
- No policy changes
- File wasn't banned
→ What happened?

Scenario 4:
- Alert not firing
- Should alert on blocked files
- Files are being blocked (seen in events)
- Alert is enabled
→ Why isn't it working?

Scenario 5:
- Computer policy shows "Out of sync"
- Agent service is running
- Network connectivity is good
→ How do you resolve this?
```

---

## Quick Reference Guide

### Common Tasks Cheat Sheet

```
APPROVE A FILE GLOBALLY:
1. Reports → Events → Blocked Files
2. Find file → Click file name link
3. Actions → Approve Globally
4. Or: Create Software Rule

APPROVE A FILE LOCALLY:
1. Reports → Events → Blocked Files
2. Find file → Click Plus (+) icon
3. Or: Tools → Approval Requests → Approve File Locally

BAN A FILE:
1. Rules → Software Rules
2. Add File Rule → Rule Type: Ban
3. Enter file name
4. Select policies to apply to

CREATE CUSTOM RULE:
1. Rules → Software Rules → Custom
2. Add Custom Rule
3. Select rule type (Trusted Path, File Integrity, etc.)
4. Configure path/conditions
5. Select policies

MOVE COMPUTER TO POLICY:
1. Assets → Computers
2. Select computer checkbox
3. Action → Move Computer to Policy
4. Select target policy

CHECK WHY FILE WAS BLOCKED:
1. Reports → Events
2. Saved Views → Blocked Files (All)
3. Filter by computer/file name
4. Click file name → View details

VIEW COMPUTER FILES:
1. Assets → Computers
2. Click View Details (📄) icon
3. Related Views → Files on this Computer
4. Add filters as needed

CREATE SNAPSHOT:
1. Assets → Computers → Computer Details
2. Actions → Add Files to Snapshot
3. Name snapshot
4. Create

RUN HEALTH CHECK:
1. Assets → Computers → Computer Details
2. Advanced → Other Actions
3. Select: Run health check
4. Go

GENERATE TEMPORARY OVERRIDE:
1. Assets → Computers → Computer Details
2. Policy Override (bottom of page)
3. Set temporary enforcement level
4. Set duration
5. Generate Code
6. On endpoint: Run TimedOverride.exe
```

### Enforcement Level Quick Reference

```
HIGH:
- Blocks: Unapproved files
- Blocks: Banned files
- Allows: Approved files only
- Use for: Production, DMZ, critical systems

MEDIUM:
- Tracks: Unapproved files (allows them)
- Blocks: Banned files
- Allows: Approved and unapproved files
- Use for: Rarely used (transition period)

LOW:
- Tracks: All file activity
- Blocks: Banned files only
- Allows: Everything except banned
- Use for: Development, testing, monitoring

NONE:
- Minimal tracking
- No blocking (even banned files)
- Use for: Monitoring mode only, special cases
```

### File State Quick Reference

```
APPROVED:
- Runs on: All computers (global) or specific computer (local)
- Color in UI: Green
- How it got this way: Explicitly approved by admin

LOCALLY APPROVED:
- Runs on: One specific computer only
- Color in UI: Light green
- How it got this way: 
  • Found during initialization
  • Admin approved locally
  • Temporary override was used

UNAPPROVED:
- Runs on: Computers in LOW/NONE enforcement only
- Blocked on: Computers in HIGH enforcement
- Color in UI: Yellow/Gray
- How it got this way: New file, not yet approved

BANNED:
- Runs on: Never (all enforcement levels)
- Color in UI: Red
- How it got this way: Explicitly banned by admin
```

---

## Key Concepts Summary

### The Three Pillars of App Control

```
1. DEFAULT DENY
   - Everything blocked unless approved
   - Opposite of traditional antivirus
   - Maximum security posture

2. HASH-BASED TRACKING
   - Every file has unique fingerprint
   - Approval tied to specific hash
   - Different version = different file

3. POLICY-BASED CONTROL
   - Enforcement varies by computer group
   - Flexible security levels
   - Balance security with usability
```

### The Approval Hierarchy

```
Most Secure → Least Secure:

1. Hash Approval (Software Rule - File)
   ✓ Most secure
   ✗ Requires re-approval for updates
   
2. Publisher Approval (Software Rule - Publisher)
   ✓ Automatic for signed updates
   ✗ Trusts all software from publisher
   
3. Reputation Approval
   ✓ Automated based on trust score
   ✗ Relies on cloud data
   
4. Custom Rule (Trusted Path)
   ✓ Flexible for entire folders
   ✗ Requires strict folder permissions
   
5. Local Approval
   ✓ Quick fix for one computer
   ✗ Doesn't scale, not centrally managed
```

### Troubleshooting Mindset

```
❌ Wrong approach:
"Why is this blocked?" → Explain blocking mechanism

✅ Right approach:
"Why isn't the approval working?" → Fix the approval

Key principle:
- Blocking is normal and expected
- Focus on approval mechanisms
- Understand the customer's intended outcome
- Find the right approval method for their use case
```

---

## Advanced Topics

### 12. Understanding Updators and Rapid Configs

#### What Are Updators?

**Updators** are Carbon Black's built-in rules for common software updates.

```
Think of Updators like pre-installed keys:
- Carbon Black provides them
- They handle common software updates
- You just enable/disable them
- You don't see their technical details
```

**Common Updators:**

```
Available Updators:
✓ Adobe Reader/Acrobat
✓ Google Chrome
✓ Mozilla Firefox
✓ Microsoft Office
✓ Java
✓ And many more...

How they work:
1. Software tries to update itself
2. Update would be blocked (new version = new hash)
3. Updator recognizes the update mechanism
4. Update is automatically approved
5. No manual intervention needed
```

**When to use Updators:**

```
Scenario 1: Chrome keeps getting blocked during updates
Solution: Enable "Google Chrome" updator
Result: Chrome updates automatically approved

Scenario 2: Adobe Reader updates fail
Solution: Enable "Adobe Reader" updator
Result: Adobe updates work seamlessly

Scenario 3: Multiple Adobe products
Solution: Enable "Adobe Application Suite" updator
Result: All Adobe updates approved
```

**Accessing Updators:**

```
1. Navigate to: Rules → Software Rules
2. Click: Updaters tab
3. Browse available updators
4. Enable needed ones
5. Select which policies they apply to

Note: Updators are pre-configured
- You can't edit their logic
- You can only enable/disable them
- You choose which policies they apply to
```

#### What Are Rapid Configs?

**Rapid Configs** are the modern replacement for Updators - pre-built rule sets for specific purposes.

```
Evolution:
Old approach: Updators (limited, built-in)
New approach: Rapid Configs (flexible, downloadable)

Rapid Configs are:
- Sets of rules bundled together
- Downloadable from Broadcom portal
- Updated regularly
- More comprehensive than Updators
```

**Types of Rapid Configs:**

```
1. Application Updators:
   - Microsoft Office
   - Adobe Creative Cloud
   - Web browsers
   - Common business apps

2. Performance Optimization:
   - Microsoft Exchange Server
   - SQL Server
   - VMware products
   - Reduce agent overhead

3. Security Hardening:
   - OS Hardening (Windows)
   - Browser Protection
   - PowerShell restrictions
   - Credential theft prevention

4. Compliance:
   - HIPAA controls
   - PCI-DSS requirements
   - NIST frameworks
```

**Installing Rapid Configs:**

```
Prerequisites:
- Download Rules Installer from Broadcom portal
- Extract the package

Installation Steps:
1. Navigate to: Admin → Upload Rules Package
2. Select: Rules Installer ZIP file
3. Click: Upload
4. Wait: Package processes
5. Navigate to: Rules → Software Rules → Rapid Configs tab

Activation Steps:
6. Find: Desired Rapid Config (e.g., "Microsoft Office 365")
7. Status: Change to Enabled
8. Policies: Select which policies it applies to
9. Click: Save

What happens:
- Multiple rules are activated at once
- Rules work together as a set
- Provides comprehensive coverage
- Maintained by Carbon Black
```

**Example: Microsoft Exchange Server Rapid Config**

```
Problem:
- Exchange server performance slow
- Agent tracking thousands of Exchange files
- Excessive disk I/O
- Server struggling

Solution: Enable Exchange Rapid Config

What it does:
✓ Excludes Exchange temp files from tracking
✓ Optimizes rules for Exchange processes
✓ Reduces agent overhead by 70%
✓ Maintains security for executables
✗ Stops tracking non-critical Exchange files

Result:
- Exchange performance restored
- Security still maintained
- Agent uses less resources
- No manual rule creation needed
```

**Example: OS Hardening Rapid Config**

```
Purpose: Harden Windows OS against common attacks

What it includes:
✓ Block PowerShell if not signed
✓ Block scripts in user temp folders
✓ Block execution from ZIP files
✓ Restrict cmd.exe usage
✓ Block regsvr32 abuse
✓ Protect LSASS memory

Configuration:
1. Rules → Software Rules → Rapid Configs
2. Find: "Windows OS Hardening"
3. Enable
4. Select policies: Production servers
5. Save

What happens:
- Common attack vectors blocked
- Legitimate activity still works
- Reduces attack surface
- Compliance requirements met

Testing:
Before enabling on production:
1. Enable on test systems first
2. Monitor for 1-2 weeks
3. Check events for legitimate blocks
4. Adjust as needed
5. Then deploy to production
```

**Updators vs. Rapid Configs Comparison:**

```
UPDATORS:
✓ Built into console
✓ Simple enable/disable
✓ Limited selection
✓ Being phased out
✗ Can't customize
✗ Can't see rule details
Use for: Quick fixes, legacy approach

RAPID CONFIGS:
✓ Downloadable/updatable
✓ Comprehensive rule sets
✓ Regular updates from Carbon Black
✓ Can see individual rules
✓ More control
✗ Requires manual download/upload
Use for: Modern deployments, advanced needs
```

---

### 13. File Analysis and Reputation

#### Understanding Trust and Threat Levels

Every file in Carbon Black has two important scores:

**Trust Level (0-100)**

```
Trust = How reputable/known is this file?

100: Widely known, highly trusted
     - Microsoft Windows files
     - Adobe software
     - Google Chrome

85-99: Well-known, generally trusted
       - Popular commercial software
       - Signed by known publishers

50-84: Somewhat known, moderate trust
       - Less common software
       - Smaller vendors
       - Newer software

25-49: Little reputation data
       - Rare software
       - Unknown publishers
       - Custom/internal applications

1-24: Very little reputation
      - Extremely rare files
      - No reputation data

0: No trust data available
   - Newly created files
   - Custom scripts
   - Internal tools
```

**Threat Level (0-100)**

```
Threat = How likely is this file to be malicious?

0: No threat indicators
   - Clean software
   - No malware characteristics

1-20: Very low threat
      - Possibly unwanted programs (PUPs)
      - Adware potential

21-50: Moderate threat
       - Suspicious characteristics
       - Potentially unwanted

51-80: High threat
       - Malware indicators
       - Suspicious behavior patterns

81-100: Critical threat
        - Known malware
        - Active threats
        - Should be banned immediately
```

**Example File Analysis:**

```
File: putty.exe
Trust: 85
Threat: 0
Analysis: 
- Well-known SSH client
- Trusted publisher (Simon Tatham)
- No malicious indicators
- Safe to approve

File: cryptominer.exe
Trust: 0
Threat: 95
Analysis:
- No reputation data
- Exhibits malware behavior
- Known cryptocurrency miner
- BAN immediately

File: custom_script.ps1
Trust: 0
Threat: 0
Analysis:
- Custom internal script
- No reputation (internal file)
- No threat indicators
- Requires manual review
```

#### Viewing File Details

**Complete file analysis workflow:**

```
1. Navigate to: Reports → Events
2. Find: File execution event
3. Click: File name link
4. View: File Instance Details page

What you see:

GENERAL INFORMATION:
- File Name: example.exe
- File Hash: ABC123DEF456...
- File Size: 2.4 MB
- Company: Software Vendor Inc.
- Product Name: Example Application
- Product Version: 1.2.3.4
- File Description: Example App Installer

REPUTATION:
- Trust Level: 75
- Threat Level: 0
- Prevalence: 1,234 installations
- First Seen Globally: 2024-06-15
- Publisher: Verified (Software Vendor Inc.)

APPROVAL STATUS:
- State: Locally Approved
- Approved On: WORKGROUP\WINDOWS-CLIENT
- Approval Date: 2025-01-15
- Approved By: admin

FILE INSTANCES:
- Computer 1: C:\Downloads\example.exe
- Computer 2: C:\Program Files\Example\example.exe
- Computer 3: C:\Users\John\Desktop\example.exe

RELATED EVENTS:
- 15 execution events
- 2 installation events
- 0 blocked events
```

**Using this information for decisions:**

```
Scenario 1: Should I approve this?
Check:
✓ Trust > 70? Generally safe
✓ Threat = 0? No malware indicators
✓ Publisher verified? Extra confidence
✓ High prevalence? Widely used
→ Probably safe to approve

Scenario 2: This looks suspicious
Check:
✗ Trust < 30? Little reputation
✗ Threat > 50? Malware indicators
✗ No publisher? Unsigned
✗ Low prevalence? Rare file
→ Investigate further or ban

Scenario 3: Internal application
Check:
- Trust = 0 (expected, internal)
- Threat = 0 (no malware indicators)
- Publisher = None (expected, internal)
- Prevalence = 0 (expected, internal)
→ Manual review required
→ If legitimate: Approve
→ Consider signing internal apps
```

#### Reputation-Based Auto-Approval

**Automate approvals based on trust level**

```
Use case: Automatically approve high-trust files

Configuration:
1. Navigate to: Rules → Software Rules
2. Click: Reputation Approvals tab
3. Click: Enable Reputation Approvals
4. Configure:
   - Minimum Trust: 90
   - Action: Automatically approve
   - Policies: Select which policies
5. Save

What happens:
- New file appears (trust 95)
- Agent checks with server
- Server checks reputation service
- Trust level 95 > minimum 90
- File automatically approved
- User doesn't see any block

Benefits:
✓ Reduces approval workload
✓ Good user experience
✓ Secure (high trust threshold)
✓ Leverages global intelligence

Risks:⚠️ Trust scores can be incorrect
⚠️ Requires internet connectivity
⚠️ Depends on Carbon Black service
⚠️ Set threshold carefully (recommend 90+)
```

**Best practices for reputation approvals:**

```
DO:
✓ Set high trust threshold (85-90+)
✓ Enable for standard workstations
✓ Monitor auto-approvals in events
✓ Combine with other controls
✓ Test before wide deployment

DON'T:
✗ Use on critical servers
✗ Set threshold too low (<80)
✗ Rely solely on reputation
✗ Disable other security controls
✗ Forget to monitor
```

---

### 14. Agent Management and Health

#### Agent States and Status

**Understanding agent status:**

```
AGENT STATES:

Connected:
- Agent communicating with server
- Policy up to date
- Can receive updates immediately

Disconnected:
- Agent offline from server
- Using cached policy
- Enforcement continues locally
- No policy updates until reconnection

Out of Sync:
- Agent hasn't checked in recently
- Policy might be outdated
- Investigate connection issues

Error:
- Agent experiencing problems
- Check logs for details
- May need reinstallation
```

**Policy Status:**

```
Up to date:
✓ Latest policy applied
✓ All rules synchronized
✓ Agent functioning normally

Pending:
⏳ Policy change queued
⏳ Waiting for agent check-in
⏳ Normal during policy changes

Out of sync:
⚠️ Agent hasn't reported recently
⚠️ May have old policy
⚠️ Check agent connectivity

Failed:
❌ Policy application failed
❌ Check agent logs
❌ May need troubleshooting
```

#### Agent Logs and Diagnostics

**Log locations on endpoints:**

```
Windows:
C:\Program Files (x86)\Bit9\Parity Agent\Logs\

Common log files:
- parity.log: Main agent activity
- daemonui.log: User interface events
- notifier.log: User notifications
- update.log: Agent update events

Linux:
/opt/bit9/logs/

Common log files:
- parity.log
- daemon.log
```

**Reading agent logs:**

```
Example log entries:

Normal operation:
[INFO] Policy synchronization successful
[INFO] File cache updated: 1,234 files
[INFO] Server connection established

Connection issues:
[WARN] Server connection timeout
[WARN] Retrying connection in 60 seconds
[ERROR] Cannot reach server: network unreachable

Policy issues:
[ERROR] Policy download failed
[WARN] Using cached policy
[INFO] Policy updated to version 15

File events:
[INFO] File approved: notepad.exe (hash: ABC123)
[WARN] File blocked: malware.exe (hash: XYZ789)
[INFO] New file discovered: custom.exe
```

**Common log-based troubleshooting:**

```
Problem: Agent not checking in

Look for:
1. Connection errors in logs
2. Certificate validation failures
3. Network timeouts
4. DNS resolution issues

Example log:
[ERROR] SSL certificate validation failed
[ERROR] Server certificate expired

Solution: Update server certificate

---

Problem: Files not being tracked

Look for:
1. Performance optimization rules
2. Excluded paths
3. File system errors

Example log:
[WARN] File excluded by performance rule
[INFO] Path C:\Temp\* ignored per policy

Solution: Review performance rules

---

Problem: High CPU usage

Look for:
1. Excessive file scanning
2. Large number of file changes
3. Performance optimization needed

Example log:
[WARN] High file activity detected
[INFO] Scanned 50,000 files in last hour

Solution: Enable performance Rapid Config
```

#### Forcing Agent Actions

**Manual agent operations:**

```
1. Force Policy Update:
   Computer Details → Other Actions → Refresh Policy
   Use when: Policy status shows "Pending" too long

2. Force Health Check:
   Computer Details → Other Actions → Run health check
   Use when: Need to verify agent functionality

3. Force File Scan:
   Computer Details → Other Actions → Scan for new files
   Use when: New software installed, need immediate inventory

4. Restart Agent Service:
   On endpoint: Services.msc → Parity Agent → Restart
   Use when: Agent appears hung or unresponsive

5. Clear Local Cache:
   Advanced troubleshooting only
   Requires: Agent reinstallation
   Use when: Corrupted cache suspected
```

#### Agent Installation and Deployment

**Installation methods:**

```
1. Manual Installation:
   - Download agent installer from server
   - Run installer with parameters
   - Agent registers with server

2. Group Policy Deployment (Windows):
   - Create GPO
   - Assign agent installer
   - Deploy to OUs
   - Automatic installation on reboot

3. Configuration Management:
   - SCCM/Intune
   - Puppet/Chef/Ansible
   - Custom scripts
   - Mass deployment capability

4. Command Line Installation:
   Windows:
   msiexec /i agent_installer.msi /quiet SERVER=appcontrol.company.com

   Linux:
   ./agent_installer.sh --server=appcontrol.company.com
```

**Installation parameters:**

```
Required parameters:
SERVER=appcontrol.company.com
  - Server FQDN or IP
  
Optional parameters:
POLICY_NAME="Standard Corporate"
  - Pre-assign to specific policy
  
COMPUTER_TAG="Building-A"
  - Tag for organization
  
SILENT=1
  - No user interaction
  
Examples:

Workstation install:
msiexec /i agent.msi /quiet SERVER=appcontrol.company.com POLICY_NAME="Standard Corporate"

Server install:
msiexec /i agent.msi /quiet SERVER=appcontrol.company.com POLICY_NAME="Production Servers" COMPUTER_TAG="DataCenter-1"
```

---

### 15. Advanced Troubleshooting Scenarios

#### Scenario A: Performance Issues

**Customer:** "Agent is slowing down the computer!"

```
Symptoms:
- High CPU usage
- Slow application launches
- Disk I/O saturation
- User complaints

Investigation steps:

1. Check agent logs:
   - Look for excessive scanning
   - Check for errors
   - Note file scan counts

2. Review file tracking:
   Computer Details → Files on this Computer
   - How many files tracked?
   - Are temp files being tracked?
   - Any unnecessary tracking?

3. Identify problematic paths:
   Events → Filter by computer
   - Which paths have most activity?
   - Are temp folders being tracked?
   - Development folders with constant changes?

Common causes:

A. Development machine:
   - Compilers creating/deleting thousands of files
   - Agent tracking every change
   Solution: Enable performance optimization rules
   
   Custom Rule:
   - Type: Performance Optimization
   - Path: C:\Development\*
   - Action: Don't track (but still monitor execution)

B. Server with high file churn:
   - Exchange, SQL, web servers
   - Thousands of temp file changes
   Solution: Enable appropriate Rapid Config
   
   Example: Exchange Rapid Config
   - Excludes Exchange temp paths
   - Maintains security on executables
   - Reduces tracking by 70%

C. Antivirus interaction:
   - Both agents scanning same files
   - Double scanning = double overhead
   Solution: Configure exclusions
   
   In antivirus:
   - Exclude: C:\Program Files (x86)\Bit9\
   
   In App Control:
   - Generally not needed (designed to coexist)
```

**Performance Optimization Best Practices:**

```
1. Use Performance Optimization rules:
   Rules → Custom Rules → Add Custom Rule
   - Type: Performance Optimization
   - Paths with high file churn
   - Examples:
     C:\Windows\Temp\*
     C:\Users\*\AppData\Local\Temp\*
     C:\Development\*\build\*

2. Enable relevant Rapid Configs:
   - Exchange Server
   - SQL Server
   - VMware
   - Specific to your environment

3. Adjust tracking settings:
   Policy configuration:
   - Track File Changes: Disable on low-risk systems
   - Reduces overhead significantly
   - Still blocks unapproved executables
   - Just doesn't log every file modification

4. Tune initialization:
   - During agent installation
   - Initialization can take time
   - Schedule for off-hours if possible
   - One-time cost

5. Monitor continuously:
   - Create meter for agent.exe CPU usage
   - Alert on sustained high usage
   - Investigate and optimize
```

#### Scenario B: Legitimate Software Blocked

**Customer:** "I need this software for my job, why is it blocked?"

```
Investigation workflow:

Step 1: Verify it's legitimate
- What software?
- What's the business justification?
- Who else needs it?
- Any security concerns?

Step 2: Check file details
- Trust level?
- Threat level?
- Publisher information?
- Prevalence?

Step 3: Determine best approval method

Decision tree:

Is it signed software from known vendor?
→ YES: Use Publisher approval
   Example: FileZilla (signed by Tim Kosse)
   Result: All FileZilla versions approved automatically

→ NO: Continue to next question

Will it update frequently?
→ YES: Consider Trusted Path or Publisher
   Example: Custom LOB app with weekly updates
   Result: Less maintenance, auto-approves updates

→ NO: Continue to next question

Is it single executable or full suite?
→ SINGLE: Use hash approval (most secure)
   Example: Standalone utility tool
   Result: Only that specific version approved

→ SUITE: Use Publisher or Custom Rule
   Example: Adobe Creative Cloud
   Result: All suite components approved

Is it used company-wide or just one person?
→ COMPANY-WIDE: Global approval
   Result: Approved for all computers

→ ONE PERSON: Local approval
   Result: Approved only on their computer

Is it for specific policy/group?
→ YES: Software Rule with policy scope
   Result: Approved only for selected policies
```

**Example decision process:**

```
Case 1: Accounting department needs QuickBooks

Analysis:
- Legitimate business software
- Signed by Intuit
- Used by 15 people in Accounting
- Updates quarterly

Best solution:
1. Create software rule (Publisher approval)
2. Approve: Intuit Inc. publisher
3. Apply to: Accounting policy only
4. Result: All QuickBooks updates auto-approved for Accounting

---

Case 2: IT needs PuTTY

Analysis:
- Well-known SSH client
- Only IT team uses it (5 people)
- Rarely updates
- Single executable

Best solution:
1. Create software rule (File/Hash approval)
2. Approve: putty.exe (specific hash)
3. Apply to: Security IT Endpoints policy
4. Result: Specific version approved for IT team

---

Case 3: Developer needs Visual Studio Code

Analysis:
- Signed by Microsoft
- Updates frequently (monthly)
- Just one developer
- Full application suite

Best solution:
1. Local approval on developer's computer
2. Or: Publisher approval (Microsoft Corporation)
3. Apply to: Development policy
4. Result: VS Code and updates work automatically
```

#### Scenario C: Rule Not Working As Expected

**Customer:** "I created a rule but it's not working!"

```
Systematic debugging:

Step 1: Verify rule exists and is enabled
- Navigate to: Rules → Software Rules → Custom
- Find the rule
- Status: Enabled?
- If disabled: Enable it

Step 2: Check rule syntax
Common errors:
❌ C:\Repository (missing asterisk)
✓ C:\Repository\* (correct)

❌ C:\Program Files\App\*.exe (too specific?)
✓ C:\Program Files\App\* (all files in folder)

❌ \Repository\* (missing drive)
✓ C:\Repository\* (includes drive)
✓ *\Repository\* (any drive)

Step 3: Verify policy assignment
- Rule applies to: Which policies?
- Computer is in: Which policy?
- Do they match?

Example problem:
Rule applies to: Standard Corporate
Computer in: DMZ policy
Result: Rule doesn't apply!

Fix: Either change rule to include DMZ, or move computer to Standard Corporate

Step 4: Check rule rank/precedence
Multiple rules can conflict:

Example:
Rule 1 (Rank 1): BAN all .exe in C:\Temp\*
Rule 2 (Rank 5): ALLOW all files in C:\Temp\*

File: C:\Temp\program.exe
Result: BANNED (Rule 1 executes first)

Fix: 
- Option A: Change ranks (make ALLOW higher priority)
- Option B: Adjust rule conditions (be more specific)
- Option C: Delete conflicting rule

Step 5: Test and verify
- Have user try to run file again
- Check Events immediately
- Look for:
  → Was rule evaluated?
  → Which rule executed?
  → What was the result?

Events will show:
"Rule 'Software Repository' matched"
"Action taken: Allow and Promote"
OR
"Rule 'Software Repository' did not match"
"Reason: Path did not match pattern"
```

**Rule testing best practice:**

```
Before deploying rule widely:

1. Create test rule:
   - Apply to: TEST policy only
   - Status: Enabled

2. Move one computer to TEST policy

3. Test thoroughly:
   - Positive test: File should work (does it?)
   - Negative test: File should block (does it?)
   - Edge cases: Subfolders, different file types

4. Review events:
   - Did rule execute as expected?
   - Any unintended blocks?
   - Any unintended allows?

5. Adjust rule based on results

6. Deploy to production:
   - Apply to: Production policies
   - Monitor for 24-48 hours
   - Address any issues quickly

7. Document:
   - Why rule created
   - What it does
   - Any special considerations
```

#### Scenario D: Agent Won't Connect

**Customer:** "Agent shows 'Disconnected' and won't reconnect"

```
Troubleshooting checklist:

1. Basic connectivity:
   On endpoint, test:
   ☐ Can ping server? ping appcontrol.company.com
   ☐ Can resolve DNS? nslookup appcontrol.company.com
   ☐ Can reach HTTPS? Open browser to https://appcontrol/
   ☐ Firewall blocking? Check firewall logs

2. Agent service status:
   Windows:
   ☐ services.msc → Parity Agent
   ☐ Status: Running?
   ☐ Startup: Automatic?
   ☐ Try: Restart service

   Linux:
   ☐ systemctl status parity
   ☐ Running?
   ☐ Try: systemctl restart parity

3. Certificate issues:
   ☐ Server certificate expired?
   ☐ Agent trusts server certificate?
   ☐ Check agent logs for SSL errors
   
   Common log errors:
   "SSL certificate validation failed"
   "Certificate has expired"
   "Certificate name mismatch"

   Fix: Update/renew server certificate

4. Server-side issues:
   ☐ Server accessible?
   ☐ Server services running?
   ☐ Database connection OK?
   ☐ License valid?

5. Agent installation issues:
   ☐ Agent version compatible with server?
   ☐ Installation completed successfully?
   ☐ May need: Reinstallation

6. Network configuration:
   ☐ Proxy settings correct?
   ☐ Port 41002 (default) open?
   ☐ SSL inspection interfering?
```

**Resolution steps:**

```
Quick fixes:

1. Restart agent service:
   Windows: services.msc → Parity Agent → Restart
   Linux: systemctl restart parity
   
2. Force policy refresh:
   Computer Details → Other Actions → Refresh Policy

3. Clear and reconnect:
   Computer Details → Other Actions → Reset Connection

If quick fixes don't work:

1. Collect diagnostics:
   - Agent logs
   - Network trace
   - Firewall logs
   - Server logs

2. Check server health:
   - Admin → Server Health Dashboard
   - Look for errors
   - Check service status

3. Reinstall agent if necessary:
   - Uninstall existing agent
   - Reboot
   - Install latest agent version
   - Should automatically register and connect
```

---

### 16. Real-World Deployment Strategies

#### Phased Deployment Approach

**Don't deploy to production all at once!**

```
Phase 1: Pilot (Week 1-2)
- Select: 10-20 test computers
- Include: Mix of roles (desktop, laptop, server)
- Policy: Low Enforcement
- Goals:
  ✓ Validate agent installation
  ✓ Confirm connectivity
  ✓ Baseline file inventory
  ✓ Identify common applications

Actions:
- Install agents
- Monitor events
- Create approval rules for common software
- Identify performance issues
- Train pilot users

---

Phase 2: Expand Pilot (Week 3-4)
- Select: 50-100 more computers
- Include: Each department represented
- Policy: Still Low Enforcement
- Goals:
  ✓ Discover more applications
  ✓ Refine approval rules
  ✓ Test custom rules
  ✓ Build approval library

Actions:
- Approve common business applications
- Create publisher rules
- Enable relevant Rapid Configs
- Document unique department needs
- Gather user feedback

---

Phase 3: Production Roll-out (Week 5-8)
- Deploy: All workstations
- Policy: Low → Medium → High (gradual)
- Goals:
  ✓ Achieve full coverage
  ✓ Maintain business continuity
  ✓ Minimize user impact

Day 1-7: Low Enforcement
- Everything runs
- Everything tracked
- Build comprehensive application list

Day 8-14: Approve applications
- Review last 7 days of events
- Approve all legitimate business software
- Create necessary rules

Day 15-21: Increase to High Enforcement (Connected)
- Block unapproved software at office
- Still allow when disconnected
- Monitor for legitimate blocks
- Quick approval process in place

Day 22+: High Enforcement (Disconnected)
- Full protection everywhere
- Laptops protected when traveling
- Comprehensive security posture

---

Phase 4: Server Protection (Week 9-12)
- Start: Non-critical servers
- Policy: High/High from start
- Goals:
  ✓ Protect critical infrastructure
  ✓ Ensure no production impact

Actions:
- Create server-specific policies
- Enable performance Rapid Configs
- Create snapshots for baseline drift
- Document server-specific applications
- Create custom rules for server apps
```

**Policy structure recommendations:**

```
Recommended policies:

1. Executive/VIP
   - Connected: Low
   - Disconnected: Medium
   - Rationale: Flexibility for executives, some protection

2. Standard Corporate
   - Connected: Low
   - Disconnected: High
   - Rationale: Balance flexibility and security

3. Development
   - Connected: Low
   - Disconnected: Low
   - Rationale: Developers need flexibility, accept risk

4. Production Servers
   - Connected: High
   - Disconnected: High
   - Rationale: Maximum protection, minimal change

5. DMZ/Internet-Facing
   - Connected: High
   - Disconnected: High
   - Rationale: Highest risk, tightest control

6. Temporary - New Deployments
   - Connected: Low
   - Disconnected: Low
   - Rationale: Discovery mode for new systems
```

#### Change Management

**Every change should be deliberate and tracked**

```
Before making changes:

1. Document current state:
   - Which computers affected?
   - Current policy/enforcement?
   - Existing rules?
   - Take snapshot if servers

2. Define desired outcome:
   - What should happen?
   - Who requested it?
   - Business justification?
   - Risk assessment?

3. Test in lab/pilot:
   - Create test rule
   - Apply to test systems
   - Verify behavior
   - Check for unintended consequences

4. Schedule change:
   - Maintenance window?
   - Business hours OK?
   - User notification needed?
   - Rollback plan?

5. Implement change:
   - Make the change
   - Verify applied correctly
   - Monitor immediately
   - Check for issues

6. Validate and document:
   - Did it work as expected?
   - Any issues?
   - Document for future reference
   - Update change log
```

**Example change process:**

```
Request: Finance department needs new accounting software

Step 1: Review request
- Software: AccountingPro 2025
- Users: 12 people in Finance department
- Justification: Required for audit season
- Risk: Low (commercial software, reputable vendor)

Step 2: Analysis
- Publisher: FinSoft Corporation (verified)
- Trust Level: 80
- Threat Level: 0
- Updates: Quarterly

Step 3: Test
- Install on test machine
- Verify functionality
- Check for dependencies
- Test updates

Step 4: Create approval
- Method: Publisher approval (FinSoft Corporation)
- Scope: Finance policy only
- Reason: Accounting software for audit season
- Created by: admin
- Date: 2025-01-15

Step 5: Deploy
- Apply rule to Finance policy
- Notify Finance department
- Install software on Finance computers
- Monitor for 48 hours

Step 6: Validate
- All installations successful
- No unexpected blocks
- Software functions correctly
- Update change log
```

---

### 17. Maintenance and Ongoing Operations

#### Daily Operations Checklist

```
Daily tasks (5-10 minutes):

☐ Check alerts
   - Any high priority alerts?
   - Investigate and resolve
   
☐ Review approval requests
   - Tools → Approval Requests
   - Approve/deny pending requests
   - Respond to users

☐ Check agent connectivity
   - Assets → Computers
   - Any agents showing disconnected?
   - Investigate long-term disconnections

☐ Review blocked files
   - Reports → Events → Blocked Files
   - Any patterns?
   - Legitimate software being blocked?
   - Quick approvals where appropriate
```
