# Carbon Black App Control Advanced Administrator
## Complete Beginner's Guide with Enhanced Lab Manual

---

## 📚 Table of Contents

1. [Prerequisites & Foundation Knowledge](#prerequisites)
2. [Understanding Key Concepts Before Labs](#key-concepts)
3. [Lab Environment Setup Guide](#lab-setup)
4. [Enhanced Lab Exercises](#enhanced-labs)
5. [Troubleshooting Reference](#troubleshooting)
6. [Quick Reference Guide](#quick-reference)

---

<a name="prerequisites"></a>
## 1. Prerequisites & Foundation Knowledge

### What You Need to Know BEFORE Starting the Labs

#### A. Basic Windows Navigation Skills

**File System Basics:**
- **Drive Letter (C:)**: The main hard drive where Windows is installed
- **Folders/Directories**: Containers for files (like folders in a filing cabinet)
- **Path**: The "address" of a file. Example: `C:\Windows\System32\notepad.exe`
  - `C:` = Drive
  - `\Windows\System32` = Folders
  - `notepad.exe` = File

**Practice Exercise:**
1. Open File Explorer (Windows key + E)
2. Navigate to `C:\Windows\System32`
3. Find `notepad.exe`
4. Right-click → Properties to see its full path

---

#### B. Understanding User Permissions

**Administrator vs. Standard User:**
- **Administrator**: Can install software, change system settings, access all files
- **Standard User**: Limited permissions, cannot modify system files

**Running as Administrator:**
```
Right-click any program → "Run as administrator"
```
This is like having a master key that opens all doors.

---

#### C. Basic Networking Concepts

**What You Need to Know:**

| Concept | Simple Explanation | Example |
|---------|-------------------|---------|
| **IP Address** | Like a phone number for computers | `10.100.10.101` |
| **Port** | Like different "doors" for different services | Port 443 = Web traffic (HTTPS) |
| **Server** | A computer that provides services to other computers | App Control Server |
| **Client** | A computer that uses services from a server | Your Windows workstation |

---

#### D. Introduction to Databases (Don't Panic!)

**What is SQL Server?**
Think of it as a super-organized filing system where CB App Control stores all its information:
- List of all computers
- List of all files and their approval status
- Security events and logs
- Policy configurations

**You Don't Need to:**
- Write SQL code
- Understand database design
- Manage the database directly

**You Only Need to Know:**
- It's where data is stored
- If it stops working, CB App Control stops working
- It runs as a Windows service

---

<a name="key-concepts"></a>
## 2. Understanding Key Concepts Before Labs

### 🎯 The Big Picture: How CB App Control Works

```
┌─────────────────────────────────────────────────────────┐
│                    CB APP CONTROL SERVER                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │  Web Console │  │  SQL Database│  │ Policy Engine│  │
│  │ (Your View)  │  │ (File Lists) │  │ (The Rules)  │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
└────────────────────────┬────────────────────────────────┘
                         │ Port 41002 (Agent Communication)
                         │ Port 443 (Web Console)
                         │
        ┌────────────────┴────────────────┐
        │                                  │
   ┌────▼─────┐                      ┌────▼─────┐
   │  AGENT   │                      │  AGENT   │
   │ (Windows)│                      │ (Linux)  │
   │          │                      │          │
   │ Monitors │                      │ Monitors │
   │ & Blocks │                      │ & Blocks │
   └──────────┘                      └──────────┘
```

---

### 🔑 Critical Terminology (Master These First!)

#### 1. **Agent**
**What it is:** Software installed on each computer you want to protect.

**What it does:**
- Watches every file that tries to run
- Checks with the server: "Is this file approved?"
- Blocks unapproved files (if policy says so)
- Reports events back to server

**Real-world analogy:** Like a security guard at a building entrance checking IDs.

---

#### 2. **Policy**
**What it is:** A collection of rules that tell the agent how to behave.

**Components:**
- **Enforcement Level**: How strict should we be?
- **Rules**: Special exceptions or requirements
- **Settings**: Additional configurations

**Types of Enforcement Levels:**

| Level | What Happens | When to Use | Traffic Light Analogy |
|-------|-------------|-------------|----------------------|
| **High Enforcement** | Only approved files run. Everything else is BLOCKED. | Production servers, locked-down systems | 🔴 Red light = Stop unless you have permission |
| **Medium Enforcement** | User gets a prompt asking "Allow or Block?" | User workstations where flexibility needed | 🟡 Yellow light = Proceed with caution |
| **Low Enforcement** | Everything runs, but agent WATCHES and LOGS | Testing and learning phase | 🟢 Green light = Go, but we're watching |

---

#### 3. **File States** (This is CRUCIAL!)

Every file CB App Control knows about has two states:

**A. Global State** (Across ALL computers)
- **Approved** ✅: File can run on any computer
- **Unapproved** ❓: Not approved yet, subject to policy
- **Banned** 🚫: File is blocked everywhere

**B. Local State** (On ONE specific computer)
- **Locally Approved** 🏠: Can run on THIS computer only
- **Locally Banned** 🏠🚫: Blocked on THIS computer only

**Example Scenario:**
```
notepad.exe on Computer A:
  - Global State: Approved ✅
  - Local State: Approved ✅
  → Result: Runs everywhere

malware.exe on Computer B:
  - Global State: Banned 🚫
  - Local State: (doesn't matter)
  → Result: Blocked everywhere

custom_tool.exe on Computer C:
  - Global State: Unapproved ❓
  - Local State: Locally Approved 🏠
  → Result: Runs on Computer C only, blocked elsewhere
```

---

#### 4. **Rules** (The Fine-Tuning Controls)

Rules are special instructions that override normal behavior.

**Types of Rules You'll Use in Labs:**

| Rule Type | What It Controls | Example Use Case |
|-----------|-----------------|------------------|
| **File Integrity Control** | Prevents CHANGES to specific files/folders | Protect system configuration files |
| **File Creation Control** | Controls WHO can CREATE/MODIFY files WHERE | Allow only Notepad to edit hosts file |
| **Advanced Rule** | Combines multiple conditions (Execute + Write) | Let developers write AND run code in specific folder |
| **Event Rule** | Automatically takes action when events occur | Auto-approve files from trusted publishers |

---

#### 5. **Initialization** (First-Time Setup)

**What happens when agent is first installed:**

```
Step 1: Agent installs on computer
   ↓
Step 2: Agent scans ALL existing files
   ↓
Step 3: Agent creates "fingerprints" (hashes) of files
   ↓
Step 4: Agent sends list to server
   ↓
Step 5: Server marks existing files as "Locally Approved"
   ↓
Step 6: Status changes from "Initializing" to "Normal"
```

**Why this matters:**
- Prevents CB from blocking your operating system!
- Existing software continues to work
- Only NEW files are subject to strict control

**How long does it take?**
- Small system: 5-10 minutes
- Large system: 30-60 minutes
- Server with millions of files: Several hours

---

### 🧪 Understanding the Lab Scenario

**The Story Behind the Labs:**

You are a security administrator at a company. Your job is to:
1. **Protect critical system files** from being modified (Lab 2)
2. **Allow developers to work** while maintaining security (Lab 3)
3. **Automate approvals** for trusted software (Lab 4)
4. **Troubleshoot issues** when things go wrong (Labs 5 & 6)

**The Characters:**
- **You**: Security administrator using the CB Console
- **Windows Client**: A user's workstation (test computer)
- **HE Policy**: "High Enforcement" - very strict security
- **Developers**: People who need to write and test code

---

<a name="lab-setup"></a>
## 3. Lab Environment Setup Guide

### Understanding Your Lab Environment

**What You Have:**

1. **App Control Server (Windows Server)**
   - IP: `10.100.10.100` (assumed)
   - Contains: CB Server software, SQL database, Web Console
   - Login: `administrator` / `Gr95M23rE9`

2. **App Control Client (Windows 10/11)**
   - IP: `10.100.10.101` (assumed)
   - Contains: CB Agent software
   - Login: `administrator` / `VMware1!`

---

### Pre-Lab Checklist

Before starting ANY lab, verify:

```
□ Can you login to Windows Client?
□ Can you open Chrome on Windows Client?
□ Can you access CB Console at: https://<server-ip>
□ Console login works: admin / admin123
□ Windows Client appears in Assets → Computers
□ Windows Client status shows "Normal" (not "Initializing")
```

**If "Initializing" status:**
- ⏰ Wait 10-30 minutes
- ☕ Take a break
- 🔄 Refresh the page periodically
- ✅ Proceed only when status = "Normal"

---

### Quick Start: Accessing the Lab

**Step-by-Step Access Procedure:**

1. **Power On VMs** (if not already running)
   ```
   Start App Control Server first
   Wait 2-3 minutes for services to start
   Then start App Control Client
   ```

2. **Login to Windows Client**
   ```
   Username: administrator
   Password: VMware1!
   ```

3. **Open Chrome Browser**
   ```
   Double-click Chrome icon on desktop
   OR
   Press Windows key → Type "chrome" → Enter
   ```

4. **Access CB Console**
   ```
   URL: https://10.100.10.100
   (Or use the server's hostname if configured)
   
   ⚠️ Certificate Warning?
   Click: Advanced → Proceed to site (unsafe)
   This is normal for lab environments
   ```

5. **Login to Console**
   ```
   Username: admin
   Password: admin123
   ```

6. **Verify Agent Connection**
   ```
   Click: Assets → Computers
   Look for: WORKGROUP\WINDOWS-CLIENT
   Status should be: Normal ✅
   ```

---

<a name="enhanced-labs"></a>
## 4. Enhanced Lab Exercises

---

## 🔬 LAB 2: File Control Rules
### Understanding File Protection

---

### 📋 Lab Objectives

By the end of this lab, you will understand:
- How to create and apply policies
- How File Integrity Control protects files from modification
- How File Creation Control allows specific programs to modify files
- The difference between blocking ALL changes vs. blocking changes FROM specific programs

---

### 🎯 The Scenario Explained

**The Problem:**
The Windows `hosts` file (`C:\Windows\System32\drivers\etc\hosts`) is a critical system file that can be abused by malware to redirect web traffic. You want to:
1. Block ALL programs from modifying it
2. BUT allow administrators using Notepad to make legitimate changes

**Real-World Application:**
This technique protects critical configuration files while allowing authorized tools to work.

---

### 📚 Background Knowledge Required

#### Understanding the Hosts File

**What is it?**
The `hosts` file is like a personal phone book for your computer. It maps website names to IP addresses.

**Example hosts file content:**
```
# This is a comment
127.0.0.1       localhost
10.100.10.101   windowsclient
```

**What this means:**
- When you type "localhost" in browser → go to 127.0.0.1
- When you type "windowsclient" → go to 10.100.10.101

**Why malware targets it:**
Malware can add entries like:
```
127.0.0.1   www.bank.com
```
Now when you try to visit your bank, you go to the attacker's fake site!

---

#### Understanding File Paths

**Anatomy of a Windows path:**
```
C:\Windows\System32\drivers\etc\hosts
│  │       │         │       │   │
│  │       │         │       │   └─ File name
│  │       │         │       └───── Folder name
│  │       │         └───────────── Subfolder
│  │       └─────────────────────── Subfolder
│  └─────────────────────────────── System folder
└────────────────────────────────── Drive letter
```

**Wildcards in paths:**
- `*` = Match anything
- `C:\Folder\*` = All files in that folder
- `C:\Folder\*.exe` = Only .exe files in that folder

---

### 🔧 Task 1: Access the Labs

**What you're doing:** Logging in and verifying access.

**Detailed Steps:**

1. **Login to Windows Client**
   ```
   Press Ctrl+Alt+Delete (if needed)
   Username: administrator
   Password: VMware1!
   Press Enter
   ```

2. **Wait for Desktop to Load**
   - You should see the Windows desktop
   - Icons should appear
   - Taskbar should be visible

3. **Open Chrome**
   ```
   Method 1: Double-click Chrome icon on desktop
   Method 2: Click Start → Type "chrome" → Press Enter
   ```

4. **Navigate to CB Console**
   ```
   In address bar, type: https://10.100.10.100
   Press Enter
   
   ⚠️ See "Your connection is not private"?
   This is normal! Click: Advanced → Proceed to 10.100.10.100 (unsafe)
   ```

5. **Login**
   ```
   Username: admin
   Password: admin123
   Click: Login
   ```

**✅ Success Check:**
- You see the CB App Control dashboard
- Top menu shows: Assets, Rules, Reports, Tools

**❌ Troubleshooting:**
- Can't access console? → Check if Server VM is running
- Login fails? → Verify password is exactly `admin123` (case-sensitive)

---

### 🔧 Task 2: Create a Policy to Use for Training Labs

**What you're doing:** Creating a strict security policy that will be applied to the Windows Client.

**Why we're doing this:** We need a "High Enforcement" policy to test blocking behavior.

---

**Background: Understanding Policies**

Think of a policy as a security profile. Different computers can have different policies:
- **Servers** might use High Enforcement (very strict)
- **Developer workstations** might use Medium Enforcement (flexible)
- **Test systems** might use Low Enforcement (monitoring only)

---

**Detailed Steps:**

1. **Navigate to Policies Page**
   ```
   In top menu → Hover over "Rules"
   Click: Policies
   ```
   
   **What you see:**
   - A list of existing policies
   - Default policies might include: "Test", "Medium Enforcement", etc.

2. **Start Creating New Policy**
   ```
   Click the blue "Add Policy" button (top right area)
   ```
   
   **What happens:**
   - A form opens with many fields
   - Don't panic! We'll fill them out step by step

3. **Fill in Basic Information**
   ```
   Policy name: HE
   Description: Policy for high enforcement
   ```
   
   **💡 Tip:** "HE" stands for "High Enforcement" - short names are easier to work with

4. **Set the Mode**
   ```
   Look for: "Mode" dropdown
   Select: Control
   ```
   
   **What this means:**
   - **Control Mode**: Agent actively blocks/allows files
   - **Monitor Mode**: Agent only watches and logs (doesn't block)
   - **Agent Disabled**: Agent doesn't monitor at all

5. **Configure Enforcement Levels**
   ```
   Find the "Enforcement Level" section
   
   Connected: High
   Disconnected: High
   ```
   
   **What this means:**
   - **Connected**: When agent can reach server
   - **Disconnected**: When agent loses network connection
   - **High**: Only approved files can run

   **Why set both to High:**
   - Ensures consistent security even if network fails
   - Prevents "unplug the network" bypass

6. **Enable File Tracking**
   ```
   Look for: "Track File Changes" checkbox
   Verify: It should be CHECKED ✅
   ```
   
   **What this does:**
   - Monitors when files are created/modified/deleted
   - Provides visibility into system changes
   - Critical for forensics and troubleshooting

7. **Save the Policy**
   ```
   Scroll to bottom
   Click: "Save & Exit"
   ```
   
   **What happens:**
   - Policy is created and saved
   - You return to the Policies list page
   - Your new "HE" policy appears in the list

**✅ Success Check:**
- Policy "HE" appears in the list
- Mode shows "Control"
- Enforcement shows "High/High"

---

**🤔 Common Questions:**

**Q: Why did we create a new policy instead of using an existing one?**
A: For training purposes, it's cleaner to have a dedicated policy. In production, you might modify existing policies.

**Q: What happens if I select "Medium" instead of "High"?**
A: Users would get prompts instead of hard blocks. We want hard blocks for these labs to see clear behavior.

**Q: Can I change the policy later?**
A: Yes! Policies can be edited at any time. Changes are pushed to agents within minutes.

---

### 🔧 Task 3: Move the Windows Client to the HE Policy

**What you're doing:** Assigning our new strict policy to the Windows Client computer.

**Why we're doing this:** Until we assign the policy, the Windows Client is still using whatever policy it had before (probably a lenient one).

---

**Background: Understanding Computer Management**

Every agent-protected computer must belong to exactly ONE policy. Think of it like:
- Your computer = Student
- Policy = Class/Grade Level
- You're transferring the student to a new class

---

**Detailed Steps:**

1. **Navigate to Computers Page**
   ```
   Top menu → Hover over "Assets"
   Click: Computers
   ```
   
   **What you see:**
   - List of all computers with CB agents installed
   - Columns showing: Computer name, Policy, Status, Last seen, etc.

2. **Locate Windows Client**
   ```
   Look for a row with: WORKGROUP\WINDOWS-CLIENT
   
   💡 You might need to scroll or use the search box
   ```
   
   **What the name means:**
   - **WORKGROUP**: The Windows workgroup/domain name
   - **WINDOWS-CLIENT**: The actual computer hostname
   - **Backslash (\)**: Separator between workgroup and hostname

3. **Select the Computer**
   ```
   Click the CHECKBOX ☐ next to WORKGROUP\WINDOWS-CLIENT
   ```
   
   **Visual cue:**
   - Checkbox should now be checked ☑
   - Row might be highlighted
   - Action buttons at top become active

4. **Open Actions Menu**
   ```
   Look at the top of the page
   Find: "Action" dropdown button
   Click it
   ```
   
   **What you see:**
   - A dropdown menu with various actions
   - Options like: "Delete", "Refresh", "Move Computer to Policy", etc.

5. **Move to New Policy**
   ```
   From the dropdown, find and click:
   "Move Computer to Policy: HE"
   ```
   
   **💡 Note:** You should see "HE" listed because we just created it. If you don't see it, go back and verify Task 2.

6. **Confirm the Action**
   ```
   A confirmation dialog appears asking:
   "Are you sure you want to move this computer?"
   
   Click: OK
   ```
   
   **What happens behind the scenes:**
   - Server updates the database
   - Server sends new policy to the agent
   - Agent applies new rules

7. **Wait for Policy Update**
   ```
   Look at the "Policy Status" column for Windows Client
   It will show: "Pending" or "Updating"
   
   Click the "Refresh Page" button (🔄 circular arrow icon)
   Keep clicking every 10-15 seconds
   ```
   
   **What you're waiting for:**
   - Status changes from "Pending" to "Up to date"
   - This means the agent received and applied the new policy

   **How long?**
   - Usually: 15-60 seconds
   - Could be longer if network is slow

**✅ Success Check:**
- Policy column now shows "HE"
- Policy Status shows "Up to date" ✅
- Computer is still showing Status: "Normal"

**❌ Troubleshooting:**

| Problem | Possible Cause | Solution |
|---------|---------------|----------|
| Don't see Windows Client | Agent not installed or not communicating | Check if agent service is running on client |
| Policy Status stays "Pending" | Network issue or agent offline | Wait longer, check network connectivity |
| Can't find "Move to Policy" option | No computer selected | Make sure checkbox is checked |

---

**🤔 Common Questions:**

**Q: What happens to files that were already approved under the old policy?**
A: Local approvals remain. Only the enforcement rules change.

**Q: Can I move multiple computers at once?**
A: Yes! Check multiple checkboxes and use the same action.

**Q: Can I move the computer back to the original policy?**
A: Absolutely. Just repeat the process and select the original policy.

**Q: What if the agent is offline when I move it?**
A: The policy change is queued. When the agent reconnects, it will receive the update.

---

### 🔧 Task 4: Verify You Can Manipulate the Hosts File

**What you're doing:** Testing that you can currently edit the hosts file BEFORE we apply restrictions.

**Why we're doing this:** Establishing a baseline. We need to prove the file CAN be edited before we block it.

---

**Background: Understanding the Test**

This is like checking that a door can open before you lock it. We need to confirm:
1. The file exists
2. We have permission to edit it
3. Changes can be saved

Then in the next task, we'll lock it down.

---

**Detailed Steps:**

1. **Ensure You're on Windows Client**
   ```
   Look at the desktop
   Verify you're NOT on the server
   
   💡 Quick check: Look at computer name in bottom-right corner
   ```

2. **Open Notepad as Administrator**
   ```
   Click: Start button (Windows logo, bottom-left)
   Type: notepad
   
   You'll see "Notepad" appear in search results
   
   RIGHT-CLICK on "Notepad"
   Select: "Run as administrator"
   
   ⚠️ User Account Control prompt appears
   Click: Yes
   ```
   
   **Why "Run as administrator"?**
   - The hosts file is in a protected system folder
   - Only administrators can edit files there
   - Running as admin gives Notepad the necessary permissions

3. **Open the Hosts File**
   ```
   In Notepad:
   Click: File menu → Open
   ```
   
   **Navigation Steps:**
   ```
   In the "Open" dialog:
   
   Step 1: Click "This PC" in left sidebar
   Step 2: Double-click "Local Disk (C:)"
   Step 3: Double-click "Windows" folder
   Step 4: Double-click "System32" folder
   Step 5: Double-click "drivers" folder
   Step 6: Double-click "etc" folder
   
   ⚠️ Can't see any files?
   Look at the bottom-right of the dialog
   Change dropdown from "Text Documents (*.txt)" to "All Files (*.*)"
   ```
   
   **What you should see now:**
   - Several files including "hosts" (no extension)
   - Possibly "networks", "protocol", "services"

4. **Open the Hosts File**
   ```
   Click on: hosts
   Click: Open button
   ```
   
   **What you see:**
   - A text file with comments (lines starting with #)
   - Possibly some entries like "127.0.0.1 localhost"
   - Might be mostly empty except comments

5. **Make a Test Edit**
   ```
   Scroll to the very bottom of the file
   
   On a NEW LINE, type:
   10.100.10.101[TAB]windowsclient
   
   ⚠️ IMPORTANT: Press TAB key between the IP and hostname
   Don't type the word [TAB], actually press the Tab key!
   ```
   
   **What this line does:**
   - Maps "windowsclient" to IP 10.100.10.101
   - Now if you type "windowsclient" in browser, it goes to that IP

6. **Save the File**
   ```
   Click: File menu → Save
   OR
   Press: Ctrl+S
   ```
   
   **Expected result:**
   - File saves successfully
   - No error message
   - Notepad title bar shows "hosts" (not "hosts*")

**✅ Success Check:**
- File saved without errors
- If you close and reopen the file, your line is still there

**❌ If You See Errors:**

| Error Message | Meaning | Solution |
|--------------|---------|----------|
| "Access denied" | Notepad doesn't have admin rights | Close Notepad, reopen as administrator |
| "File is in use" | Another program has the file open | Close other programs, try again |
| "Cannot save" | File might be read-only | Right-click hosts → Properties → Uncheck "Read-only" |

---

**🤔 Common Questions:**

**Q: Why did we add that specific line?**
A: It's just a test entry we can easily identify later. The content doesn't matter, only that we can modify the file.

**Q: What if the file already has entries?**
A: That's fine! Just add your line to the bottom.

**Q: Do I need to keep Notepad open?**
A: No, you can close it. We'll open it again in the next task.

**Q: What's the TAB for?**
A: The hosts file format requires whitespace (tab or spaces) between IP and hostname. Tab is standard.

---

### 🔧 Task 5: Create a File Integrity Control Rule to Block Edits to the etc Directory

**What you're doing:** Creating a rule that BLOCKS all programs from modifying anything in the `etc` directory.

**Why we're doing this:** This is a strict "nobody can touch this folder" rule. In the real world, you'd use this for critical system files.

---

**Background: Understanding File Integrity Control**

**File Integrity Control** is like putting a glass case around a valuable item:
- You can LOOK at it (read)
- You CANNOT touch it (write/modify)
- NOBODY can touch it (applies to all programs)

**Where it's used in real life:**
- Protecting system configuration files
- Securing registry backups
- Protecting audit logs from tampering

---

**Detailed Steps:**

1. **Navigate to Software Rules**
   ```
   Top menu → Hover over "Rules"
   Click: "Software Rules"
   ```
   
   **What you see:**
   - Tabs: "Windows", "Mac", "Linux", "Custom"
   - Lists of approved/banned software

2. **Switch to Custom Tab**
   ```
   Click the "Custom" tab
   ```
   
   **What you see:**
   - Custom rules you create appear here
   - Might be empty if this is a fresh install
   - Button: "Add Custom Rule"

3. **Start Creating New Rule**
   ```
   Click: "Add Custom Rule" button
   ```
   
   **What happens:**
   - A form opens with many fields
   - This is where we define our protection rule

4. **Fill in Basic Information**
   ```
   Rule name: Etc Directory
   Description: Block edits to etc directory
   ```
   
   **💡 Naming tip:** Use descriptive names that explain what the rule does. Future-you will thank you!

5. **Set Rule Status**
   ```
   Find: "Status" section
   Click: "Enabled" radio button
   ```
   
   **Options explained:**
   - **Enabled**: Rule is active and enforced immediately
   - **Disabled**: Rule exists but doesn't do anything (good for testing)
   - **Simulate**: Rule runs but doesn't block (logs what WOULD happen)

6. **Select Platform**
   ```
   Find: "Platform" dropdown
   Verify: It's set to "Windows"
   ```
   
   **Why this matters:**
   - Rules are platform-specific
   - Windows rules don't affect Linux agents (different file paths)

7. **Select Rule Type**
   ```
   Find: "Rule Type" dropdown
   Click it and select: "File Integrity Control"
   ```
   
   **Rule Type Options:**
   - **File Integrity Control**: Block modifications (what we're using)
   - **File Creation Control**: Block creation of new files
   - **Execution Control**: Block running programs
   - **Advanced**: Combination of multiple controls

8. **Configure Write Action**
   ```
   Find: "Write Action" dropdown
   Verify: It's set to "Block"
   ```
   
   **What "Write Action" means:**
   - **Block**: Prevent all write operations
   - **Allow**: Permit write operations (used with other restrictive rules)

9. **Specify the Path**
   ```
   Find: "Path or File" dropdown
   Verify: It's set to "Specific Path..."
   
   In the text box below, type:
   C:\Windows\System32\drivers\etc\*
   ```
   
   **Understanding the path:**
   ```
   C:\Windows\System32\drivers\etc\*
                                    ^
                                    └─ Wildcard means "all files in this folder"
   ```
   
   **What this protects:**
   - `C:\...\etc\hosts`
   - `C:\...\etc\networks`
   - `C:\...\etc\protocol`
   - Any other file in that folder

10. **Select Which Policies This Rule Applies To**
    ```
    Find: "Rule Applies To" section
    Click: "Selected policies" radio button
    
    In the list below, check the box for: HE
    ```
    
    **Why choose policies:**
    - You might want different rules for different groups
    - Example: Developers need flexibility, servers need lockdown
    - Here, we're applying to our "High Enforcement" policy only

11. **Save the Rule**
    ```
    Scroll to bottom
    Click: "Save & Exit"
    ```
    
    **What happens:**
    - Rule is saved to database
    - Server will push this rule to all computers in "HE" policy

12. **Wait for Rule to Apply**
    ```
    Click: "Assets" → "Computers"
    Find: WORKGROUP\WINDOWS-CLIENT
    Look at: "Policy Status" column
    
    Keep clicking "Refresh Page" until it shows: "Up to date"
    ```
    
    **Why we wait:**
    - Agent needs to download the new rule
    - Usually takes 30-60 seconds
    - Until "Up to date", the rule isn't active yet

**✅ Success Check:**
- Rule "Etc Directory" appears in Custom tab
- Status shows "Enabled"
- Windows Client Policy Status: "Up to date"

---

**🎓 What Did We Just Do?**

We created a "glass case" around the `etc` folder:
```
Before: ✍️ Any program can edit files in etc folder
After:  🚫 NO program can edit files in etc folder
```

`C:\Windows\System32\drivers\etc\hosts` (no wildcard)

**Q: Can I have multiple paths in one rule?**
A: No, but you can create multiple rules or use wildcards strategically.

**Q: What happens if someone tries to delete a file in etc?**
A: Deletion is a "write operation", so it's blocked too!

**Q: Does this affect reading the file?**
A: No! Programs can still READ the files, just not modify them.

---

### 🔧 Task 6: Verify File Manipulation of the Hosts File Is No Longer Possible

**What you're doing:** Testing that our rule is working by attempting to edit the hosts file again.

**Expected outcome:** We should be BLOCKED from saving changes.

---

**Background: What's Happening Behind the Scenes**

When you try to save the hosts file now:
```
You click Save
    ↓
Notepad tries to write to C:\...\etc\hosts
    ↓
CB Agent intercepts the write operation
    ↓
Agent checks rules: "Is writing to C:\...\etc\* allowed?"
    ↓
Rule says: "Block all writes"
    ↓
Agent blocks the operation
    ↓
You see: Error message or Notifier popup
```

---

**Detailed Steps:**

1. **Ensure You're on Windows Client**
   ```
   Verify you're on the client desktop
   ```

2. **Open Notepad as Administrator**
   ```
   Click: Start
   Type: notepad
   RIGHT-CLICK: Notepad
   Select: "Run as administrator"
   Click: Yes (UAC prompt)
   ```

3. **Open the Hosts File**
   ```
   File → Open
   Navigate: This PC → C: → Windows → System32 → drivers → etc
   Change file type: "All Files (*.*)"
   Select: hosts
   Click: Open
   ```
   
   **What you see:**
   - The hosts file opens successfully
   - You see the line you added earlier: `10.100.10.101    windowsclient`

4. **Attempt to Delete Your Test Line**
   ```
   Locate the line: 10.100.10.101    windowsclient
   Highlight the entire line
   Press: Delete key
   ```
   
   **What you see:**
   - Line is removed from Notepad window
   - So far, nothing stops you from editing the TEXT

5. **Try to Save**
   ```
   Click: File → Save
   OR
   Press: Ctrl+S
   ```
   
   **💥 EXPECTED RESULT:**
   You should see a **CB App Control Notifier** popup that says something like:
   ```
   Access Denied
   
   Carbon Black App Control has blocked this action.
   File: C:\Windows\System32\drivers\etc\hosts
   Process: C:\Windows\System32\notepad.exe
   Rule: Etc Directory
   ```

6. **Try Saving with a Different Name**
   ```
   Click: File → Save As
   In filename box, type: hosts_edit
   Click: Save
   ```
   
   **💥 EXPECTED RESULT:**
   Same block! The notifier appears again.
   
   **Why?**
   Because the rule blocks ALL writes to the `etc\*` folder, including creating new files.

**✅ Success Check:**
- You see the CB Notifier blocking the save
- File remains unchanged (if you close and reopen, your deleted line is still gone in Notepad but not saved to disk)
- No errors about Notepad crashing

---

**❌ Troubleshooting:**

| Problem | Possible Cause | Solution |
|---------|---------------|----------|
| File saves successfully | Rule not applied yet | Check Policy Status, wait longer |
| No Notifier popup | Notifier disabled | Check if notifier service is running |
| Different error message | Rule misconfigured | Review Task 5, verify path exactly |

---

**🧐 Question from Lab Guide:**

**Q: Why are you unable to save this file?**

**A: Because our File Integrity Control rule blocks ALL write operations to the `C:\Windows\System32\drivers\etc\*` path, regardless of which program is trying to write. Even though Notepad is running as Administrator (which normally has full permissions), the CB Agent enforces the rule BEFORE Windows checks file permissions.**

**Detailed Explanation:**
```
Normal Windows Security:
Administrator → ✅ Can write to system files

With CB App Control:
Administrator → CB Agent checks rules → Rule says Block → ❌ Cannot write
```

The CB Agent sits at the **kernel level** (deep in Windows), intercepting operations before they reach the file system.

---

**🤔 Common Questions:**

**Q: Can I still read the file?**
A: Yes! File Integrity Control only blocks WRITES. Reading is still allowed.

**Q: What if I rename the file first?**
A: Still blocked. Any write operation (create, modify, delete, rename) is blocked.

**Q: What if I copy the file to another location first, edit it, then copy back?**
A: The final copy back into `etc\` would be blocked.

**Q: What if I boot into Safe Mode?**
A: CB Agent runs in Safe Mode too (if configured properly), so still blocked.

**Q: Is there ANY way to edit this file now?**
A: Yes, but only by:
   1. Disabling the rule, OR
   2. Moving computer to different policy, OR
   3. Creating an exception rule (next task!)

---

### 🔧 Task 7: Change the File Integrity Control to a File Creation Control

**What you're doing:** Creating a NEW rule that allows ONLY Notepad to modify the hosts file.

**Why we're doing this:** Sometimes you need to protect files from most programs while allowing specific trusted tools to work.

---

**Background: Understanding the Rule Hierarchy**

**Current situation:**
```
Rule: "Etc Directory" (File Integrity Control)
Action: Block ALL writes to C:\...\etc\*
Result: NOBODY can modify any file in etc folder
```

**What we're adding:**
```
Rule: "Notepad Allow Edits to Hosts" (File Creation Control)
Action: Allow writes to C:\...\etc\hosts ONLY from Notepad
Result: Notepad can modify hosts, but other programs still blocked
```

**How rules interact:**
- More specific rules override general rules
- **Allow** rules can override **Block** rules
- Order matters: CB evaluates most specific match first

---

**Detailed Steps:**

1. **Navigate to Software Rules**
   ```
   Top menu → Rules → Software Rules
   Click: Custom tab
   ```

2. **Create New Rule**
   ```
   Click: "Add Custom Rule"
   ```

3. **Fill in Basic Information**
   ```
   Rule name: Notepad Allow Edits to Hosts
   Description: Allow notepad edits to hosts file
   ```

4. **Enable the Rule**
   ```
   Status: Click "Enabled"
   ```

5. **Set Platform**
   ```
   Platform: Windows (should be default)
   ```

6. **Select Rule Type**
   ```
   Rule Type: Select "File Creation Control"
   ```
   
   **Why File Creation Control?**
   - Despite the name, it controls BOTH creation AND modification
   - Lets us specify which PROCESS can write
   - More flexible than File Integrity Control

7. **Set Write Action**
   ```
   Write Action: Select "Allow"
   ```
   
   **Critical distinction:**
   - Previous rule: Write Action = Block
   - This rule: Write Action = Allow
   - This creates an exception!

8. **Specify the Target File**
   ```
   Path or File: "Specific Path..."
   
   In text box, type:
   C:\Windows\System32\drivers\etc\hosts
   ```
   
   **⚠️ NOTE THE DIFFERENCE:**
   - Previous rule: `etc\*` (all files)
   - This rule: `etc\hosts` (one specific file)
   - No wildcard this time!

9. **Specify Which Process Can Write**
   ```
   Find: "Process" dropdown
   Select: "Specific Process..."
   
   In text box, type:
   C:\Windows\System32\Notepad.exe
   ```
   
   **What this means:**
   - ONLY `notepad.exe` from this exact path can write to hosts
   - If malware pretends to be notepad.exe but is in a different folder, still blocked!

10. **Apply to HE Policy**
    ```
    Rule Applies To: Select "Selected policies"
    Check: HE
    ```

11. **Save and Wait**
    ```
    Click: "Save & Exit"
    
    Navigate to: Assets → Computers
    Refresh until: Policy Status = "Up to date"
    ```

**✅ Success Check:**
- New rule appears in Custom tab
- Status: Enabled
- Two rules now exist: "Etc Directory" and "Notepad Allow Edits to Hosts"

---

**🎓 Understanding the Combined Effect:**

```
┌─────────────────────────────────────────────────┐
│         RULE EVALUATION ORDER                    │
├─────────────────────────────────────────────────┤
│                                                  │
│  Program tries to write to:                     │
│  C:\Windows\System32\drivers\etc\hosts          │
│                                                  │
│  ┌──────────────────────────────────────────┐  │
│  │ Is program Notepad.exe?                   │  │
│  │                                            │  │
│  │  YES ──→ Rule: "Notepad Allow..." ──→ ✅ │  │
│  │                                            │  │
│  │  NO  ──→ Rule: "Etc Directory"    ──→ ❌ │  │
│  └──────────────────────────────────────────┘  │
│                                                  │
└─────────────────────────────────────────────────┘
```

---

**🤔 Common Questions:**

**Q: Why didn't we just modify the existing rule?**
A: Because we want BOTH rules active:
   - General rule: Block everything
   - Specific rule: Allow one exception

**Q: What if I open hosts with WordPad instead of Notepad?**
A: Blocked! The rule specifically says only `Notepad.exe`

**Q: What about other files in the etc folder?**
A: Still fully protected by the "Etc Directory" rule. Only hosts has an exception.

**Q: Can I add more programs to the exception?**
A: You'd need separate rules for each program, or use more advanced rule syntax.

**Q: Does the rule check if Notepad is legitimate?**
A: Yes, CB checks the file hash of Notepad.exe. If malware replaced it, the hash wouldn't match and it would be blocked.

---

### 🔧 Task 8: Verify File Manipulation of the Hosts File Is Now Possible for Notepad

**What you're doing:** Testing that Notepad CAN now edit hosts, while other programs still CANNOT.

**Expected outcome:** Notepad saves successfully, but only Notepad.

---

**Detailed Steps:**

**Part A: Test with Notepad (Should Work)**

1. **Open Notepad as Administrator**
   ```
   Start → Type "notepad"
   Right-click → "Run as administrator"
   Yes (UAC)
   ```

2. **Open Hosts File**
   ```
   File → Open
   Navigate: This PC → C: → Windows → System32 → drivers → etc
   File type: "All Files (*.*)"
   Select: hosts
   Click: Open
   ```

3. **Delete Your Test Line**
   ```
   Find line: 10.100.10.101    windowsclient
   Delete it
   ```

4. **Save the File**
   ```
   File → Save (or Ctrl+S)
   ```
   
   **💚 EXPECTED RESULT:**
   - File saves successfully
   - No errors
   - No CB Notifier popup
   - ✅ SUCCESS!

5. **Try Save As**
   ```
   File → Save As
   Filename: hosts_edit
   Click: Save
   ```
   
   **💔 EXPECTED RESULT:**
   - CB Notifier blocks this
   - ❌ Blocked!
   
   **Why?**
   Our rule allows Notepad to write to `hosts`, but NOT to create `hosts_edit` (different filename).

---

**Part B: Test with Different Program (Should Fail)**

6. **Open WordPad**
   ```
   Start → Type "wordpad"
   Right-click → "Run as administrator"
   Yes (UAC)
   ```

7. **Try to Open Hosts File**
   ```
   File → Open
   Navigate: C:\Windows\System32\drivers\etc
   File type: "All Files (*.*)"
   Open: hosts
   ```
   
   **What you see:**
   - File opens (reading is allowed)
   - Content displays

8. **Try to Save**
   ```
   Make any edit (type a space)
   File → Save
   ```
   
   **💥 EXPECTED RESULT:**
   - CB Notifier blocks!
   - ❌ Blocked!
   
   **Why?**
   The exception is ONLY for `Notepad.exe`, not `WordPad.exe`

**✅ Success Check:**
- Notepad can save to hosts ✅
- Notepad cannot create hosts_edit ❌
- WordPad cannot save to hosts ❌

---

**🧐 Lab Guide Questions:**

**Q: Can the file be saved?**
**A: Yes, when using Notepad.exe. No, when using any other program.**

**Q: Why are you unable to save the file as hosts_edit?**
**A: The File Creation Control rule specifically allows writes to `C:\...\etc\hosts` (exact filename). Saving as `hosts_edit` is a different file, not covered by the rule. The general File Integrity Control rule (`etc\*`) still blocks it.**

**Q: If you open the hosts file with a different text editor, such as WordPad, are you able to save the file?**
**A: No. The File Creation Control rule allows only `C:\Windows\System32\Notepad.exe` to write. WordPad's executable path is `C:\Program Files\Windows NT\Accessories\wordpad.exe`, which doesn't match the rule condition.**

---

**🎓 Key Learning Points:**

1. **Specificity Matters:**
   ```
   Specific rule (Allow Notepad → hosts) 
   overrides 
   General rule (Block * → etc\*)
   ```

2. **Process Path Is Critical:**
   ```
   C:\Windows\System32\Notepad.exe ✅ Allowed
   C:\Path\To\Malware\notepad.exe  ❌ Blocked
   ```

3. **File Path Is Exact:**
   ```
   etc\hosts       ✅ Covered by rule
   etc\hosts_edit  ❌ Not covered
   etc\hosts.bak   ❌ Not covered
   ```

---

**🤔 Real-World Application:**

This technique is used in production to:
- Allow backup software to write to protected directories
- Allow antivirus to update definition files
- Allow monitoring tools to write logs to protected locations
- Allow Configuration Management tools (like SCCM) to update system files

**Example production rule:**
```
Rule: Allow SCCM to update C:\Windows\System32
Process: C:\Windows\CCM\CCMExec.exe
Path: C:\Windows\System32\*
Write Action: Allow
```

---

## 🎉 Lab 2 Complete!

### What You Learned:

✅ **File Integrity Control**: Blocks ALL programs from modifying files
✅ **File Creation Control**: Allows SPECIFIC programs to modify files
✅ **Rule Hierarchy**: Specific rules override general rules
✅ **Process Paths**: CB validates the exact executable path
✅ **Wildcards**: `*` matches multiple files, specific paths match one file

### Skills Gained:

✅ Creating policies
✅ Moving computers between policies
✅ Creating custom rules
✅ Testing rule effectiveness
✅ Understanding rule interactions

---

**Ready for Lab 3?** 
Lab 3 introduces **Advanced Rules**, which combine multiple controls (Execute + Write) to allow developers to work in secure environments. Take a short break, review what you've learned, then continue!

---

**🤔 Common Questions:**

**Q: Why use wildcard (*) instead of specifying just the hosts file?**
A: To protect ALL files in that folder, not just one. More comprehensive security.

# 🔬 LAB 3: Advanced Rules
## Allowing Developers to Work Securely

---

## 📋 Lab Objectives

By the end of this lab, you will understand:
- Why developers need special permissions in high enforcement environments
- How to use **Advanced Rules** to combine Execute and Write permissions
- The difference between File Creation Control and Advanced Rules
- How to create a secure development environment that balances security with productivity

**Estimated Time:** 30-45 minutes

---

## 🎯 The Real-World Scenario

### The Business Problem

**Situation:**
You're a security administrator at a software company. Your security policy requires:
- ✅ All workstations must use **High Enforcement** (only approved files run)
- ✅ Developers need to write and test new code
- ❌ Problem: High Enforcement blocks all unapproved code from running!

**The Challenge:**
```
Developer writes code → Compiles it → Tries to run it
                                            ↓
                                    CB Agent blocks it!
                                    (Unapproved file)
```

**Business Impact:**
- Developers cannot test their work
- Productivity drops to zero
- Either: Weaken security OR Find a smart solution

**The Solution:**
Use **Advanced Rules** to create a "sandbox" where:
- Developers can write code to a specific directory (`C:\Code`)
- Developers can execute code FROM that directory
- Code OUTSIDE that directory is still blocked (security maintained)

---

## 📚 Background Knowledge Required

### Understanding the Development Workflow

**What developers do:**
```
Step 1: Write source code
   Example: hello.py or script.bat
   
Step 2: Save to disk
   Requires: WRITE permission
   
Step 3: Run/Execute the code
   Requires: EXECUTE permission
   
Step 4: Test and debug
   Requires: Both WRITE (to modify) and EXECUTE (to test)
```

**Why normal rules don't work:**

**File Creation Control:**
- ✅ Allows WRITING code
- ❌ Doesn't allow EXECUTING code
- Result: Developer can save files but not run them

**Execution Control:**
- ✅ Allows EXECUTING code
- ❌ Doesn't control where code can be written
- Result: Developer can run approved files but can't create new ones

**Advanced Rule:**
- ✅ Allows BOTH writing AND executing
- ✅ Can be limited to specific directories
- ✅ Can specify which programs can perform actions
- Result: Perfect for development scenarios!

---

### Understanding File Types

**Executable Files** (Can run as programs):
- `.exe` - Compiled Windows programs
- `.bat` - Batch scripts (text files with commands)
- `.cmd` - Command scripts
- `.ps1` - PowerShell scripts
- `.vbs` - VBScript files

**In this lab:**
We'll create `.bat` files (batch scripts) because:
- Easy to create (just text)
- Easy to understand
- Don't require compilers
- Clearly demonstrate the concepts

**Example batch script:**
```batch
@echo off
echo Hello, World!
pause
```
This is a simple program that prints text.

---

### Understanding Process Relationships

**Who runs your code?**

When you double-click a `.bat` file:
```
You double-click script.bat
    ↓
Windows Explorer (explorer.exe) launches it
    ↓
Windows calls cmd.exe (Command Prompt)
    ↓
cmd.exe reads and executes script.bat
```

**Why this matters for rules:**
- If we want users to double-click scripts, we need to allow `explorer.exe`
- If we want to run scripts from Command Prompt, we need to allow `cmd.exe`
- Advanced Rules let us specify these parent processes

---

## 🔧 Task 1: Create an Advanced Rule to Verify That New Code Fails with High Enforcement

**What you're doing:** Creating a test scenario to prove that High Enforcement blocks new code.

**Why we're doing this:** Before fixing a problem, we need to confirm the problem exists!

---

**Detailed Steps:**

### Part A: Create the Code Directory

1. **Ensure You're on Windows Client**
   ```
   Check: Desktop shows Windows Client
   Not the server!
   ```

2. **Open File Explorer**
   ```
   Method 1: Click folder icon on taskbar
   Method 2: Press Windows key + E
   Method 3: Right-click Start → File Explorer
   ```

3. **Navigate to C: Drive**
   ```
   In left sidebar, click "This PC"
   In right pane, double-click "Local Disk (C:)"
   ```
   
   **What you see:**
   - Folders like: Windows, Program Files, Users, etc.

4. **Create New Folder**
   ```
   In ribbon at top, click "New Folder"
   OR
   Right-click empty space → New → Folder
   
   Name it: Code
   Press: Enter
   ```
   
   **✅ Result:**
   - New folder `C:\Code` appears
   - Icon shows an empty folder

---

### Part B: Create a Test Script

5. **Open Notepad (Regular, Not Admin)**
   ```
   Click: Start
   Type: notepad
   Click: Notepad (regular click, don't right-click)
   ```
   
   **⚠️ Note:** We're NOT running as administrator this time. We're acting like a regular developer.

6. **Write Your First Script**
   ```
   In Notepad, type exactly:
   
   ping 10.100.10.101
   ```
   
   **What this command does:**
   - `ping` = Network testing tool (built into Windows)
   - `10.100.10.101` = Windows Client's IP address
   - Result: Tests network connectivity to itself

7. **Save the Script**
   ```
   File → Save As
   
   Navigate to: C:\Code
   File name: unapproved.bat
   Save as type: All Files (*.*)
   
   Click: Save
   ```
   
   **⚠️ Important:**
   - Must have `.bat` extension
   - Must be saved in `C:\Code`
   - Case doesn't matter: `.bat` or `.BAT` both work

---

### Part C: Try to Run the Script

8. **Navigate to Code Directory**
   ```
   In File Explorer, go to C:\Code
   ```
   
   **What you see:**
   - Your file: `unapproved.bat`
   - File icon shows a gear/cog (batch file icon)

9. **Attempt to Execute**
   ```
   Double-click: unapproved.bat
   ```
   
   **💥 EXPECTED RESULT:**
   You see the **CB App Control Notifier** popup:
   ```
   Access Denied
   
   Carbon Black App Control has blocked this action.
   File: C:\Code\unapproved.bat
   Process: C:\Windows\explorer.exe
   Policy: HE
   Enforcement: High
   
   [Request Approval Button]
   ```

10. **Try from Command Prompt**
    ```
    Click: Start
    Type: cmd
    Press: Enter (regular user Command Prompt)
    
    In Command Prompt, type:
    cd C:\Code
    Press: Enter
    
    Type:
    unapproved.bat
    Press: Enter
    ```
    
    **💥 EXPECTED RESULT:**
    Same block! CB Notifier appears again.

---

**✅ Success Check:**
- Folder `C:\Code` exists
- File `unapproved.bat` exists in that folder
- Double-clicking the file is blocked
- Running from cmd is blocked

---

**🧐 Lab Guide Question:**

**Q: Why are you unable to run unapproved.bat?**

**A: Because the computer is in the "HE" policy with High Enforcement. Under High Enforcement, only files with an Approved state can execute. The file `unapproved.bat` was just created, so it has never been approved by CB App Control. Therefore, when we try to execute it (by double-clicking or running from Command Prompt), the CB Agent intercepts the execution attempt and blocks it according to policy.**

**Detailed Breakdown:**
```
File State Check:
1. unapproved.bat was just created
2. Server has never seen this file before
3. Global State: Unapproved ❓
4. Local State: Unapproved ❓

Policy Check:
5. Computer is in "HE" policy
6. Enforcement Level: High
7. High Enforcement rule: "Block unapproved files"

Result:
8. CB Agent blocks execution
9. Notifier alerts user
```

---

**🤔 Common Questions:**

**Q: Why can I create the file but not run it?**
A: CB App Control separates Write (creating/modifying files) from Execute (running files). High Enforcement blocks execution of unapproved files, but doesn't prevent creating them (by default).

**Q: What if I copied the file from another computer instead of creating it?**
A: Same result. It's still unapproved, so still blocked.

**Q: Can I just approve this one file?**
A: Yes, but then every time you modify it, you'd need to re-approve it. Not practical for development!

**Q: What about the ping command inside the script?**
A: `ping.exe` itself is approved (it's part of Windows). But the `.bat` file that wants to run ping is not approved.

---

## 🔧 Task 2: Create a File Creation Control Rule to Block All Writes to the Code Directory

**What you're doing:** Adding a rule that blocks ALL programs from writing to `C:\Code`.

**Why we're doing this:** To demonstrate a common security posture: "Lock down the directory completely first, then add specific exceptions."

---

**Background: Defense in Depth**

This is a security principle called **"Defense in Depth"**:

```
Layer 1: High Enforcement (blocks unapproved execution)
Layer 2: File Creation Control (blocks writing to Code directory)
Layer 3: Advanced Rule (exception for specific tools)
```

If an attacker bypasses one layer, others still protect you.

---

**Detailed Steps:**

1. **Navigate to Software Rules**
   ```
   In CB Console (Chrome)
   Top menu → Rules → Software Rules
   Click: Custom tab
   ```

2. **Create New Rule**
   ```
   Click: "Add Custom Rule"
   ```

3. **Fill in Basic Info**
   ```
   Rule name: Block code writes
   Description: Block edits to the Code directory
   ```
   
   **💡 Naming convention:**
   - Start with the action: "Block" or "Allow"
   - Describe what's affected: "code writes"
   - Makes it easy to scan rule lists later

4. **Enable the Rule**
   ```
   Status: Enabled
   ```

5. **Set Platform**
   ```
   Platform: Windows
   ```

6. **Select Rule Type**
   ```
   Rule Type: File Creation Control
   ```
   
   **Why File Creation Control here?**
   - Controls WHO can write
   - We'll set it to block EVERYONE
   - Later, Advanced Rule will override for specific processes

7. **Set Write Action**
   ```
   Write Action: Block
   ```

8. **Specify Path**
   ```
   Path or File: "Specific Path..."
   
   In text box:
   C:\Code\*
   ```
   
   **Understanding the wildcard:**
   - `C:\Code\*` = All files directly in Code folder
   - Does NOT include subfolders (for this lab, that's fine)
   - If you had `C:\Code\Project1\file.txt`, this rule wouldn't affect it

9. **Apply to HE Policy**
   ```
   Rule Applies To: Selected policies
   Check: HE
   ```

10. **Save and Wait**
    ```
    Click: "Save & Exit"
    
    Go to: Assets → Computers
    Refresh until: Windows Client shows "Up to date"
    ```

**✅ Success Check:**
- Rule "Block code writes" appears in Custom tab
- Status: Enabled
- Write Action: Block
- Path: C:\Code\*

---

**🎓 What We Just Did:**

```
Before this rule:
- Could create files in C:\Code
- Could not execute them (High Enforcement)

After this rule:
- Cannot create files in C:\Code
- Cannot execute files in C:\Code
- Directory is now "read-only" to everyone
```

---

## 🔧 Task 3: Verify That Writing New Code Fails Because of the File Creation Control Rule

**What you're doing:** Testing that we can no longer create files in `C:\Code`.

**Expected outcome:** Saving a new file should be blocked.

---

**Detailed Steps:**

1. **Open Notepad (Regular User)**
   ```
   Start → Type "notepad" → Click Notepad
   ```

2. **Write Another Test Script**
   ```
   Type in Notepad:
   ping 10.100.10.101
   ```

3. **Try to Save**
   ```
   File → Save As
   Navigate to: C:\Code
   File name: nowrite.bat
   Save as type: All Files (*.*)
   Click: Save
   ```
   
   **💥 EXPECTED RESULT:**
   CB Notifier blocks the save operation:
   ```
   Access Denied
   
   Carbon Black App Control has blocked this action.
   File: C:\Code\nowrite.bat
   Process: C:\Windows\System32\notepad.exe
   Rule: Block code writes
   ```

**✅ Success Check:**
- Save operation blocked
- CB Notifier explains which rule blocked it
- File `nowrite.bat` does NOT appear in `C:\Code` folder

---

**🧐 Lab Guide Question:**

**Q: Why are you unable to save nowrite.bat?**

**A: The File Creation Control rule "Block code writes" prevents ALL processes from writing files to `C:\Code\*`. Even though Notepad is a legitimate Microsoft program, and even though we're logged in as Administrator, the CB Agent enforces the rule at the kernel level before Windows file permissions are checked. The rule specifies Write Action: Block for the entire path, with no process exceptions, so all write attempts fail.**

---

**🤔 Common Questions:**

**Q: What about the file we created earlier (unapproved.bat)?**
A: It still exists! The rule only affects NEW write operations, not existing files.

**Q: Can I delete files from C:\Code?**
A: No! Deletion is a write operation, so it's blocked too.

**Q: Can I read files from C:\Code?**
A: Yes! Reading is not affected by File Creation Control.

**Q: What if I try to save to a subfolder like C:\Code\Temp?**
A: That would work! Our rule only covers `C:\Code\*` (files directly in Code folder), not subfolders. In production, you'd use `C:\Code\**` to include all subfolders.

---

## 🔧 Task 4: Create an Advanced Rule That Permits Writes to the Code Directory

**What you're doing:** Creating our first **Advanced Rule** that allows Notepad to write to `C:\Code`.

**Why we're doing this:** This creates an exception to the "Block code writes" rule, but ONLY for Notepad.

---

**Background: Understanding Rule Precedence**

When CB Agent evaluates rules, it uses this logic:

```
┌─────────────────────────────────────┐
│  1. Check for MOST SPECIFIC rule    │
│     ↓                                │
│  2. Check Process match              │
│     ↓                                │
│  3. Check Path match                 │
│     ↓                                │
│  4. Apply the matched rule's action  │
└─────────────────────────────────────┘

Example:
- General rule: Block writes to C:\Code\*
- Specific rule: Allow Notepad.exe to write to C:\Code\*
- Result: Notepad can write, others cannot
```

**Why Advanced Rule?**
- File Creation Control: Only controls Write OR Execute (one at a time)
- Advanced Rule: Can control Write AND Execute together
- Here, we're only using Write, but we'll add Execute later

---

**Detailed Steps:**

1. **Navigate to Custom Rules**
   ```
   Rules → Software Rules → Custom tab
   ```

2. **Create New Rule**
   ```
   Click: "Add Custom Rule"
   ```

3. **Fill in Basic Info**
   ```
   Rule name: Permit code writes for the compiler
   Description: Permit edits to Code directory by the compiler
   ```
   
   **⚠️ Note about the name:**
   - Lab guide says "compiler" but we're using Notepad
   - In real scenarios, this would be your IDE (Visual Studio, etc.)
   - For this lab, Notepad is our "compiler"

4. **Enable the Rule**
   ```
   Status: Enabled
   ```

5. **Set Platform**
   ```
   Platform: Windows
   ```

6. **Select Rule Type**
   ```
   Rule Type: Advanced
   ```
   
   **🎯 This is the key difference!**

7. **Select Operation Type**
   ```
   Operation: Write
   ```
   
   **Operation options:**
   - **Write**: Control file creation/modification
   - **Execute**: Control program execution
   - **Execute and Write**: Control both (we'll use this later)

8. **Set Write Action**
   ```
   Write Action: Allow
   ```
   
   **Critical distinction:**
   - Previous rule: Write Action = Block (general rule)
   - This rule: Write Action = Allow (specific exception)

9. **Specify Path**
   ```
   Path or File: "Specific Path..."
   
   Text box:
   C:\Code\*
   ```
   
   **Same path as the blocking rule!**
   That's okay - this rule is more specific because it includes a process condition.

10. **Specify Process**
    ```
    Process: "Specific Process..."
    
    Text box:
    C:\Windows\System32\Notepad.exe
    ```
    
    **What this means:**
    "Allow writes to C:\Code\* ONLY when the writing process is Notepad.exe"

11. **Apply to HE Policy**
    ```
    Rule Applies To: Selected policies
    Check: HE
    ```

12. **Save and Wait**
    ```
    Save & Exit
    
    Assets → Computers → Refresh until "Up to date"
    ```

**✅ Success Check:**
- Rule "Permit code writes for the compiler" exists
- Rule Type: Advanced
- Operation: Write
- Write Action: Allow
- Process: C:\Windows\System32\Notepad.exe

---

**🎓 Rule Interaction Visualization:**

```
┌───────────────────────────────────────────────────┐
│  Attempt: Notepad tries to write to C:\Code\      │
│                                                    │
│  Step 1: Check rules for C:\Code\*                │
│                                                    │
│  Found Rules:                                      │
│  ├─ Block code writes (general, all processes)    │
│  └─ Permit code writes... (specific, Notepad only)│
│                                                    │
│  Step 2: Which is more specific?                  │
│  └─ "Permit..." is more specific (has process)    │
│                                                    │
│  Step 3: Apply the matched rule                   │
│  └─ Write Action: Allow                           │
│                                                    │
│  Result: ✅ Write permitted                       │
└───────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────┐
│  Attempt: WordPad tries to write to C:\Code\      │
│                                                    │
│  Step 1: Check rules for C:\Code\*                │
│                                                    │
│  Found Rules:                                      │
│  ├─ Block code writes (matches!)                  │
│  └─ Permit code writes... (doesn't match process) │
│                                                    │
│  Step 2: Apply matched rule                       │
│  └─ Write Action: Block                           │
│                                                    │
│  Result: ❌ Write blocked                         │
└───────────────────────────────────────────────────┘
```

---

## 🔧 Task 5: Verify That Writing New Code Succeeds Because of the Advanced Rule

**What you're doing:** Testing that Notepad can now write to `C:\Code`, but still can't execute the code.

**Expected outcomes:**
- ✅ Save succeeds
- ❌ Execution still blocked

---

**Detailed Steps:**

### Part A: Test Writing

1. **Open Notepad**
   ```
   Start → notepad → Click Notepad
   ```

2. **Write Test Script**
   ```
   Type:
   ping localhost
   ```
   
   **What `localhost` means:**
   - Special name that always means "this computer"
   - IP address: 127.0.0.1
   - Used for testing network stack

3. **Save to Code Directory**
   ```
   File → Save As
   Navigate: C:\Code
   File name: write-unapproved.bat
   Save as type: All Files (*.*)
   Click: Save
   ```
   
   **💚 EXPECTED RESULT:**
   - File saves successfully!
   - No errors
   - No CB Notifier block
   - ✅ Success!

4. **Verify File Exists**
   ```
   Open File Explorer
   Navigate to C:\Code
   ```
   
   **What you see:**
   - `unapproved.bat` (from Task 1)
   - `write-unapproved.bat` (just created)

---

### Part B: Test Execution

5. **Try to Run the Script**
   ```
   In File Explorer (C:\Code)
   Double-click: write-unapproved.bat
   ```
   
   **💥 EXPECTED RESULT:**
   CB Notifier blocks execution:
   ```
   Access Denied
   
   Carbon Black App Control has blocked this action.
   File: C:\Code\write-unapproved.bat
   Process: C:\Windows\explorer.exe
   Policy: HE
   Enforcement: High
   ```

---

**✅ Success Check:**
- ✅ Notepad can create files in C:\Code
- ❌ Double-clicking files is still blocked
- The Advanced Rule works for Write, but not Execute (yet)

---

**🧐 Lab Guide Question:**

**Q: Why are you unable to run write-unapproved.bat?**

**A: Our Advanced Rule only permits the WRITE operation for Notepad.exe. We haven't granted Execute permission yet. When we double-click the file, Windows Explorer (explorer.exe) tries to execute it. The High Enforcement policy blocks this because:**

1. **File State: Unapproved**
   - We just created it, so it's not globally approved

2. **Policy: High Enforcement**
   - Only approved files can execute

3. **No Execute Rule**
   - We have a rule allowing Notepad to WRITE
   - We don't have a rule allowing explorer.exe to EXECUTE

**Solution:** We need to modify our Advanced Rule to include Execute permission (next task).

---

**🎓 Current State Summary:**

```
┌────────────────────────┬──────────┬─────────┐
│ Action                 │ Allowed? │ Why?    │
├────────────────────────┼──────────┼─────────┤
│ Notepad writes to Code │    ✅    │ Adv Rule│
│ WordPad writes to Code │    ❌    │ Block   │
│ Anyone executes files  │    ❌    │ High Enf│
└────────────────────────┴──────────┴─────────┘
```

We're halfway there! Next, we add Execute permission.

---

## 🔧 Task 6: Edit the Advanced Rule to Permit the Execution of Code Compiled in the Code Directory

**What you're doing:** Modifying our Advanced Rule to allow BOTH Write AND Execute operations.

**Why we're doing this:** Developers need to test their code, not just write it!

---

**Background: Combining Operations**

Advanced Rules can control multiple operations simultaneously:

**Single Operation:**
- Write only → Can create/modify files
- Execute only → Can run files

**Combined Operations:**
- Execute AND Write → Can create files AND run them
- Perfect for development environments!

---

**Detailed Steps:**

1. **Navigate to Custom Rules**
   ```
   Rules → Software Rules → Custom tab
   ```

2. **Create NEW Rule** (Lab Guide Says "Click Add Custom Rule")
   ```
   Click: "Add Custom Rule"
   ```
   
   **⚠️ Lab Guide Note:**
   - The guide says to create a NEW rule
   - In practice, you could also edit the existing rule
   - We'll create a new one as instructed for clarity

3. **Fill in Basic Info**
   ```
   Rule name: Permit code execute and writes for compile
   Description: Permit edits to the Code directory and execution by the compiler
   ```

4. **Set Platform**
   ```
   Platform: Windows
   ```

5. **Select Rule Type**
   ```
   Rule Type: Advanced
   ```

6. **Select Operation** (**This is the critical change!**)
   ```
   Operation: Execute and Write
   ```
   
   **🎯 Key difference from Task 4:**
   - Task 4: Operation = Write (only)
   - Task 6: Operation = Execute and Write (both!)

7. **Set Execute Action**
   ```
   Execute Action: Allow
   ```
   
   **New field appears when you select "Execute and Write"**

8. **Set Write Action**
   ```
   Write Action: Allow
   ```

9. **Specify Path**
   ```
   Path or File: "Specific Path..."
   
   Text box:
   C:\Code\*
   ```

10. **Specify Process**
    ```
    Process: "Specific Process..."
    
    Text box:
    C:\Windows\Explorer.exe
    ```
    
    **🎯 Critical distinction:**
    - Previous rule: `Notepad.exe` (for writing code)
    - This rule: `Explorer.exe` (for running code by double-clicking)
    
    **Why Explorer.exe?**
    When you double-click a file:
    ```
    You → Double-click file.bat
         ↓
    Windows Explorer (parent process) → Launches file
    ```
    
    We're giving Explorer.exe permission to execute files from C:\Code

11. **Apply to HE Policy**
    ```
    Rule Applies To: Selected policies
    Check: HE
    ```

12. **Save and Wait**
    ```
    Save & Exit
    
    Assets → Computers → Refresh until "Up to date"
    ```

**✅ Success Check:**
- Rule "Permit code execute and writes for compile" exists
- Operation: Execute and Write
- Execute Action: Allow
- Write Action: Allow
- Process: C:\Windows\Explorer.exe

---

**🎓 Understanding the Three-Rule System:**

Now we have THREE rules working together:

```
┌──────────────────────────────────────────────────────┐
│ Rule 1: "Block code writes"                          │
│   Type: File Creation Control                        │
│   Action: Block ALL writes to C:\Code\*              │
│   Purpose: Default deny                              │
└──────────────────────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────┐
│ Rule 2: "Permit code writes for the compiler"        │
│   Type: Advanced (Write only)                        │
│   Action: Allow Notepad.exe to write to C:\Code\*    │
│   Purpose: Let developers create code                │
└──────────────────────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────┐
│ Rule 3: "Permit code execute and writes for compile" │
│   Type: Advanced (Execute and Write)                 │
│   Action: Allow Explorer.exe to execute from C:\Code\│
│   Purpose: Let developers run code by double-clicking│
└──────────────────────────────────────────────────────┘
```

---

**🤔 Common Questions:**

**Q: Why not combine Rules 2 and 3 into one rule?**
A: We could! But the lab demonstrates building up progressively. In production, you might have:
- One rule for all IDE tools (Visual Studio, VS Code, etc.)
- Separate rule for execution

**Q: What if I want to run scripts from Command Prompt instead of double-clicking?**
A: You'd need another rule with Process: `C:\Windows\System32\cmd.exe`

**Q: Does Explorer.exe also get Write permission from Rule 3?**
A: Yes! But that's okay because Explorer.exe is a trusted Windows component.

**Q: Can malware pretend to be Explorer.exe?**
A: CB checks the file hash. If malware replaced Explorer.exe, the hash wouldn't match the legitimate Explorer.exe, and it would be blocked.

---

## 🔧 Task 7: Verify That Writing New Code Succeeds and Executes Because of the Advanced Rule

**What you're doing:** Testing the complete workflow: Write code → Save code → Run code

**Expected outcome:** Everything works!

---

**Detailed Steps:**

### Part A: Write Code

1. **Open Notepad**
   ```
   Start → notepad → Click Notepad
   ```

2. **Write Test Script**
   ```
   Type:
   ping 10.100.10.100
   ```
   
   **What this does:**
   - Pings the App Control Server
   - You should see replies if network is working

3. **Save File**
   ```
   File → Save As
   Navigate: C:\Code
   File name: write-execute.bat
   Save as type: All Files (*.*)
   Click: Save
   ```
   
   **💚 EXPECTED RESULT:**
   - Saves successfully (because of Rule 2)

### Part B: Execute Code

4. **Navigate to Code Directory**
   ```
   Open File Explorer
   Go to: C:\Code
   ```

5. **Run the Script**
   ```
   Double-click: write-execute.bat
   ```
   
   **💚 EXPECTED RESULT:**
   - A Command Prompt window opens
   - You see ping output:
   ```
   Pinging 10.100.10.100 with 32 bytes of data:
   Reply from 10.100.10.100: bytes=32 time<1ms TTL=128
   Reply from 10.100.10.100: bytes=32 time<1ms TTL=128
   ...
   ```
   - Window closes after ping completes
   - ✅ SUCCESS! The script executed!

**✅ Success Check:**
- File created successfully
- File executed successfully
- No CB Notifier blocks

---

**🧐 Lab Guide Question:**

**Q: Does write-execute.bat run?**

**A: Yes! The script runs successfully because:**

1. **Write Operation (Saving the file):**
   - Advanced Rule "Permit code writes for the compiler"
   - Allows Notepad.exe to write to C:\Code\*
   - ✅ File created

2. **Execute Operation (Running the file):**
   - Advanced Rule "Permit code execute and writes for compile"
   - Allows Explorer.exe to execute files from C:\Code\*
   - ✅ File executed

**Complete Flow:**
```
Developer (You)
    ↓
Opens Notepad → Writes code
    ↓
Notepad.exe attempts write to C:\Code\write-execute.bat
    ↓
CB Agent checks rules → Finds Rule 2 → Allows write
    ↓
File saved ✅
    ↓
Developer double-clicks file
    ↓
Explorer.exe attempts execute of C:\Code\write-execute.bat
    ↓
CB Agent checks rules → Finds Rule 3 → Allows execute
    ↓
Script runs ✅
```

---

**🎓 Real-World Application:**

This three-rule pattern is used in production for:

**Software Development:**
```
Rule: Allow Visual Studio to write to C:\Development\*
Rule: Allow developers to execute from C:\Development\*
Result: Developers can code and test without security exceptions
```

**Data Science:**
```
Rule: Allow Python IDE to write to C:\DataProjects\*
Rule: Allow Python.exe to execute scripts from C:\DataProjects\*
Result: Data scientists can develop and run analysis scripts
```

**DevOps:**
```
Rule

**Q: What if I want to protect just one file?**
A: Use the exact path:
