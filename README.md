# Active Directory Home Lab – Windows Server 2019 on Oracle VirtualBox

**Portfolio Project** | Hands-on Windows Server & Active Directory Lab

This repository documents my complete setup of a functional Active Directory Domain Services (AD DS) home lab using Oracle VirtualBox. The lab consists of a Windows Server 2019 Domain Controller and a Windows 10 client joined to the domain, with DHCP, NAT, and bulk user creation via PowerShell.

I followed the excellent tutorial by **Josh Madakor**:  
[How to Setup a Basic Home Lab Running Active Directory (Oracle VirtualBox) | Add Users w/PowerShell](https://www.youtube.com/watch?v=MHsI8hJmggI)

---

## Project Goals

- Build a realistic Windows Server / Active Directory environment on my local machine
- Practice core system administration skills daily
- Create a documented, resume-ready portfolio project that demonstrates hands-on experience with:
  - Virtualization (Oracle VirtualBox)
  - Windows Server 2019
  - Active Directory Domain Services
  - DHCP & Network Address Translation (NAT)
  - PowerShell automation for user management

---

## Lab Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Host Machine (Your PC)                   │
│                                                             │
│  ┌──────────────────────┐         ┌──────────────────────┐  │
│  │   Domain Controller  │         │     Client1 (Win10)  │  │
│  │  Windows Server 2019 │         │                      │  │
│  │                      │         │  Joined to Domain    │  │
│  │  • AD DS             │◄───────►│  • Obtains IP via    │  │
│  │  • DHCP Server       │ Internal│    DHCP              │  │
│  │  • NAT / Routing     │ Network │  • Domain Login      │  │
│  │  • DNS               │         │                      │  │
│  │                      │         │                      │  │
│  │  Adapter 1: NAT      │         │  Adapter 1: Internal │  │
│  │  Adapter 2: Internal │         │                      │  │
│  └──────────────────────┘         └──────────────────────┘  │
│           │                                                 │
│           │ NAT → Internet                                  │
└───────────┼─────────────────────────────────────────────────┘
            ▼
       Internet Access
```

**Key Components**
- **Domain Controller (DC)**: Windows Server 2019 – hosts AD DS, DHCP, DNS, and NAT
- **Client**: Windows 10 – joins the domain and authenticates against AD
- **Network**: Isolated Internal Network + NAT for internet access
- **Users**: ~1,000 domain users created automatically with PowerShell

---

## What I Learned

### Core Skills Practiced
- Creating and configuring virtual machines in **Oracle VirtualBox**
- Installing and hardening Windows Server 2019 (Desktop Experience)
- Configuring dual network adapters (NAT + Internal Network)
- Setting static IP addressing and DNS on a domain controller
- Installing and promoting **Active Directory Domain Services** (new forest)
- Creating Organizational Units (OUs) and managing security groups
- Configuring **Routing and Remote Access (NAT)** so internal clients can reach the internet
- Installing and authorizing a **DHCP Server** with proper scopes and options
- Using **PowerShell** to bulk-create hundreds of Active Directory users
- Joining a Windows 10 client to an Active Directory domain
- Verifying connectivity with `ipconfig`, `ping`, and domain logon

### Key Takeaways
- Understanding the relationship between DNS, DHCP, and Active Directory
- Why a domain controller needs a static IP and why clients should use DHCP
- How NAT on the DC allows an isolated lab to still reach the internet
- The power of PowerShell for scalable user provisioning (creating 1,000 users in minutes instead of hours)
- Importance of Organizational Units for proper Group Policy application later

---

## Prerequisites

| Software / File                  | Download Link |
|----------------------------------|---------------|
| Oracle VirtualBox + Extension Pack | [virtualbox.org](https://www.virtualbox.org/) |
| Windows Server 2019 Evaluation ISO | [Microsoft Eval Center](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019) |
| Windows 10 ISO                   | [Microsoft Media Creation Tool](https://www.microsoft.com/en-us/software-download/windows10) |
| PowerShell User Creation Script  | [Josh Madakor’s GitHub](https://github.com/joshmadakor1/AD_PS) |

**Recommended Host Resources**
- At least 8 GB RAM (2 GB allocated to each VM)
- 50+ GB free disk space
- Virtualization enabled in BIOS

---

## Step-by-Step Lab Setup

### 1. Install VirtualBox & Download ISOs
- Install Oracle VirtualBox and the Extension Pack
- Download Windows Server 2019 Evaluation (Desktop Experience) and Windows 10 ISOs

### 2. Create the Domain Controller VM
1. Click **New** in VirtualBox
2. Name: `DC`
3. Type: Microsoft Windows → Windows Server 2019 (64-bit)
4. Memory: **2048 MB**
5. Create a virtual hard disk (default VDI, dynamically allocated)
6. In **Settings**:
   - **System → Processor**: 1 CPU
   - **Network**:
     - Adapter 1 → Attached to: **NAT**
     - Adapter 2 → Enable → Attached to: **Internal Network** (name: `intnet` or leave default)
   - **General → Advanced**: Shared Clipboard & Drag and Drop → Bidirectional
7. Attach the Windows Server 2019 ISO and start the VM

### 3. Install Windows Server 2019
- Select **Windows Server 2019 Standard Evaluation (Desktop Experience)**
- Complete the installation and set a strong local Administrator password
- Install **Guest Additions** (Devices → Insert Guest Additions CD image)

### 4. Configure Networking on the DC
1. Rename the network adapters for clarity:
   - Adapter connected to NAT → `_INTERNET_`
   - Adapter connected to Internal Network → `_INTERNAL_`
2. Configure a **static IP** on the `_INTERNAL_` adapter:
   ```
   IP Address:      172.16.0.1
   Subnet Mask:     255.255.255.0
   Default Gateway: (leave blank)
   Preferred DNS:   127.0.0.1
   ```
3. Rename the computer to `DC` and reboot

### 5. Install Active Directory Domain Services
1. Open **Server Manager → Add Roles and Features**
2. Install **Active Directory Domain Services**
3. Promote the server to a domain controller:
   - Add a new forest
   - Root domain name: `mydomain.com` (or your preferred lab domain)
   - Set Directory Services Restore Mode (DSRM) password
4. Reboot when prompted
5. Log in with the new domain admin account (`MYDOMAIN\Administrator`)

### 6. Create a Domain Admin Account & OU
1. Open **Active Directory Users and Computers**
2. Create an Organizational Unit named `_ADMINS`
3. Create a new user inside `_ADMINS` and add it to the **Domain Admins** group
4. Log out and log back in with the new domain admin account (best practice)

### 7. Configure NAT (Routing and Remote Access)
1. Install the **Remote Access** role
2. Open **Routing and Remote Access**
3. Right-click the server → **Configure and Enable Routing and Remote Access**
4. Choose **Network Address Translation (NAT)**
5. Select the `_INTERNET_` adapter as the public interface
6. Finish the wizard

### 8. Install and Configure DHCP
1. Install the **DHCP Server** role
2. Authorize the DHCP server in Active Directory
3. Create a new IPv4 scope:
   - Scope name: Lab Scope
   - IP range: `172.16.0.100` – `172.16.0.200`
   - Subnet mask: `255.255.255.0`
   - Lease duration: 8 days (lab-friendly)
   - Router (Default Gateway): `172.16.0.1`
   - Parent Domain: `mydomain.com`
   - DNS Server: `172.16.0.1`
4. Activate the scope

### 9. Bulk Create Users with PowerShell
1. Download the script from Josh Madakor’s repository:  
   [https://github.com/joshmadakor1/AD_PS](https://github.com/joshmadakor1/AD_PS)
2. Extract the files and place `names.txt` and the `.ps1` script on the DC desktop
3. Open **PowerShell ISE as Administrator**
4. Run:
   ```powershell
   Set-ExecutionPolicy Unrestricted -Force
   ```
5. Navigate to the script folder and execute the script
6. The script will:
   - Create an Organizational Unit named `_USERS`
   - Generate usernames from the names list (first initial + last name)
   - Set password to `Password1` for all users
   - Enable the accounts and place them in the `_USERS` OU

### 10. Create and Join the Windows 10 Client
1. Create a new VM named `Client1`
2. Memory: 2048 MB
3. Network Adapter: **Internal Network** only
4. Install Windows 10
5. Rename the computer to `CLIENT1`
6. Join the domain:
   - System Properties → Change → Domain → `mydomain.com`
   - Authenticate with a Domain Admin account
7. Reboot and log in with one of the newly created domain users

---

## Verification Checklist

- [ ] Domain Controller has static IP `172.16.0.1`
- [ ] DHCP is authorized and handing out addresses in the correct range
- [ ] Client receives an IP via DHCP and can ping the DC
- [ ] Client can reach the internet (via NAT)
- [ ] Domain users appear in Active Directory Users and Computers under `_USERS`
- [ ] Domain login works on the client

---

## Daily Practice Ideas

Now that the lab is running, I plan to practice daily:
- Create and manage Group Policy Objects (GPOs)
- Configure fine-grained password policies
- Practice user/group management and delegated permissions
- Explore Active Directory Sites and Services
- Add additional domain controllers or member servers
- Experiment with PowerShell AD cmdlets (`Get-ADUser`, `Set-ADUser`, etc.)
- Snapshot the VMs before major changes so I can roll back safely

---

## Credits

Huge thanks to **Josh Madakor** for the clear, practical tutorial that made this lab possible.

- YouTube Video: [https://www.youtube.com/watch?v=MHsI8hJmggI](https://www.youtube.com/watch?v=MHsI8hJmggI)
- PowerShell Script: [https://github.com/joshmadakor1/AD_PS](https://github.com/joshmadakor1/AD_PS)



