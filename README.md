# 💻 Active Directory Home Lab

A home lab project building and administering a **Windows Server 2022 Active Directory (AD) domain** from scratch using VirtualBox — practicing the core directory services skills used daily by IT helpdesk and systems administrators.

---

## 🎯 Goals

- Stand up a Windows Server 2022 Domain Controller in a virtualized environment
- Practice real-world AD administration tasks (users, groups, OUs, Group Policy)
- Explore NTFS permissions and file auditing tied to Windows security Event IDs
- Understand how AD relates to authentication, lateral movement, and security monitoring

---

## 🏗️ Lab Architecture

```
[VirtualBox — Internal Network: lab.local]

  Windows Server 2022 VM  ──►  Domain Controller (DC)
                                    lab.local forest

  Windows 10/11 VM        ──►  Domain-Joined Client
                                    (lab\username)
```

**Hypervisor:** VirtualBox on Windows host
**Domain:** `lab.local`
**Server OS:** Windows Server 2022 (180-day evaluation)
**Client OS:** Windows 10/11 Pro (required for domain join)

---

## ⚙️ Setup Steps

### 1. Download Evaluation ISOs
- [Windows Server 2022 Eval](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022) — free 180-day license
- Windows 10/11 Pro — for client machine

### 2. Create VMs in VirtualBox
| VM | RAM | Storage | Network |
|---|---|---|---|
| Server 2022 | 4GB | 50GB | Internal Network |
| Windows Client | 2GB | 40GB | Internal Network |

### 3. Install AD Domain Services (AD DS)
```
Server Manager → Add Roles and Features
→ Active Directory Domain Services → Install
→ Promote this server to a Domain Controller
→ Add a new forest: lab.local
→ Set DSRM password → Complete
```

### 4. Install RSAT on Client (for remote AD management)
```
Settings → Optional Features → Add a Feature
→ Search "RSAT: Active Directory Domain Services"
→ Install → Launch: dsa.msc
```

### 5. Join Client Machine to Domain
```
Settings → System → About → Domain or Workgroup
→ Change → Domain: lab.local
→ Enter domain admin credentials → Restart
```

---

## 🔧 Skills Practiced

### User & Group Management
```powershell
# Create a new user
New-ADUser -Name "Test User" -SamAccountName "tuser" -AccountPassword (Read-Host -AsSecureString) -Enabled $true

# Get all users
Get-ADUser -Filter * | Select Name, SamAccountName

# Get group members
Get-ADGroupMember -Identity "Domain Admins"
```

### NTFS Permissions
- Configured basic and advanced NTFS permissions on shared folders
- Practiced Allow vs. Deny rules and inheritance behavior
- Enabled file access auditing tied to **Event ID 4663**

### net use — Network Drive Mapping
```cmd
net use Z: \\SERVER\SharedFolder /user:lab\username password
net use Z: /delete
```

### Event Log Monitoring
| Event ID | Meaning |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4648 | Logon using explicit credentials |
| 4663 | File object access |
| 4740 | Account lockout |

---

## 🛠️ AD Management Tools Used

| Tool | Command | Purpose |
|---|---|---|
| Active Directory Users & Computers | `dsa.msc` | Primary GUI for managing users/groups/OUs |
| Active Directory Admin Center | `dsac.exe` | Modern GUI, fine-grained password policies |
| ADSI Edit | `adsiedit.msc` | Low-level raw attribute editor |
| PowerShell AD Module | `Import-Module ActiveDirectory` | Scripting and automation |

---

## 🔗 Integration with Wazuh SIEM

Windows Security Event Logs from this AD lab are forwarded to the [Wazuh SIEM](../home-lab-siem-wazuh), making it possible to detect failed logon attempts, account lockouts, and privileged group changes in real time.

---

## 📚 What I Learned

- How Active Directory authenticates users via Kerberos
- The relationship between NTFS permissions and Share permissions
- How Group Policy Objects (GPOs) push settings to domain-joined machines
- How attackers use `net use` for lateral movement — and how defenders detect it
- The role of Event IDs in both helpdesk troubleshooting and SOC monitoring

---

## 🛠️ Tools & Technologies

`Windows Server 2022` `Active Directory` `VirtualBox` `PowerShell` `RSAT` `Windows 10 Pro` `Wazuh SIEM`
