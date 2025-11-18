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
