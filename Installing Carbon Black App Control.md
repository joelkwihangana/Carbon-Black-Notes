# Installing Carbon Black App Control

## 🎯 What You're Building

Think of Carbon Black App Control like a **security guard for your computers**:
- **Windows VM (Server)** = The security office with cameras and records
- **Linux VM (Agent)** = A building that the security guard watches over

---

## 📋 Before You Start - Things You Need

### For Your Windows VM (Server):
1. **Operating System**: Windows Server 2016, 2019, or 2022 (US English version only)
2. **SQL Server**: Download and install **Microsoft SQL Server Express** (free for small setups)
3. **Internet Information Services (IIS)**: Built into Windows Server
4. **.NET Framework 4.8**: Download from Microsoft
5. **Broadcom Account**: Sign up at Broadcom's website to download the software
6. **At least 4GB RAM** and **50GB disk space**

### For Your Linux VM (Agent):
- Red Hat Enterprise Linux 8.10 or 9.4
- Network connection to your Windows Server

---

## 🚀 PHASE 1: Setting Up the Windows Server (The Brain)

### Step 1: Install Prerequisites (Do This First!)

#### 1.1 Install SQL Server Express
**What is SQL Server?** It's like a filing cabinet where all security information is stored.

1. Download **SQL Server 2019 Express** from Microsoft
2. Run the installer
3. Choose "Basic" installation type
4. Accept the license terms
5. Choose an installation location
6. Wait for installation to complete
7. **Write down** the SQL Server instance name (usually `localhost\SQLEXPRESS`)

#### 1.2 Enable Internet Information Services (IIS)
**What is IIS?** It's the web server that lets you access the control panel in your browser.

1. Open **Server Manager**
2. Click **"Manage"** → **"Add Roles and Features"**
3. Click **Next** through the wizard until you see "Server Roles"
4. Check the box for **"Web Server (IIS)"**
5. Click **"Add Features"** when prompted
6. Click **Next**, then **Next**, then **Install**
7. Wait for installation to complete

#### 1.3 Install .NET Framework 4.8
1. Download from: `https://dotnet.microsoft.com/download/dotnet-framework/net48`
2. Run the installer
3. Follow the prompts (it's simple, just click Next)
4. **Restart your computer** when done

---

### Step 2: Download Carbon Black Software

1. Go to: `https://support.broadcom.com`
2. Sign in with your Broadcom account (create one if needed)
3. Navigate to **"Cyber Security Software"** → **"Carbon Black App Control"**
4. Download these files:
   - **Server Installer**: `ParityServerSetup.exe` (version 8.11.2)
   - **Rules Installer**: `RulesInstaller.exe`
   - **Windows Agent**: `WindowsHostPackageInstaller.exe`
   - **Linux Agent**: `LinuxHostPackageInstaller.exe`
5. **Save your license key** - you'll get this when you download or from your Broadcom contact

---

### Step 3: Install the Carbon Black Server

1. **Run the installer** as Administrator:
   - Right-click `ParityServerSetup.exe`
   - Choose **"Run as administrator"**

2. **Welcome Screen**:
   - Click **Next**

3. **License Agreement**:
   - Read and accept the terms
   - Click **Next**

4. **Database Configuration** (Important!):
   - **SQL Server Name**: Enter `localhost\SQLEXPRESS` (or your instance name)
   - **Database Action**: Select **"Create a new database"**
   - **Database Name**: Leave as default (`AppControl`)
   - **Service Account**: Choose **"Use Local System Account"** (simplest for beginners)
   - Click **Next**

5. **Network Configuration**:
   - **Server Address**: Enter your Windows VM's hostname or IP address
   - **Console Port**: Leave as `443` (standard HTTPS)
   - **Agent Port**: Leave as `41002` (agents connect here)
   - Click **Next**

6. **Certificate Configuration**:
   - Choose **"Create a Carbon Black self-signed certificate"** (simplest option)
   - Note: Your browser will show a security warning, but that's okay for testing
   - Click **Next**

7. **Administrator Password**:
   - Create a strong password for the `admin` account
   - **WRITE THIS DOWN!** You'll need it to log in
   - Click **Next**

8. **Installation**:
   - Click **Install**
   - Wait 5-10 minutes for installation to complete
   - Click **Finish**

---

### Step 4: First Login and Configuration

1. **Open your web browser** (Chrome or Firefox recommended)
2. Go to: `https://your-windows-server-ip`
3. Click **"Advanced"** → **"Proceed to site"** (ignore the certificate warning for now)
4. **Login**:
   - Username: `admin`
   - Password: (the one you created in Step 3.7)

5. **Upload Agent and Rules Packages**:
   - Click the **gear icon** (⚙️) in the top right → **"System Configuration"**
   - Click **"Update Agent/Rule Versions"**
   - **Drag and drop** these files (one at a time):
     - `RulesInstaller.exe`
     - `WindowsHostPackageInstaller.exe`
     - `LinuxHostPackageInstaller.exe`
   - Wait for each to finish uploading (you'll see "Upload Complete")

---

### Step 5: Create Your First Policy

**What is a Policy?** It's a rule set that tells the agent what to allow or block.

1. In the console, click **"Rules"** in the left menu → **"Policies"**
2. Click **"Add Policy"** button
3. **Policy Settings**:
   - **Name**: Enter `Learning Policy`
   - **Description**: `Initial policy for testing - does not block anything`
   - **Agent Mode**: Select **"Agent Disabled"**
   - **Enforcement Level**: Select **"None"**
4. Click **"Save"**

**Why start with "Agent Disabled"?** This lets the agent learn what's on your computer without blocking anything. Perfect for beginners!

---

## 🐧 PHASE 2: Installing the Agent on Linux

### Step 1: Download the Agent Installer

1. In the Carbon Black console, go to **"Rules"** → **"Policies"**
2. Find your **"Learning Policy"**
3. Click the **"Download agent software"** link
4. Download the **Linux Red Hat** package (`.tgz` file)
5. Note the filename - it will be something like: `learningpolicy-redhat.tgz`

---

### Step 2: Transfer the File to Your Linux VM

**Option A: Using a Shared Folder** (if VMs share folders):
1. Copy the `.tgz` file to the shared folder

**Option B: Using SCP** (secure copy - if you're comfortable):
```bash
# From your Windows machine in PowerShell:
scp learningpolicy-redhat.tgz username@linux-vm-ip:/home/username/
```

**Option C: Using a USB or Direct Copy** (simplest):
1. Copy file to USB drive
2. Connect to Linux VM
3. Copy from USB to Linux home directory

---

### Step 3: Install the Agent on Linux

**Don't worry!** I'll walk you through each command. Just copy and paste.

1. **Open the Terminal** on your Linux VM

2. **Go to where you saved the file**:
```bash
cd ~
# This goes to your home directory
```

3. **Extract the installer**:
```bash
tar -xvzf learningpolicy-redhat.tgz
# This "unzips" the file
```

4. **Go into the extracted folder**:
```bash
cd learningpolicy-redhat
# Replace "learningpolicy" with your actual filename
```

5. **Run the installer** (you'll need the password for sudo):
```bash
sudo sh ./b9install.sh
```
   - Type your Linux password when prompted
   - Wait for installation to complete (1-2 minutes)

6. **Check if it's running**:
```bash
ps aux | grep b9
```
   - You should see a line with `b9daemon` in it
   - This means it's working! ✅

---

### Step 4: Verify the Agent is Connected

1. Go back to the **Carbon Black console** in your browser
2. Click **"Assets"** → **"Computers"** in the left menu
3. Wait 1-2 minutes, then refresh the page
4. You should see your **Linux VM** listed!
5. Status should show **"Initializing"** - this is normal and can take 10-30 minutes

---

## 🎓 Understanding What's Happening

### The "Initialization" Process
When the agent first installs, it:
1. **Scans all files** on your Linux computer
2. **Creates a fingerprint (hash)** of each file
3. **Sends this information** to the Windows server
4. **Automatically approves** existing files (since they were already there)

This process can take 10-30 minutes depending on how many files you have.

### What Each Status Means
- **Initializing**: Agent is scanning files (first time setup)
- **Normal**: Everything is working correctly
- **Offline**: Agent can't connect to the server (check network/firewall)

---

## 🔒 PHASE 3: Moving to Protection Mode (When You're Ready)

**⚠️ Only do this after initialization completes!**

Once your Linux VM shows **"Normal"** status:

1. In the console, go to **"Assets"** → **"Computers"**
2. **Check the box** next to your Linux VM
3. Click **"Actions"** → **"Move to Policy"**
4. Create a new policy or select a stricter policy:
   - **Name**: `Medium Protection`
   - **Agent Mode**: `Enabled`
   - **Enforcement Level**: `Medium Enforcement`
5. Click **"Move"**

**What happens now?**
- The agent will start monitoring new files
- Unknown files will be sent to the server for approval
- You'll get notifications about blocked files

---

## 🛠️ Troubleshooting Common Issues

### Server Won't Start
- **Check**: Is SQL Server running?
  - Open **Services** (services.msc)
  - Look for "SQL Server (SQLEXPRESS)"
  - Right-click → Start

### Can't Access Console
- **Check**: Firewall settings
  - Open **Windows Firewall** → **Advanced Settings**
  - Make sure port 443 is allowed

### Linux Agent Won't Connect
- **Check**: Network connectivity
```bash
# Test connection to server
ping your-windows-server-ip
telnet your-windows-server-ip 41002
```
- **Check**: Firewall on Windows allows port 41002

### Agent Stuck on "Initializing"
- This is normal! Be patient (can take 30+ minutes)
- Check progress: In console → Computer Details → "Initialization Status"

---

## 📚 Next Steps & Learning Resources

### Official Documentation
- **User Guide**: https://techdocs.broadcom.com/us/en/carbon-black/app-control/carbon-black-app-control/8-11-2.html
- **Installation Guide**: Included in the PDF you provided
- **Community**: Broadcom Carbon Black Community forums

### What to Learn Next
1. **Creating approval rules** for trusted software
2. **Setting up notifications** for blocked files
3. **Understanding enforcement levels** (Low → High)
4. **Creating custom rules** for your environment

### Practice Exercises
1. Install a known application on Linux and watch it get approved
2. Try to run an unknown script and see how CB blocks it
3. Manually approve a file through the console
4. Create a "Trusted Directory" for your development tools

---

## ✅ Installation Checklist

- [ ] SQL Server installed on Windows VM
- [ ] IIS enabled on Windows VM
- [ ] .NET Framework 4.8 installed
- [ ] CB App Control Server installed
- [ ] Can login to web console
- [ ] Uploaded Agent and Rules packages
- [ ] Created "Learning Policy"
- [ ] Downloaded Linux agent installer
- [ ] Installed agent on Linux VM
- [ ] Linux computer appears in console
- [ ] Initialization completed successfully
- [ ] Ready to move to protection mode

---

## 🆘 Getting Help

**If you get stuck:**
1. Check the Events page in console (shows errors)
2. Review the troubleshooting section above
3. Search Broadcom Community forums
4. Contact Broadcom Support (if you have a license)
5. Check server logs: `C:\Program Files\Bit9\Parity Server\Logs`

**Remember**: It's okay to take it slow! Security software is complex, and learning takes time.

---

## 🎯 Key Takeaways

1. **Start in "Disabled" mode** - let the agent learn first
2. **Initialization takes time** - be patient
3. **Watch the Events page** - it tells you what's happening
4. **Test before enforcing** - don't jump straight to High Enforcement
5. **Document everything** - write down your passwords and settings!
