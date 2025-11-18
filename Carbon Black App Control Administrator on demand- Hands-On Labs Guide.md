# Carbon Black App Control - Hands-On Labs Guide

## Lab Environment Setup

### Prerequisites
Before starting the labs, ensure you have:
- ✅ App Control Server installed and running
- ✅ App Control Client (Windows) with agent installed
- ✅ Network connectivity between server and client
- ✅ Access credentials ready

### Lab Credentials Reference

```
App Control Console:
URL: https://appcontrol/
Username: admin
Password: admin123

Windows Client Desktop:
Username: administrator
Password: VMware1!

App Control Server (if needed):
Username: administrator
Password: Gr95M23rE9
```

---

## Lab 1: Login Accounts

### Learning Objectives
By the end of this lab, you will:
- ✓ Understand user role permissions in Carbon Black App Control
- ✓ Create user accounts with different privilege levels
- ✓ Test and verify role-based access control

### Why This Matters
In a real-world environment, you need different access levels:
- **Administrators** need full control
- **Security analysts** need policy management without system changes
- **Help desk** needs read-only access with specific tool permissions

---

### Task 1: Add User Roles – Security Administrator

**Scenario:** Jamie Appleton is joining your security team and needs full administrative access, just like you have.

**What You'll Learn:** How to create an administrator account with full privileges.

#### Step-by-Step Instructions

**Step 1: Access the Windows Client**
```
1. Log in to App Control Client desktop
   Username: administrator
   Password: VMware1!
```

**Step 2: Open the App Control Console**
```
2. Open Chrome browser
3. Navigate to: https://appcontrol/
4. You'll see a certificate warning (NET::ERR_CERT_AUTHORITY_INVALID)
   - This is expected in a lab environment
   - Click: Advanced
   - Click: Proceed to appcontrol
```

**💡 Understanding:** Certificate warnings occur because the lab uses a self-signed certificate. In production, you'd have a proper SSL certificate from a trusted authority.

**Step 3: Log In to Console**
```
5. Enter credentials:
   Username: admin
   Password: admin123
6. Click: Log In
```

**Step 4: Navigate to User Management**
```
7. At the top right, click: Gear icon (⚙️)
8. Select: Login Accounts
9. In the left navigation pane, click: Users
```

**💡 Understanding:** The Gear icon contains all administrative settings. Login Accounts is where you manage who can access the console.

**Step 5: Create Jamie's Account**
```
10. Click: Add User

11. Fill in the user details:
    Username: jappleton
    Password: $3cur!tY
    Status: Enabled
    First name: Jamie
    Last name: Appleton
    Title: Security Administrator

12. Under User Roles section:
    ☑️ Select: Administrator User Role

13. Click: Create & Exit
```

**💡 Understanding Each Field:**
- **Username:** Login credential (case-sensitive, no spaces)
- **Password:** Must meet complexity requirements ($3cur!tY has special characters, numbers, uppercase)
- **Status: Enabled** means the account is active immediately
- **Administrator User Role** grants ALL permissions

**Verification:**
```
✓ You should see jappleton in the user list
✓ Status column shows: Enabled
✓ User Role column shows: Administrator
```

**What Jamie Can Do:**
```
✅ Create/delete users
✅ Modify system settings
✅ Create/edit policies
✅ Approve/ban files
✅ View all events and reports
✅ Everything you can do
```

---

### Task 2: Add User Roles – Assistant Security Administrator

**Scenario:** Daryl Johnson is your assistant security administrator. Daryl needs to manage policies and approve files, but should NOT be able to create users or change system settings.

**What You'll Learn:** How to assign the **Power User** role, which provides policy management without full system administration.

#### Step-by-Step Instructions

**Step 1: Ensure You're Logged In**
```
1. You should already be in the App Control Console from Task 1
2. If not, log in:
   URL: https://appcontrol/
   Username: admin
   Password: admin123
```

**Step 2: Navigate to Users**
```
3. Gear icon (⚙️) → Login Accounts
4. Left navigation pane → Users
5. Click: Add User
```

**Step 3: Create Daryl's Account**
```
6. Fill in the user details:
   Username: djohnson
   Password: $3cur!tY
   Email address: djohnson@localhost.com
   Status: Enabled
   First name: Daryl
   Last name: Johnson
   Title: Assistant Security Administrator

7. Under User Roles section:
   ☑️ Select: Power User User Role

8. Click: Create & Exit
```

**💡 Understanding: Power User vs Administrator**

| Permission | Administrator | Power User |
|------------|--------------|------------|
| Create/delete users | ✅ Yes | ❌ No |
| Modify system settings | ✅ Yes | ❌ No |
| Create/edit policies | ✅ Yes | ✅ Yes |
| Approve/ban files | ✅ Yes | ✅ Yes |
| View events/reports | ✅ Yes | ✅ Yes |
| Manage rules | ✅ Yes | ✅ Yes |

**Power User is perfect for:**
- Security analysts who manage day-to-day operations
- Team members who approve software requests
- Staff who shouldn't change system configuration

**Verification:**
```
✓ djohnson appears in user list
✓ Status: Enabled
✓ User Role: Power User
```

---

### Task 3: Add User Roles – Help Desk Director

**Scenario:** Jane Ridley is your Help Desk Director. She needs to:
- View all information (read-only)
- Create and manage Meters (usage tracking)
- Create and manage Alerts (notifications)
- **NOT** approve files, create policies, or modify settings

**What You'll Learn:** How to create a **custom user role** with specific permissions.

#### Step-by-Step Instructions

**Part A: Create the Custom Role**

**Step 1: Navigate to User Roles**
```
1. Gear icon (⚙️) → Login Accounts
2. Left navigation pane → User Roles
3. Click: Add User Role
```

**Step 2: Configure the Role**
```
4. At the top, find: Copy Settings From dropdown
   Select: ReadOnly
   
   💡 Why? We want Jane to see everything (ReadOnly base),
   then add specific permissions on top
```

**Step 3: Set Basic Information**
```
5. Fill in:
   Name: Help Desk
   Description: User Role for Help Desk
   Status: Enabled
```

**Step 4: Configure Permissions**
```
6. Scroll down to Permissions section
7. You'll see many permission checkboxes
8. Find and enable:
   ☑️ Tools - Manage meters
   ☑️ Tools - Manage Alerts
   
   💡 Leave everything else as inherited from ReadOnly
```

**💡 Understanding Permissions:**
```
ReadOnly provides:
- View computers
- View events
- View policies
- View files
- NO editing capability

We're adding:
- Create/edit/delete meters
- Create/edit/delete alerts
```

**Step 5: Set Policy Scope**
```
9. Find: Scope of Policy Permissions
10. Select: All Current and Future policies
11. Click: Create & Exit
```

**💡 Understanding Policy Scope:**
- **All policies:** Jane can see data from all policies
- **Selected policies:** Would limit her view to specific policies only

**Verification:**
```
✓ "Help Desk" appears in User Roles list
✓ Status: Enabled
```

**Part B: Create Jane's User Account**

**Step 6: Navigate Back to Users**
```
12. Left navigation pane → Users
13. Click: Add User
```

**Step 7: Create Jane's Account**
```
14. Fill in:
    Username: jridley
    Password: $3cur!tY
    Email address: jridley@localhost.com
    Status: Enabled
    First name: Jane
    Last name: Ridley
    Title: Help Desk Director

15. Under User Roles:
    ☑️ Select: Help Desk

16. Click: Create & Exit
```

**Verification:**
```
✓ jridley appears in user list
✓ Status: Enabled
✓ User Role: Help Desk
```

**What Jane Can Do:**
```
✅ View all computers, events, files
✅ Create/manage meters (track software usage)
✅ Create/manage alerts (get notified of events)
❌ Cannot approve files
❌ Cannot create policies
❌ Cannot create users
❌ Cannot modify system settings
```

---

### Task 4: Test the Administrative Limits of the New User Role

**What You'll Learn:** How to verify that role-based access control is working correctly.

#### Test 1: Verify Daryl's Limitations (Power User)

**Step 1: Log Out as Admin**
```
1. In the console, top right corner
2. Click: admin dropdown (or account name)
3. Select: Log Out
4. Confirm: Yes
```

**Step 2: Log In as Daryl**
```
5. Login page appears
6. Enter credentials:
   Username: djohnson
   Password: $3cur!tY
7. Click: Log In
```

**Step 3: Try to Create a User**
```
8. Look for the Gear icon (⚙️) at top right
   
   Question: Do you see it?
   Answer: YES - Power Users can access some admin functions

9. Click: Gear icon → Login Accounts
10. Left pane → Users
11. Try to click: Add User

    Question: What happens?
    Expected: You DON'T see the "Add User" button
    OR: You see it but get an "Access Denied" error
```

**💡 Understanding:**
```
Daryl (Power User) can:
✅ Access Assets → Computers
✅ Access Rules → Software Rules
✅ Access Reports → Events
✅ Approve/ban files

Daryl CANNOT:
❌ Create/delete users
❌ Access advanced system settings
❌ Modify server configuration
```

**Step 4: Verify What Daryl CAN Do**
```
12. Click: Assets → Computers
    Result: ✓ Can view computers

13. Click: Rules → Software Rules
    Result: ✓ Can view and create rules

14. Click: Reports → Events
    Result: ✓ Can view events
```

**Step 5: Log Out as Daryl**
```
15. Top right → djohnson dropdown → Log Out
16. Click: Yes
```

#### Test 2: Verify Jane's Limitations (Help Desk - Read Only + Specific Tools)

**Step 6: Log In as Jane**
```
17. Enter credentials:
    Username: jridley
    Password: $3cur!tY
18. Click: Log In
```

**Step 7: Check Interface Differences**
```
19. Look at the top navigation bar

    Question: Do you see the Gear icon (⚙️)?
    Answer: NO - Read-only users don't see admin settings
```

**💡 Understanding:**
Jane's interface is simplified. She only sees:
- **Assets** (view only)
- **Rules** (view only)
- **Reports** (view only)
- **Tools** (can create/manage meters and alerts)

**Step 8: Verify Jane Can Create Meters**
```
20. Click: Tools → Meters
21. Click: Add Meter
    
    Result: ✓ She CAN create meters (special permission)
    
22. Cancel (don't create anything yet)
```

**Step 9: Verify Jane Can Create Alerts**
```
23. Click: Tools → Alerts
24. Click: Add Alert
    
    Result: ✓ She CAN create alerts (special permission)
    
25. Cancel (don't create anything yet)
```

**Step 10: Verify Jane CANNOT Approve Files**
```
26. Click: Reports → Events
27. Saved Views → Blocked Files (All)
28. Try to find approval options

    Result: ❌ No approval buttons available
    Jane can VIEW blocked files but cannot approve them
```

**Step 11: Log Out as Jane**
```
29. Top right → jridley dropdown → Log Out
30. Click: Yes
```

#### Test 3: Log Back In as Admin

**Step 12: Return to Admin Account**
```
31. Enter credentials:
    Username: admin
    Password: admin123
32. Click: Log In
```

---

### Lab 1 Summary & Review

#### What You Accomplished
```
✅ Created 3 user accounts with different permission levels
✅ Understood the Administrator role (full access)
✅ Understood the Power User role (policy management, no system admin)
✅ Created a custom role (Help Desk - read-only + specific tools)
✅ Tested and verified role-based access control works correctly
```

#### Key Concepts Review

**1. User Role Hierarchy:**
```
Administrator (Full Control)
    ↓
Power User (Policy Management)
    ↓
Custom Roles (Specific Permissions)
    ↓
Read Only (View Only)
```

**2. When to Use Each Role:**
```
Administrator:
- Senior security team members
- System administrators
- People who manage the App Control system itself

Power User:
- Security analysts
- Day-to-day policy managers
- Staff who handle approval requests

Custom Roles:
- Help desk teams
- Compliance auditors
- Management (view-only reports)
- Anyone needing specific tool access
```

**3. Real-World Application:**
```
In production, you would:
1. Create policies first (Lab 2)
2. Then create user accounts with appropriate roles
3. Assign users to specific policies if needed
4. Regularly audit user permissions
5. Remove accounts when staff leave
```

#### Troubleshooting Common Issues

**Issue 1: Can't see Gear icon**
```
Symptom: Logged in but no Gear icon
Cause: Account doesn't have admin permissions
Solution: Verify user role assignment
```

**Issue 2: "Access Denied" when trying actions**
```
Symptom: Button visible but error when clicking
Cause: Permission changed after login
Solution: Log out and log back in
```

**Issue 3: User created but can't log in**
```
Symptom: "Invalid credentials" error
Cause: 
- Username/password typo
- Status set to "Disabled"
- Account not saved properly
Solution: 
- Verify credentials (case-sensitive)
- Check Status is "Enabled"
- Recreate if necessary
```

#### Questions to Test Your Understanding

1. **Q:** Why shouldn't everyone have Administrator role?
   **A:** Security principle of least privilege - users should only have permissions they need for their job role.

2. **Q:** Can you have multiple user roles assigned to one account?
   **A:** Yes! You can select multiple roles, and the account gets the combined permissions.

3. **Q:** What's the difference between Power User and a custom role with similar permissions?
   **A:** Power User is predefined by Carbon Black. Custom roles let you choose exactly which permissions to grant.

4. **Q:** Why did we create Help Desk role based on ReadOnly?
   **A:** Starting with ReadOnly ensures Jane can view everything, then we add specific management permissions (meters/alerts) on top.

---

### Preparation for Lab 2

In Lab 2, you'll create **Policies** that define how strict security enforcement is for different groups of computers. Understanding user roles first is important because:
- Different roles can create policies
- Policies can be scoped to specific user roles
- You'll test policies while logged in as different users

Make sure you understand the user accounts you just created - you'll use them throughout the remaining labs!

---

**🎯 Lab 1 Complete!**

You now understand how to manage users and roles in Carbon Black App Control. This foundation is critical before moving to policies and rules.

Continue to **Lab 2: Policies** →

___________________________________________________________________________________________________________________________________________________

# Lab 2: Policies

### Learning Objectives
By the end of this lab, you will:
- ✓ Understand what policies are and why they're critical
- ✓ Create policies with different enforcement levels
- ✓ Understand Connected vs. Disconnected enforcement
- ✓ Test how policies affect software execution
- ✓ Handle user approval requests

### Why This Matters
**Policies are the foundation of App Control security.** They determine:
- How strict security is for different computer groups
- What happens when users try to run unapproved software
- How protection changes when laptops leave the office

---

## Understanding Policies - Critical Concepts

### What is a Policy?

Think of a policy as a **security blueprint** for a group of computers.

```
Policy = Security Rules for a Group of Computers

Example:
┌─────────────────────────────┐
│   DMZ Policy (Maximum)      │
│   ├─ Web servers            │
│   ├─ High enforcement       │
│   └─ Block everything       │
└─────────────────────────────┘

┌─────────────────────────────┐
│   Standard Corporate        │
│   ├─ Office workstations    │
│   ├─ Flexible at office     │
│   └─ Locked down when away  │
└─────────────────────────────┘
```

### Enforcement Levels - The Core Concept

**This is THE most important concept in App Control:**

```
┌──────────────────────────────────────────────────┐
│  HIGH ENFORCEMENT (Maximum Security)             │
│  ─────────────────────────────────────────────  │
│  ✅ Approved files: RUN                          │
│  ❌ Unapproved files: BLOCKED                    │
│  ❌ Banned files: BLOCKED                        │
│  Use for: Production servers, DMZ, high-risk     │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│  LOW ENFORCEMENT (Monitoring Mode)               │
│  ─────────────────────────────────────────────  │
│  ✅ Approved files: RUN                          │
│  ✅ Unapproved files: RUN (but logged)           │
│  ❌ Banned files: BLOCKED                        │
│  Use for: Development, testing, monitoring       │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│  NONE (Disabled)                                 │
│  ─────────────────────────────────────────────  │
│  ✅ Everything runs (minimal tracking)           │
│  ❌ Only banned files blocked                    │
│  Use for: Special cases only                     │
└──────────────────────────────────────────────────┘
```

### Connected vs. Disconnected - The Laptop Scenario

**This is a brilliant feature for laptop security:**

```
SCENARIO: Sales laptop

┌────────────────────────────────────────────┐
│  AT THE OFFICE (Connected to server)      │
│  ─────────────────────────────────────────│
│  Enforcement: LOW                          │
│  User can install needed software          │
│  IT can see what's happening               │
│  Everything is logged                      │
└────────────────────────────────────────────┘
           ↓
    User travels
           ↓
┌────────────────────────────────────────────┐
│  ON THE ROAD (Disconnected from server)    │
│  ─────────────────────────────────────────│
│  Enforcement: HIGH                         │
│  Only approved software runs               │
│  Protected from malware at hotel WiFi     │
│  Cached policy still enforced              │
└────────────────────────────────────────────┘
```

**💡 Key Understanding:**
- The agent **automatically switches** enforcement based on connectivity
- This gives flexibility where it's safe (office) and protection where it's risky (public networks)
- The policy is **cached locally** so enforcement continues even offline

---

## Task 1: Create a Security IT Endpoint Policy

### Scenario
Your security team (you and your colleagues) need computers for:
- Testing new software
- Analyzing suspicious files
- Creating approval rules
- Building lab environments

**Requirements:**
- Must run ANY software (for testing)
- Must track ALL activity (to create rules later)
- Low security restrictions (trusted team)

### Step-by-Step Instructions

**Step 1: Ensure You're Logged In**
```
1. You should be logged in as admin from Lab 1
2. If not:
   Open Chrome → https://appcontrol/
   Username: admin
   Password: admin123
```

**Step 2: Navigate to Policies**
```
3. Top navigation → Rules drop-down menu
4. Select: Policies
```

**💡 Understanding:** Policies are under "Rules" because they work together with other rules to control software execution.

**Step 3: Create New Policy**
```
5. Click: Add Policy
```

**Step 4: Configure Basic Settings**
```
6. Fill in:
   Policy name: Security IT Endpoints
   Description: Policy for Security IT
```

**💡 Naming Convention Best Practice:**
```
Good policy names:
✓ Security IT Endpoints (describes WHO it's for)
✓ DMZ Web Servers (describes WHERE)
✓ Finance Department Windows 10 (describes WHAT)

Bad policy names:
✗ Policy1
✗ Test
✗ NewPolicy
```

**Step 5: Set Mode**
```
7. Mode: Control

   💡 Understanding Mode:
   
   Visibility Mode:
   - Agent monitors only
   - No enforcement
   - Used during initial deployment
   
   Control Mode:
   - Full enforcement capability
   - Can block or allow
   - Production mode
```

**Step 6: Configure Enforcement Levels**
```
8. Set Enforcement Levels:
   Connected: Low
   Disconnected: Low
```

**💡 Why Both Low?**
```
Security IT team needs flexibility:
- Test malware samples (safely)
- Install beta software
- Run unapproved tools
- Even when traveling (they know the risks)

Everything is still LOGGED, just not BLOCKED
```

**Step 7: Set Initial Settings**
```
9. Initial Settings dropdown: Template Policy

   💡 Understanding Initial Settings:
   
   Template Policy:
   - Includes common application approvals
   - Microsoft, Adobe, etc. already approved
   - Good starting point
   
   Default Policy:
   - Minimal approvals
   - Most restrictive starting point
   
   Clone from existing:
   - Copy settings from another policy
```

**Step 8: Enable Tracking**
```
10. Verify checkbox is selected:
    ☑️ Track File Changes
    
    💡 Why Track File Changes?
    - Logs when files are modified
    - Helps detect malware activity
    - Required for baseline drift reports
    - Small performance overhead
```

**Step 9: Save the Policy**
```
11. Click: Save & Exit
```

**Verification:**
```
✓ Policy "Security IT Endpoints" appears in policy list
✓ Mode: Control
✓ Connected Enforcement: Low
✓ Disconnected Enforcement: Low
```

---

## Task 2: Create a Policy for Standard Corporate Endpoints

### Scenario
Your standard office workers have:
- Desktop computers in the office
- Some have laptops they take home

**Requirements:**
- Flexible when at office (can install approved software)
- Locked down when traveling (security risk)
- Balance security with usability

### Step-by-Step Instructions

**Step 1: Navigate to Policies**
```
1. Ensure logged in as admin
2. Rules → Policies
3. Click: Add Policy
```

**Step 2: Configure Policy Name**
```
4. Fill in:
   Policy Name: Standard Corporate
   
   (Description is optional, but recommended:
   Description: Policy for standard office workers)
```

**Step 3: Accept Defaults and Save**
```
5. Click: Save & Exit

   💡 Notice: We're accepting ALL defaults!
```

**💡 Understanding the Defaults:**
```
When you create a policy without changing settings:

Mode: Control (default)
Connected: Low (default)
Disconnected: High (default) ← This is the key!
Initial Settings: Default Policy
Track File Changes: Selected (default)
```

**Why These Defaults Are Perfect for Corporate:**
```
Connected: LOW
└─ User at office
└─ Installs PDF reader → Runs (logged)
└─ IT reviews logs later
└─ Can approve if legitimate

Disconnected: HIGH
└─ User at coffee shop
└─ Malicious WiFi tries to install malware → BLOCKED
└─ Only pre-approved software runs
└─ Laptop is protected
```

**Verification:**
```
✓ Policy "Standard Corporate" appears in list
✓ Connected: Low
✓ Disconnected: High (automatically set)
```

**Real-World Example:**
```
Sarah (Sales Manager) has a laptop:

Monday (at office):
- Downloads new CRM software → Runs (Low enforcement)
- App Control logs the installation
- IT sees it in events, approves it globally

Tuesday (at client site):
- Disconnected from office network
- Enforcement switches to HIGH automatically
- Tries to download file from suspicious email → BLOCKED
- Only pre-approved CRM and Office software work
- Sarah is protected without knowing it
```

---

## Task 3: Create a Policy for Your Perimeter DMZ Sub Network

### Scenario
Your DMZ contains:
- Public-facing web servers
- Application servers accessible from internet
- Highest risk systems in your environment

**Requirements:**
- **MAXIMUM security at all times**
- **ZERO tolerance** for unapproved software
- Never should need new software (stable environment)
- Always connected to your network (not laptops)

### Step-by-Step Instructions

**Step 1: Create the Policy**
```
1. Rules → Policies
2. Click: Add Policy
```

**Step 2: Configure Maximum Security**
```
3. Fill in:
   Policy Name: DMZ
   Description: Policy for DMZ Sub Network

4. Mode: Control

5. Set Enforcement Levels:
   Connected: High
   Disconnected: High
   
   💡 Why both High?
   - These servers are always connected
   - Always maximum risk (internet-facing)
   - No need for flexibility
   - Any unexpected software = security incident
```

**Step 3: Ensure Tracking Enabled**
```
6. Verify:
   ☑️ Track File Changes
   
   💡 Critical for servers:
   - Track any modifications
   - Detect compromise attempts
   - Audit trail for compliance
```

**Step 4: Save**
```
7. Click: Save & Exit
```

**Verification:**
```
✓ Policy "DMZ" created
✓ Connected: High
✓ Disconnected: High
✓ Track File Changes: Enabled
```

**Understanding DMZ Security:**
```
Before App Control:
├─ Antivirus on DMZ server
├─ Firewall rules
├─ Regular patching
└─ Hope nothing bad happens

With App Control (DMZ Policy):
├─ Only approved web server software runs
├─ Apache/IIS updates must be approved first
├─ ANY other executable → BLOCKED immediately
├─ Attacker gains access but can't run malware
└─ "Zero-day" exploit can't execute payload
```

---

## Task 4: Testing Policies

### What You'll Learn
- How to assign a computer to a policy
- What happens when enforcement blocks software
- How users see blocks
- The approval request process

### Step-by-Step Instructions

**Step 1: Verify Login**
```
1. Logged in as: admin
2. In App Control Console
```

**Step 2: Navigate to Computers**
```
3. Top navigation → Assets drop-down
4. Select: Computers
```

**💡 Understanding the Computers View:**
```
You'll see a list of all computers with agents installed:
- Computer name
- Policy currently assigned
- Policy status (up to date, pending, etc.)
- Last check-in time
- Agent version
```

**Step 3: Assign Computer to DMZ Policy**
```
5. Find: WORKGROUP\WINDOWS-CLIENT
6. Select its checkbox (left side)
7. Action dropdown → Move Computer to Policy: DMZ
```

**💡 Understanding:**
```
You're moving the Windows Client to the strictest policy
This simulates a DMZ server environment
Perfect for testing blocking behavior
```

**Step 4: Confirm Policy Change**
```
8. Confirmation dialog appears:
   "Are you sure you want to move this computer?"
9. Click: OK
```

**Step 5: Wait for Policy to Apply**
```
10. Watch the Policy Status column for WINDOWS-CLIENT
11. Click: Refresh (top of page)
12. Keep refreshing until status shows: "Up to date"

    💡 What's happening behind the scenes:
    1. Server queues policy change
    2. Agent checks in (usually within 60 seconds)
    3. Agent downloads new policy
    4. Agent applies enforcement rules
    5. Status updates to "Up to date"
```

**Timeline:**
```
00:00 - Policy change initiated
00:05 - Agent checks in with server
00:10 - New policy downloaded
00:15 - Enforcement applied
00:20 - Status: "Up to date"
```

**Step 6: Switch to Windows Client Desktop**
```
13. Minimize browser (keep console open)
14. On Windows Client desktop, locate: Downloads ZIP file
15. Right-click: Downloads.zip
16. Select: Extract All
17. Check: ☑️ Show extracted files when complete
18. Click: Extract
```

**💡 Understanding:**
```
This ZIP contains software installers:
- Notepad++ (text editor)
- PuTTY (SSH client)
- WinSCP (file transfer)
- Wireshark (network analyzer)

All are legitimate tools, but:
- Not yet approved in App Control
- Will be blocked in HIGH enforcement
```

**Step 7: Test Blocking - Notepad++**
```
19. In extracted Downloads folder, find:
    npp.7.8.5.Installer.exe

20. Double-click: npp.7.8.5.Installer.exe
```

**💡 What Happens:**
```
┌─────────────────────────────────────────┐
│  Security Notification                  │
│  ─────────────────────────────────────  │
│  This file is not approved and has      │
│  been prevented from running.           │
│                                          │
│  File: npp.7.8.5.Installer.exe          │
│  Publisher: Notepad++ Team              │
│  Trust Level: 90                        │
│  Threat Level: 0                        │
│                                          │
│  Request Approval                       │
│  └─ Reason: [text box]                  │
│  └─ Email: [text box]                   │
│  └─ [Submit Request]                    │
└─────────────────────────────────────────┘
```

**Understanding the Notification:**
```
THIS IS APP CONTROL WORKING!

File: npp.7.8.5.Installer.exe
├─ User tried to run it
├─ Policy: DMZ (HIGH enforcement)
├─ File state: Unapproved
├─ Action: BLOCKED

Trust Level: 90 (high - legitimate software)
Threat Level: 0 (no malware indicators)

But still blocked because:
└─ HIGH enforcement = only approved files run
└─ This file is not approved (yet)
```

**Step 8: Submit Approval Request**
```
21. In the Security Notification window:
    
22. Reason text box, enter:
    "Need for code editing and script development"

23. Email text box, enter:
    admin@localhost.com

24. Click: Submit Request
```

**💡 Understanding Approval Requests:**
```
What just happened:
1. User was blocked
2. User requests permission
3. Request goes to specified email (admin)
4. Admin reviews and approves/denies
5. If approved, user can then run software

This workflow:
✓ Maintains security (blocks by default)
✓ Provides flexibility (users can request)
✓ Creates audit trail (who requested, why)
✓ Puts decision in admin hands
```

**Step 9: Test PuTTY - The Mystery!**
```
25. In the same Downloads folder:
26. Double-click: putty.exe

    Question: What happens?
    Expected: Should be blocked (same as Notepad++)
    Actual: IT RUNS! 🤔
```

**💡 The PuTTY Mystery:**
```
Why did PuTTY run in HIGH enforcement?

This is intentional for teaching!
You'll solve this mystery in Lab 3, Task 6

Hint: Think about agent initialization...
When the agent first installed, what files did it find?

We'll investigate this fully later.
For now, just note: PuTTY runs even though it shouldn't.
```

---

## Task 5: Responding to Application Approval Requests

### What You'll Learn
- How admins receive approval requests
- How to review file details before approving
- How to approve files locally vs. globally
- How users experience approved software

### Step-by-Step Instructions

**Step 1: Return to App Control Console**
```
1. Switch back to Chrome browser
2. You should still be logged in as admin
```

**Step 2: Navigate to Approval Requests**
```
3. Top navigation → Tools drop-down
4. Select: Approval Requests
```

**💡 Understanding the Approval Requests View:**
```
This is your "inbox" for software requests:
- What: File name and details
- Who: User who requested
- When: Timestamp
- Why: Their justification
- Where: Computer name
- Trust/Threat: Security assessment
```

**Step 3: View Request Details**
```
5. Find: npp.7.8.5.Installer.exe request
6. Click: View Details icon (📄 paper and pencil)
   Located next to the checkbox
```

**Step 4: Review the Request**
```
7. Click: Open Request button
```

**You'll see detailed information:**
```
┌────────────────────────────────────────────┐
│  Approval Request Details                   │
│  ──────────────────────────────────────────│
│  File: npp.7.8.5.Installer.exe             │
│  Computer: WORKGROUP\WINDOWS-CLIENT        │
│  User: Administrator                        │
│  Submitted: [timestamp]                     │
│  Email: admin@localhost.com                 │
│                                             │
│  Justification:                             │
│  "Need for code editing and script          │
│   development"                              │
│                                             │
│  FILE ANALYSIS:                             │
│  Trust Level: 90 (High)                     │
│  Threat Level: 0 (None)                     │
│  Publisher: Notepad++ Team                  │
│  Prevalence: 50,000+ installations          │
│  First Seen: 2019-01-15                     │
│                                             │
│  ACTIONS: (right side)                      │
│  → Approve File Locally                     │
│  → Approve File Globally                    │
│  → Deny Request                             │
│  → Request More Information                 │
└────────────────────────────────────────────┘
```

**Step 5: Analyze Before Approving**
```
Review the Justification Details:

Trust Level: 90
└─ This is HIGH (scale 0-100)
└─ Notepad++ is well-known, legitimate software
└─ Safe to approve

Threat Level: 0
└─ No malware indicators
└─ No suspicious behavior
└─ Clean file

Publisher: Notepad++ Team
└─ Verified digital signature
└─ Reputable developer

Justification: "Need for code editing..."
└─ Makes sense
└─ Legitimate business need
└─ Not suspicious
```

**💡 Decision Matrix:**
```
Should you approve?

✅ YES, approve if:
- High trust (80+)
- Low threat (0-20)
- Valid business justification
- Reputable publisher
- Widely used software

❌ NO, deny if:
- Low trust (<50)
- High threat (>50)
- No business justification
- Unknown publisher
- Request seems suspicious

⚠️ INVESTIGATE if:
- Moderate trust (50-80)
- Request doesn't match user's role
- Multiple requests for same file
- Unusual file name or location
```

**Step 6: Approve Locally**
```
8. On the right side, under Action links
9. Click: Approve File Locally
```

**💡 Understanding: Local vs. Global Approval**
```
LOCAL APPROVAL:
- File runs on THIS computer only
- Hash approved for WORKGROUP\WINDOWS-CLIENT
- Other computers still block it
- Use when: One person needs it, testing phase

GLOBAL APPROVAL:
- File runs on ALL computers
- Hash approved everywhere
- Any policy can run it (unless banned)
- Use when: Company-wide software
```

**For this lab, Local Approval is appropriate because:**
```
✓ We're testing
✓ Only one computer affected
✓ Can expand to global later if needed
✓ Safer approach (least privilege)
```

**Step 7: Confirm Approval**
```
10. Comment popup appears
11. You can add a comment (optional):
    "Approved for testing purposes"
12. Click: OK
```

**💡 What Just Happened Behind the Scenes:**
```
1. File hash added to local approval list
2. WINDOWS-CLIENT agent notified
3. Next agent check-in (seconds)
4. Agent updates local policy cache
5. File now allowed to run on this computer
```

**Step 8: Test the Approval - Return to Windows Client**
```
13. Switch to Windows Client desktop
14. Navigate to: Downloads folder
15. Double-click: npp.7.8.5.Installer.exe
```

**Result:**
```
✅ Notepad++ installer runs!
✅ No security notification
✅ Installation proceeds normally
```

**Step 9: Install Notepad++**
```
16. Installation wizard appears
17. Click: OK (accept defaults)
18. When prompted:
    ☑️ Create a shortcut on the desktop
19. Continue with default options
20. Click: Install
21. Wait for installation to complete
22. Click: Finish
```

**Step 10: Verify Application Works**
```
23. On desktop, find: Notepad++ shortcut
24. Double-click: Notepad++
25. Application opens successfully
```

**💡 Understanding:**
```
Notepad++ now works because:
1. Installer (npp.7.8.5.Installer.exe) was locally approved
2. During installation, Notepad++ wrote files:
   - notepad++.exe (main application)
   - plugins (DLLs)
   - support files
3. App Control tracked these new files
4. All files from the installer inherit "trusted" status
5. Main application can now run

This is called "Promotion":
Installer approved → Files it creates are trusted
```

---

## Task 5 Summary - The Approval Workflow

**Complete Workflow Recap:**

```
┌─────────────────────────────────────────────────────┐
│  1. USER ATTEMPTS TO RUN SOFTWARE                   │
│     └─ Double-click npp.7.8.5.Installer.exe        │
└────────────────┬────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────┐
│  2. APP CONTROL EVALUATES                           │
│     └─ Policy: DMZ (HIGH enforcement)              │
│     └─ File state: Unapproved                      │
│     └─ Decision: BLOCK                             │
└────────────────┬────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────┐
│  3. USER SEES SECURITY NOTIFICATION                 │
│     └─ File blocked                                │
│     └─ Option to request approval                  │
└────────────────┬────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────┐
│  4. USER SUBMITS REQUEST                            │
│     └─ Reason: Business justification             │
│     └─ Email: admin@localhost.com                  │
└────────────────┬────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────┐
│  5. ADMIN RECEIVES REQUEST                          │
│     └─ Tools → Approval Requests                   │
│     └─ Reviews file details                        │
│     └─ Checks trust/threat levels                  │
└────────────────┬────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────┐
│  6. ADMIN MAKES DECISION                            │
│     └─ Approves locally (this computer)           │
│     └─ Or approves globally (all computers)        │
│     └─ Or denies request                           │
└────────────────┬────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────┐
│  7. FILE NOW APPROVED                               │
│     └─ User tries again                            │
│     └─ Software runs successfully                  │
│     └─ Installation completes                      │
└─────────────────────────────────────────────────────┘
```

---

## Lab 2 Summary & Key Takeaways

### What You Accomplished
```
✅ Created 3 different policies with different security levels
✅ Understood Connected vs. Disconnected enforcement
✅ Moved a computer to a policy
✅ Experienced software being blocked
✅ Submitted and processed an approval request
✅ Installed software after approval
```

### Critical Concepts Review

**1. Policy Enforcement Hierarchy:**
```
HIGH Enforcement (Strictest)
├─ Only approved files run
├─ Unapproved = BLOCKED
├─ Banned = BLOCKED
└─ Use for: Servers, DMZ, high-risk

LOW Enforcement (Flexible)
├─ Approved files run
├─ Unapproved run but LOGGED
├─ Banned = BLOCKED
└─ Use for: Development, monitoring

NONE (Disabled)
├─ Everything runs
├─ Minimal tracking
└─ Use for: Special cases only
```

**2. Three Policies You Created:**
```
Security IT Endpoints:
└─ Connected: LOW, Disconnected: LOW
└─ For: Testing and development
└─ Rationale: Trusted team needs flexibility

Standard Corporate:
└─ Connected: LOW, Disconnected: HIGH
└─ For: Office workers with laptops
└─ Rationale: Balance security and usability

DMZ:
└─ Connected: HIGH, Disconnected: HIGH
└─ For: Internet-facing servers
└─ Rationale: Maximum security always
```

**3. The Approval Process:**
```
Block → Request → Review → Approve → Run

This maintains security while providing flexibility
```

### Real-World Application

**In production, you would:**
```
Week 1-2: Deploy with LOW enforcement
├─ Monitor what software runs
├─ Build approval library
└─ Create rules for common apps

Week 3-4: Approve legitimate software
├─ Review events daily
├─ Approve business applications
└─ Ban unauthorized software

Week 5+: Increase to HIGH enforcement
├─ Gradually tighten security
├─ Maintain approval process
└─ Monitor for legitimate blocks
```

### Common Policy Mistakes to Avoid

```
❌ MISTAKE 1: Starting with HIGH enforcement everywhere
   Problem: Blocks legitimate software, angry users
   Solution: Start LOW, approve software, then increase

❌ MISTAKE 2: Using NONE enforcement
   Problem: No security, defeats the purpose
   Solution: Use LOW if you need flexibility

❌ MISTAKE 3: Same policy for all computers
   Problem: Servers and desktops need different security
   Solution: Multiple policies for different roles

❌ MISTAKE 4: Not handling approval requests quickly
   Problem: Users blocked, can't work, frustrated
   Solution: Check requests daily, approve quickly
```

### Questions to Test Your Understanding

1. **Q:** Why is DMZ policy set to HIGH/HIGH instead of LOW/HIGH?
   **A:** DMZ servers are always high-risk (internet-facing), always connected to network (not laptops), and should never need new software.

2. **Q:** What happens to a laptop's enforcement when it disconnects from the office?
   **A:** If policy is configured Connected:LOW/Disconnected:HIGH, enforcement automatically switches to HIGH when disconnected.

3. **Q:** If you approve a file LOCALLY, will it run on other computers?
   **A:** No. Local approval is computer-specific. Use global approval for company-wide software.

4. **Q:** What's the difference between LOCAL and GLOBAL approval?
   **A:**
   - Local: File runs on one specific computer only
   - Global: File runs on all computers (or all in selected policies)

5. **Q:** Why did PuTTY run even though policy was HIGH enforcement?
   **A:** This mystery will be solved in Lab 3, Task 6!  (Hint: initialization process)

---

### Troubleshooting Common Issues

**Issue 1: Policy Status stuck on "Pending"**
```
Symptom: Policy assigned but status doesn't change to "Up to date"
Cause: Agent not checking in with server
Solutions:
1. Wait 2-3 minutes (agent checks in every 60 seconds)
2. Computer Details → Other Actions → Refresh Policy
3. Check agent service is running on endpoint
4. Verify network connectivity
```

**Issue 2: Software runs when it should be blocked**
```
Symptom: Unapproved file runs in HIGH enforcement
Possible causes:
1. Policy not yet applied (check status)
2. File was approved (check file details)
3. File discovered during initialization (Lab 3)
4. Custom rule allowing it (Lab 4)
Solutions:
1. Verify computer policy assignment
2. Check file approval status
3. Review applicable rules
```

**Issue 3: Can't submit approval request**
```
Symptom: Security notification appears but can't request approval
Cause: File is BANNED (not just unapproved)
Solution: Banned files can't be requested - must be unb

anned by admin first
```

---

**🎯 Lab 2 Complete!**

You now understand how policies control security enforcement and how the approval workflow maintains both security and usability.

### Preparation for Lab 3

In Lab 3, you'll dive deeper into individual computer management:
- Adding descriptions and tags
- Health checks
- Snapshots
- Temporary policy overrides
- **Solving the PuTTY mystery!**

Make sure you understand:
- ✅ How policies enforce security
- ✅ The difference between Connected/Disconnected
- ✅ The approval workflow
- ✅ Your Windows Client is currently in DMZ policy

**Continue to Lab 3: Computer Details** →

___________________________________________________________________________________________________________________________________________-

# Lab 3: Computer Details

### Learning Objectives
By the end of this lab, you will:
- ✓ Understand how to organize and manage individual computers
- ✓ Perform health checks on agents
- ✓ Create snapshots for baseline tracking
- ✓ Use temporary policy overrides for emergency access
- ✓ **Solve the PuTTY mystery from Lab 2**
- ✓ Understand file discovery during agent initialization

### Why This Matters
While policies control groups of computers, you often need to:
- Manage individual computers
- Troubleshoot specific endpoints
- Grant temporary exceptions
- Track changes over time
- Understand why files behave unexpectedly

---

## Task 1: Add a Description and Tag to the Windows Client Endpoint

### Scenario
You have hundreds or thousands of computers in your environment. You need ways to:
- Identify computers by purpose
- Organize computers by location or department
- Add notes for future reference

### What You'll Learn
- How to add descriptive information to computers
- How to use tags for organization
- How to search for files on specific computers
- How to analyze file details

### Step-by-Step Instructions

**Step 1: Navigate to Computer List**
```
1. Ensure logged in as: admin
2. Top navigation → Assets drop-down
3. Select: Computers
```

**Step 2: Open Computer Details**
```
4. Find: WORKGROUP\WINDOWS-CLIENT in the list
5. Click: View Details icon (📄 paper and pencil)
   Located next to the checkbox on the left
```

**💡 Understanding the Computer Details Page:**
```
This is the "hub" for managing an individual computer:
- General Information (name, policy, agent version)
- Actions (snapshots, policy override, health check)
- Related Views (files, events, instances)
- Advanced options (other actions, diagnostics)
```

**Step 3: Add Description**
```
6. At the top of the Computer Details page
7. Find the Description field
8. Enter: Pilot Deployment Test Endpoint

   💡 Why add descriptions?
   - Quick identification of computer purpose
   - Notes for other admins
   - Documentation of special configurations
```

**Example Descriptions:**
```
Good descriptions:
✓ "Finance Department - Jane Smith's laptop"
✓ "DMZ Web Server - Production - Apache"
✓ "Development Workstation - Python Testing"
✓ "CEO Executive Laptop - High Priority"

Less helpful:
✗ "Computer 1"
✗ "Test"
✗ "Windows"
```

**Step 4: Add Computer Tag**
```
9. Find the Computer Tag field
10. Enter: 02

    💡 Understanding Computer Tags:
    Tags are short identifiers for grouping/searching:
    - Building numbers: 01, 02, 03
    - Department codes: FIN, HR, IT
    - Asset numbers: AST-1001
    - Location codes: NYC-3F-12
```

**Step 5: Save Changes**
```
11. Click: Save button
```

**Verification:**
```
✓ Description field shows: "Pilot Deployment Test Endpoint"
✓ Computer Tag field shows: "02"
✓ Changes saved successfully
```

---

### Exploring Files on This Computer

Now let's look at what files are on this computer and analyze Notepad++ that we installed in Lab 2.

**Step 6: View Files on Computer**
```
12. On the right side of Computer Details page
13. Under "Related Views" section
14. Click: Files on this Computer
```

**💡 What This Shows:**
```
A list of ALL tracked files on this computer:
- File names
- Paths
- Approval status
- Trust/Threat levels
- Publishers
- File types

This is EXTREMELY useful for:
- Investigating what software is installed
- Finding unapproved files
- Analyzing suspicious files
- Checking software versions
```

**Step 7: Filter by Publisher**
```
15. At the top, click: Add filter dropdown
16. Select: Publisher
    (Creates a filter line below)

17. Second dropdown (after "Publisher"): select "is"
18. Text box: enter "Notepad++"
19. Click: Apply
```

**💡 Understanding Filters:**
```
Filters help you find specific files among thousands:

Common filters:
- File Name (contains, is, starts with)
- Publisher (who signed the file)
- Path (where file is located)
- Approval Status (approved, unapproved, banned)
- Trust Level (high, medium, low)
- Computer (which computer)
```

**Step 8: Review Results**
```
20. You should see Notepad++ files:
    - npp.7.8.5.Installer.exe (the installer)
    - notepad++.exe (main application)
    - Various plugins and DLLs
```

**💡 Answer These Questions (Think Before Looking Ahead):**

**Question 1: What is the trust level of the installer?**
```
Look at the Trust column for npp.7.8.5.Installer.exe

Expected: 85-95 (High)

Why?
- Notepad++ is well-known software
- Used by millions
- Carbon Black reputation service knows it
- Legitimate publisher signature
```

**Question 2: What is the threat level of all files associated with Notepad++?**
```
Look at the Threat column

Expected: 0 or very low (0-5)

Why?
- No malware indicators
- Clean software
- Reputable source
- No suspicious behavior patterns
```

**Question 3: What is the Local Approval status of this file and why?**
```
Look at the Approval Status column

Expected: Locally Approved

Why?
- You approved it in Lab 2, Task 5
- You clicked "Approve File Locally"
- Approval applies to THIS computer only
```

**Question 4: Can Notepad++ run on all endpoints running App Control Agents currently? Why or why not?**
```
Answer: NO

Why?
- Local approval = ONE computer only
- Approved on: WORKGROUP\WINDOWS-CLIENT
- Other computers: Still blocked

To allow on all computers:
- Would need Global Approval
- Or Software Rule for all policies
- Or Publisher approval for Notepad++ Team
```

---

## Task 2: Schedule a Health Check

### Scenario
An agent isn't behaving correctly:
- Not checking in
- Policy not applying
- Files not being tracked

You need to verify the agent is functioning properly.

### What You'll Learn
- How to run agent health checks
- What health checks verify
- How to interpret results
- Where to view health check history

### Step-by-Step Instructions

**Step 1: Navigate to Computer Details**
```
1. If not already there:
   Assets → Computers
   Click: View Details for WORKGROUP\WINDOWS-CLIENT

2. Or use the breadcrumb trail at top:
   Click: Computer Details (WORKGROUP\WINDOWS-CLIENT)
```

**💡 Understanding Breadcrumbs:**
```
Top of page shows navigation path:
Computers > Computer Details (WORKGROUP\WINDOWS-CLIENT) > ...

Click any part to navigate back:
- Computers: Back to computer list
- Computer Details: Back to this computer's main page
```

**Step 2: Access Other Actions**
```
3. On the right side, under "Advanced" section
4. Click: Other Actions link
```

**💡 What's in Other Actions:**
```
Available actions for this computer:
☐ Run health check
☐ Refresh policy
☐ Scan for new files
☐ Reset agent
☐ And more...

These are administrative commands sent to the agent
```

**Step 3: Run Health Check**
```
5. Select checkbox: ☑️ Run health check
6. Click: Go button
```

**💡 What Just Happened:**
```
1. Server queued health check command
2. Agent receives command on next check-in (60 seconds)
3. Agent performs self-diagnostic tests:
   ✓ Service running?
   ✓ Database connection?
   ✓ Policy current?
   ✓ File cache valid?
   ✓ Communication with server?
4. Agent reports results back to server
5. Results appear in events
```

**Step 4: View Health Check Events**
```
7. On the right side, under "Related Views"
8. Click: Health Check Events
```

**Step 5: Wait for Results**
```
9. Click: Refresh Page link (top of page)
10. Keep clicking Refresh every 10-15 seconds
11. Wait until results appear in the Description column

    ⏱️ This can take 1-2 minutes:
    - Agent must check in
    - Perform diagnostic
    - Report back
```

**💡 Be Patient:**
```
If results don't appear immediately:
- Wait 2 full minutes
- Keep refreshing
- Agent checks in every 60 seconds
- Health check takes 15-30 seconds to run
```

**Step 6: Review Results**
```
12. Look at the Description column

Expected results:
✅ "Health check passed"
✅ "Agent service: Running"
✅ "Policy: Up to date"
✅ "Communication: OK"
✅ "File cache: Current"
```

**💡 Answer This Question:**

**Question: What are the results of the health check from the Description column?**
```
Expected: All checks passed

Meaning:
✅ Agent service is running properly
✅ Can communicate with server
✅ Policy is synchronized
✅ File tracking database is healthy
✅ No errors detected

If you saw errors:
❌ "Agent service: Not responding"
❌ "Policy: Out of sync"
❌ "Communication: Failed"
→ Would indicate agent problems needing investigation
```

**Real-World Health Check Scenarios:**
```
Scenario 1: Agent not checking in
- Run health check
- Result: Communication failed
- Action: Check network, firewall, DNS

Scenario 2: Policy not applying
- Run health check
- Result: Policy out of sync
- Action: Refresh policy, restart agent

Scenario 3: Files not being tracked
- Run health check
- Result: File cache corrupted
- Action: Reset file cache, rescan

Scenario 4: Performance issues
- Run health check
- Result: Database connection slow
- Action: Check disk space, optimize database
```

---

## Task 3: Add Files to a Snapshot

### Scenario
You want to:
- Create a "baseline" of what files exist on a computer right now
- Compare future state to this baseline (detect changes)
- Track what files were added, removed, or modified
- Compliance reporting (prove system hasn't changed)

### What You'll Learn
- How to create file snapshots
- Why snapshots are valuable
- How snapshots enable baseline drift reports (Lab 7)

### Step-by-Step Instructions

**Step 1: Navigate to Computer Details**
```
1. Assets → Computers
2. Click: View Details (📄) for WORKGROUP\WINDOWS-CLIENT
```

**Step 2: Create First Snapshot**
```
3. On the right side, under "Actions" section
4. Click: Add Files to Snapshot
```

**💡 Understanding Snapshots:**
```
A snapshot is a point-in-time inventory:
- Lists ALL tracked files on the computer
- Records file hashes
- Notes approval status
- Captures file paths
- Timestamp of when snapshot taken

Think of it like a photograph of the file system
```

**Step 3: Name the Snapshot**
```
5. In the popup, find: "Create new snapshot" text box
6. Enter: App Control Server <today's date>
   
   Example: App Control Server 2025-01-18
   
   💡 Naming Convention:
   Good names include:
   - Computer identifier
   - Date
   - Purpose (if specific)
   
   Examples:
   ✓ "DMZ-WEB01 Pre-Patch 2025-01-18"
   ✓ "Finance-Laptop Before Upgrade"
   ✓ "Baseline January 2025"
```

**Step 4: Create Snapshot**
```
7. Click: Create button
```

**💡 What's Happening Behind the Scenes:**
```
1. Server sends snapshot command to agent
2. Agent enumerates all tracked files
3. Agent records:
   - File names
   - File paths
   - File hashes
   - Approval status
   - Timestamps
4. Agent uploads snapshot data to server
5. Server stores snapshot for comparison

Time required: 1-5 minutes depending on file count
```

**Step 5: Create Second Snapshot (Windows Client)**
```
8. Navigate back: Assets → Computers
9. Click: View Details (📄) for WORKGROUP\WINDOWS-CLIENT
   (Should already be on this computer)

10. Under Actions → Add Files to Snapshot
11. Text box: Windows Client <today's date>
    Or simply: Windows Client
12. Click: Create
```

**💡 Why Two Snapshots?**
```
In Lab 7, you'll create baseline drift reports:
- Compare current state to snapshot
- See what changed since snapshot
- Each computer needs its own snapshot

In this lab environment:
- App Control Server snapshot: For server comparison
- Windows Client snapshot: For workstation comparison

In production:
- Create snapshots for critical servers monthly
- Create snapshots before major changes
- Create snapshots after clean builds
```

**Step 6: View Your Snapshots**
```
13. Navigate: Reports → Baseline Drift
14. Click: Snapshots tab (at top)

15. You should see both snapshots:
    ☑️ App Control Server <date>
    ☑️ Windows Client <date>
```

**Step 7: Explore Snapshot Details**
```
16. Click: View Details (📄) icon for "Windows Client" snapshot

You'll see:
- Snapshot name and date
- Computer name
- Total files in snapshot
- Approved files count
- Unapproved files count
- Banned files count (if any)
- When snapshot was created
- Who created it
```

**💡 Understanding the Snapshot Data:**
```
Example Snapshot Summary:

Snapshot: Windows Client 2025-01-18
Computer: WORKGROUP\WINDOWS-CLIENT
Created: 2025-01-18 14:30:22
By: admin

File Summary:
├─ Total Files: 5,234
├─ Approved: 5,100 (97.4%)
├─ Unapproved: 134 (2.6%)
└─ Banned: 0 (0%)

This baseline can now be used to detect:
+ New files added
- Files removed
~ Files modified
= Files unchanged
```

**Real-World Snapshot Use Cases:**
```
Use Case 1: Server Compliance
- Snapshot: Production database server
- Frequency: Monthly
- Purpose: Prove no unauthorized changes
- Audit: Compare each month's report

Use Case 2: Before/After Changes
- Snapshot: Before Windows update
- Apply update
- Run baseline drift report
- Verify: Only expected files changed

Use Case 3: Golden Image
- Snapshot: Clean workstation build
- Deploy to 100 computers
- Each week: Compare to golden image
- Detect: Configuration drift

Use Case 4: Breach Investigation
- Snapshot: Suspected compromised server
- Compare to pre-breach snapshot
- Identify: What malware added
- Determine: What was modified
```

---

## Task 4: Test the Use of a Local Approval Policy Code

### Scenario
A user is blocked from installing software and needs it urgently, but you're not immediately available to approve it through the console.

**Local Approval Policy Code** provides a temporary code that lowers enforcement, allowing installations.

### What You'll Learn
- What local approval codes are
- When to use them (and when NOT to)
- Security implications

### Step-by-Step Instructions

**Step 1: Switch to Windows Client**
```
1. Minimize browser (console)
2. On Windows Client desktop
3. Open: Downloads folder
   (Should still be open from Lab 2)
```

**Step 2: Try to Install Wireshark**
```
4. In Downloads folder, find: Wireshark-win64-3.2.3.exe
5. Double-click: Wireshark-win64-3.2.3.exe
```

**💡 What Happens:**
```
┌────────────────────────────────────────┐
│  Security Notification                 │
│  ──────────────────────────────────────│
│  This file is not approved and has     │
│  been prevented from running.          │
│                                         │
│  File: Wireshark-win64-3.2.3.exe       │
│  Trust: 90                             │
│  Threat: 0                              │
│                                         │
│  [Request Approval]                    │
└────────────────────────────────────────┘
```

**💡 Answer This Question:**

**Question: What prevented you from installing Wireshark?**
```
Answer: DMZ Policy with HIGH enforcement

Detailed explanation:
1. Computer is in DMZ policy (from Lab 2, Task 4)
2. DMZ policy: Connected = HIGH, Disconnected = HIGH
3. Wireshark is UNAPPROVED (not yet approved)
4. HIGH enforcement rule: Unapproved files = BLOCKED
5. Therefore: Wireshark blocked

This is working exactly as designed!
```

**Step 3: Leave This Blocked for Now**
```
6. Click: Cancel or X to close the notification
7. Do NOT request approval yet
```

**💡 Understanding:**
```
We intentionally left Wireshark blocked to demonstrate
the Temporary Policy Override feature in the next task.

In Task 5, we'll generate a time-limited code that
temporarily lowers enforcement to allow this installation.
```

---

## Task 5: Generate a Temporary Policy Override

### Scenario
**Emergency situation:**
- IT needs to install software on a locked-down computer
- No time to go through approval process
- Need immediate access
- But only for a short time (security)

**Solution:** Temporary Policy Override

### What You'll Learn
- How to generate time-limited override codes
- How users apply override codes
- Security best practices for overrides
- When this feature is appropriate

### Step-by-Step Instructions

**Part A: Generate the Override Code (Admin)**

**Step 1: Return to Console**
```
1. Switch back to Chrome browser
2. Should still be logged in as admin
```

**Step 2: Navigate to Computer Details**
```
3. Assets → Computers
4. Click: View Details (📄) for WORKGROUP\WINDOWS-CLIENT
```

**Step 3: Access Policy Override**
```
5. Scroll to bottom of Computer Details page
6. Find and click: Policy Override link/button
```

**💡 Understanding Policy Override:**
```
This feature generates a ONE-TIME-USE code that:
- Temporarily changes enforcement level
- For a specific duration (minutes/hours)
- Code has expiration time
- After duration, enforcement returns to normal
- All activity during override is logged

Think of it like a temporary security badge:
- Valid for limited time
- Expires automatically
- Usage is tracked
- Can't be reused
```

**Step 4: Configure the Override**
```
7. Configure settings:
   
   Temporary Enforcement: Local Approval
   (Dropdown menu - select "Local Approval")
   
   💡 What this means:
   During override period, enforcement drops to:
   - Approved files: RUN
   - Unapproved files: RUN (and get locally approved)
   - Banned files: Still BLOCKED
   
   Enforcement Level Active For: 5 Minutes
   (How long the lowered enforcement lasts)
   
   Code Valid For: 5 Minutes
   (How long the code can be entered)
```

**💡 Understanding the Time Settings:**
```
Two different timers:

Code Valid For (5 minutes):
└─ You have 5 minutes to ENTER the code
└─ After 5 minutes, code expires (can't be used)
└─ Must generate new code if expired

Enforcement Level Active For (5 minutes):
└─ Once code entered, lowered enforcement lasts 5 minutes
└─ After 5 minutes, HIGH enforcement resumes automatically
└─ Timer starts when code is applied, not generated

Example Timeline:
00:00 - Code generated (valid for 5 min)
00:02 - User enters code (enforcement drops)
00:07 - Enforcement active period ends (back to HIGH)
        (Note: Even though code expires at 00:05, 
         enforcement was active for 5 min from 00:02)
```

**Step 5: Generate the Code**
```
8. Click: Generate Code button

9. A code appears (random alphanumeric):
   Example: ABC-123-XYZ-789
   
10. Click in the code box
11. Press: Ctrl+C (copy the code)
    Or click: Copy button if available
```

**💡 Security Note:**
```
⚠️ Treat this code like a password:
- Don't share widely
- Use secure communication
- Code is single-use
- Expires quickly
- All usage is logged

In production:
- Phone the code to user
- Use encrypted messaging
- Don't email (can be intercepted)
- Document why override was needed
```

**Part B: Apply the Override Code (User)**

**Step 6: Switch to Windows Client**
```
12. Minimize browser
13. On Windows Client desktop
```

**Step 7: Navigate to Parity Agent Folder**
```
14. Click: Start menu (Windows logo)
15. Select: File Explorer
16. Click: This PC
17. Double-click: Local Disk (C:)
18. Navigate to folders:
    Program Files (x86) → Bit9 → Parity Agent
    
    Full path: C:\Program Files (x86)\Bit9\Parity Agent\
```

**💡 Understanding Agent Folder:**
```
This folder contains:
- Agent executable (parity.exe)
- Configuration files
- Log files
- Administrative tools:
  └─ TimedOverride.exe ← What we need
  └─ PolicyTools.exe
  └─ DiagnosticTool.exe
```

**Step 8: Run Timed Override Tool**
```
19. Find file: TimedOverride.exe
20. Double-click: TimedOverride.exe
```

**💡 What Appears:**
```
┌──────────────────────────────────┐
│  Authorization Required           │
│  ────────────────────────────────│
│                                   │
│  Enter authorization code:        │
│  [____________________]           │
│                                   │
│  [     OK     ] [  Cancel  ]     │
└──────────────────────────────────┘
```

**Step 9: Enter the Code**
```
21. Click in the text box
22. Press: Ctrl+V (paste the code you copied)
    Or manually type: ABC-123-XYZ-789 (your actual code)
23. Click: OK
```

**💡 What Happens:**
```
1. Tool validates code with server
2. Server confirms code is valid
3. Enforcement temporarily drops to Local Approval
4. Timer starts (5 minutes)
5. User sees confirmation message
6. Any software run during this period will work
```

**Expected Result:**
```
✅ Code accepted
✅ Enforcement temporarily changed
✅ 5-minute timer started

You might see:
"Policy override activated. Enforcement will return 
 to HIGH in 5 minutes."
```

**Step 10: Install Wireshark During Override Window**
```
24. QUICKLY return to: Downloads folder
25. Double-click: Wireshark-win64-3.2.3.exe
```

**Result:**
```
✅ Wireshark installer RUNS (no block!)
✅ Installation proceeds normally
✅ No security notification
```

**Step 11: Complete Installation**
```
26. Wireshark installer wizard appears
27. Click: Next
28. Accept: License agreement
29. Click: Next (accept default components)
30. Click: Next (accept default location)
31. Click: Next (shortcuts)
32. Click: Install
33. Wait for installation to complete
34. Click: Finish
```

**Step 12: Test Wireshark**
```
35. Click: Start menu
36. Search: Wireshark
37. Click: Wireshark (from search results)
38. Application opens successfully
```

**💡 What Just Happened:**
```
Timeline of Events:

00:00 - Code generated (admin)
00:30 - Code entered (user)
00:30 - Enforcement drops to Local Approval
00:45 - Wireshark installer runs
01:00 - Wireshark installs successfully
05:30 - 5-minute window expires
05:30 - Enforcement returns to HIGH

After 05:30:
- Wireshark still works (was approved during window)
- New unapproved software would be blocked again
- Override period has ended
```

**💡 Answer This Question:**

**Question: After a 5 minute active period, is Wireshark still running on this endpoint?**
```
Answer: YES - Wireshark continues to run

Why?
1. Wireshark was installed during override window
2. Installation process approved the files
3. Files got "Locally Approved" status
4. Even after override expires, approved files still work
5. HIGH enforcement returns, but Wireshark is now approved

What would be blocked after 5 minutes?
- NEW unapproved software
- Not already-approved Wireshark

Think of it like this:
- Override opened a door temporarily
- Wireshark walked through and got approved
- Door closed again (HIGH enforcement resumed)
- But Wireshark is already inside (approved)
```

---

### Policy Override - Security Best Practices

**When TO use Policy Override:**
```
✅ Emergency software installation
✅ Critical business need (production down)
✅ Remote site with slow approval process
✅ After-hours urgent fix
✅ Time-sensitive situation

Example:
"Production database server down. DBA needs to install
 diagnostic tool immediately. No time for approval process.
 Generate 10-minute override code."
```

**When NOT to use Policy Override:**
```
❌ Regular software installations (use approval process)
❌ User convenience ("I don't want to wait")
❌ Bypassing security policy
❌ Installing personal software
❌ Long-term access (use proper approval)

Example:
"User wants to install game during lunch break."
→ NO. Use proper approval request process.
```

**Audit and Compliance:**
```
All policy overrides are logged:
- Who generated the code (admin user)
- When code was generated
- Computer it was for
- How long the override lasted
- What was installed during override
- Who used the code

Reports → Events → Filter by:
"Subtype: Policy Override"

This audit trail is critical for:
✓ Security investigations
✓ Compliance audits
✓ Tracking emergency access
✓ Identifying override abuse
```

---

## Task 6: Investigate Why PuTTY Started When in High Enforcement Mode

### The Mystery from Lab 2

**Remember this from Lab 2, Task 4?**
```
Computer: WORKGROUP\WINDOWS-CLIENT
Policy: DMZ (HIGH enforcement)
File: putty.exe
Expected: BLOCKED (unapproved file in HIGH enforcement)
Actual: RAN successfully! 🤔

Question: WHY?
```

### Time to Solve the Mystery!

### Step-by-Step Investigation

**Step 1: Navigate to Computer Details**
```
1. Return to Chrome browser (console)
2. Assets → Computers
3. Click: View Details (📄) for WORKGROUP\WINDOWS-CLIENT
```

**Step 2: View Files on Computer**
```
4. Right side → Related Views
5. Click: Files on this Computer
```

**Step 3: Filter for PuTTY**
```
6. Click: Add filter dropdown
7. Select: File Name
8. Second dropdown: select "is"
9. Text box: enter "putty.exe"
10. Click: Apply
```

**Result:**
```
You should see putty.exe in the results
Possibly multiple instances (different paths)
```

**Step 4: View File Details**
```
11. Click: View Details icon (📄) next to putty.exe
    (The checkbox, then the icon)
```

**Step 5: Examine General Information**
```
12. Review the "General Information" section carefully

Pay special attention to:
- First Seen Name
- First Seen Date
- First Seen Path
```

**💡 What You'll Discover:**
```
General Information:
├─ File Name: putty.exe
├─ File Hash: ABC123DEF456...
├─ First Seen Name: putty.exe
├─ First Seen Date: 2025-01-14 10:05:22  ← BEFORE Lab 2!
├─ First Seen Path: C:\Program Files\PuTTY\putty.exe
└─ Approval Status: Locally Approved

Key Insight:
App Control saw this file BEFORE you extracted the ZIP!
```

**Step 6: View File History**
```
13. Scroll down to "File History" section
14. Look for earliest event
15. Find: "Discovery during initialization"
```

**💡 The Critical Event:**
```
File History:
┌────────────────────────────────────────────────────┐
│ Date: 2025-01-14 10:05:22                         │
│ Event: Discovery during initialization            │
│ Computer: WORKGROUP\WINDOWS-CLIENT                │
│ Path: C:\Program Files\PuTTY\putty.exe           │
│ Action: Locally approved (initialization)         │
└────────────────────────────────────────────────────┘
```

**💡 Answer This Question:**

**Question: Based on the file's General information, during the initialization process, what was the file location for it to receive local approval?**
```
Answer: C:\Program Files\PuTTY\putty.exe

This was the ORIGINAL location where App Control
first discovered the file during agent initialization.
```

**Step 7: View All File Instances**
```
16. Right side → Related Views
17. Click: All File Instances
```

**What You'll See:**
```
Multiple instances of the same file (same hash):

Instance 1:
├─ Computer: WORKGROUP\WINDOWS-CLIENT
├─ Path: C:\Program Files\PuTTY\putty.exe
├─ First Seen: 2025-01-14 10:05:22
├─ Event: Discovery during initialization
└─ Status: Locally Approved

Instance 2:
├─ Computer: WORKGROUP\WINDOWS-CLIENT  
├─ Path: C:\Users\Administrator\Downloads\putty.exe
├─ First Seen: 2025-01-15 14:20:15  ← During Lab 2
├─ Event: File copied
└─ Status: Locally Approved (inherited from Instance 1)

Key Understanding:
Same file (same hash) = same approval status
```

---

### The Complete Explanation - Mystery Solved!

**What Really Happened:**

```
TIMELINE:

Day 1 (Before Labs):
┌──────────────────────────────────────────────┐
│ 10:00 AM - Agent installed on Windows Client │
│ 10:02 AM - Initialization process begins     │
│ 10:05 AM - Agent discovers all existing files│
│          - Finds: C:\Program Files\PuTTY\    │
│          - Finds: putty.exe                  │
│ 10:06 AM - putty.exe LOCALLY APPROVED        │
│          - (automatic during initialization) │
└──────────────────────────────────────────────┘

Day 2 (Lab 2):
┌──────────────────────────────────────────────┐
│ 02:15 PM - Extract Downloads.zip             │
│ 02:16 PM - putty.exe copied to Downloads     │
│ 02:17 PM - You try to run it                 │
│ 02:17 PM - App Control checks:               │
│          - Hash matches approved file        │
│          - Locally approved status applies   │
│ 02:17 PM - putty.exe RUNS (approved!)        │
└──────────────────────────────────────────────┘
```
