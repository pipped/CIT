# Active Directory Lab

## Overview

This lab documents a hands-on Active Directory deployment using Windows Server on a virtualized environment. It covers forest and domain setup, organizational unit design, user and group management, Group Policy, and DNS integration — simulating a real IT environment where identity and access management are core responsibilities.

The goal was to practice the full lifecycle of an AD environment: from domain controller promotion through domain-joined workstations with policy-enforced configurations.

## Environment

| Component | Details |
|---|---|
| Hypervisor | Hyper-V on Windows 10/11 host |
| Domain Controller | Windows Server 2022 (VM) |
| Workstation | Windows 10 Pro (VM) |
| Domain Name | `lab.local` |
| DC Hostname | `DC01` |
| DC IP Address | `192.168.10.10` |
| Workstation IP | `192.168.10.20` |
| DNS | AD-integrated, hosted on DC01 |

## Lab Scope

- Promote a Windows Server VM to domain controller
- Configure a new AD forest and domain (`lab.local`)
- Design and build an OU structure
- Create users and security groups
- Apply Group Policy Objects (GPOs)
- Join a Windows 10 workstation to the domain
- Validate DNS resolution and domain authentication

---

## Lab Walkthrough

### 1. Windows Server Setup

Before promoting to a domain controller, the Windows Server VM was configured with a static IP and renamed.

Steps performed:

- Assigned static IP `192.168.10.10`, subnet `255.255.255.0`, gateway `192.168.10.1`
- Set preferred DNS to `127.0.0.1` (self, since this server will host DNS)
- Renamed the server to `DC01` via System Properties
- Rebooted to apply hostname change

Key concept: A domain controller must have a static IP. Dynamic addressing causes AD replication and DNS failures.

---

### 2. Installing Active Directory Domain Services

The AD DS role was installed via Server Manager before promoting the server.

Steps performed:

- Opened **Server Manager → Add Roles and Features**
- Selected **Active Directory Domain Services**
- Accepted the automatic inclusion of required features (Group Policy Management, DNS Server, Remote Server Administration Tools)
- Completed the role installation wizard

After installation, a post-deployment notification appeared prompting domain controller promotion.

---

### 3. Domain Controller Promotion

The server was promoted to a domain controller for a new forest.

Configuration used:

| Setting | Value |
|---|---|
| Deployment operation | Add a new forest |
| Root domain name | `lab.local` |
| Forest functional level | Windows Server 2016 |
| Domain functional level | Windows Server 2016 |
| DNS Server | Enabled |
| Global Catalog | Enabled |
| DSRM password | Set (recovery-only use) |
| NetBIOS domain name | `LAB` |
| NTDS / SYSVOL paths | Default |

After promotion, the server automatically rebooted and joined `LAB\DC01`.

Key concept: The Directory Services Restore Mode (DSRM) password is used to recover AD if the domain becomes unreachable — it is not the same as the administrator password.

---

### 4. Verifying DNS and AD Installation

After the reboot, DNS and AD health were validated.

Checks performed:

- Opened **DNS Manager** — confirmed forward lookup zone `lab.local` was created automatically
- Verified `_msdcs`, `_sites`, `_tcp`, and `_udp` SRV records were present (required for domain-joined clients to locate the DC)
- Ran `dcdiag /test:dns` in an elevated command prompt — all tests passed
- Ran `nslookup lab.local` — resolved to `192.168.10.10`

Key concept: AD relies entirely on DNS for service location. Missing SRV records prevent workstations from finding the domain controller.

---

### 5. Organizational Unit Structure

A logical OU structure was created in **Active Directory Users and Computers (ADUC)** to reflect a standard company layout.

OU hierarchy built:

```
lab.local
└── LAB
    ├── IT
    │   ├── Users
    │   └── Computers
    ├── HR
    │   ├── Users
    │   └── Computers
    ├── Finance
    │   ├── Users
    │   └── Computers
    └── Servers
```

Steps performed:

- Right-clicked `lab.local` → **New → Organizational Unit**
- Created top-level `LAB` OU with sub-OUs per department
- Each department received separate `Users` and `Computers` OUs to allow targeted GPO linking

Key concept: OUs are the primary unit for applying Group Policy. Separating users from computers allows policies to be applied to each independently.

---

### 6. User and Group Creation

User accounts and security groups were created to simulate a real department structure.

Users created:

| Name | Username | Department | OU |
|---|---|---|---|
| Dylan Sokolov | `d.sokolov` | IT | LAB/IT/Users |
| Alex Carter | `a.carter` | HR | LAB/HR/Users |
| Sam Rivera | `s.rivera` | Finance | LAB/Finance/Users |

Steps performed for each user:

- Right-clicked target OU → **New → User**
- Set first name, last name, and UPN (e.g. `d.sokolov@lab.local`)
- Set initial password, checked **User must change password at next logon**
- Unchecked **Account is disabled**

Security groups created:

| Group Name | Type | Scope | Members |
|---|---|---|---|
| IT-Admins | Security | Global | d.sokolov |
| HR-Staff | Security | Global | a.carter |
| Finance-Staff | Security | Global | s.rivera |

Key concept: Security groups are used to assign permissions and apply policies to sets of users without managing each account individually. Global scope is appropriate for groups used within a single domain.

---

### 7. Group Policy Objects

Two GPOs were created and linked to demonstrate policy enforcement across the domain.

**GPO 1: Desktop Lockdown (linked to LAB/HR/Computers)**

Settings configured:

- **User Configuration → Administrative Templates → System**: Prevent access to registry editing tools — Enabled
- **User Configuration → Administrative Templates → Start Menu and Taskbar**: Remove and prevent access to the Shut Down command — Enabled
- **Computer Configuration → Windows Settings → Security Settings → Account Policies → Password Policy**:
  - Minimum password length: 10
  - Password must meet complexity requirements: Enabled
  - Maximum password age: 90 days

**GPO 2: IT Admin Policy (linked to LAB/IT)**

Settings configured:

- **Computer Configuration → Windows Settings → Security Settings → Local Policies → User Rights Assignment**: Allow log on locally — added IT-Admins group
- **Computer Configuration → Administrative Templates → Windows Components → Remote Desktop Services**: Allow users to connect remotely — Enabled

Steps performed:

- Opened **Group Policy Management** from Server Manager → Tools
- Right-clicked target OU → **Create a GPO in this domain and link it here**
- Named the GPO, then right-clicked → **Edit** to open the Group Policy Management Editor
- Applied settings and closed editor
- Ran `gpupdate /force` on the affected machine to apply immediately

Key concept: GPOs linked higher in the OU hierarchy inherit downward unless **Block Inheritance** is set. The **Enforced** flag on a GPO prevents child OUs from blocking it.

---

### 8. Joining a Workstation to the Domain

A Windows 10 Pro VM was joined to `lab.local` to validate the domain environment end-to-end.

Steps performed on the workstation:

- Set static IP `192.168.10.20`, subnet `255.255.255.0`, gateway `192.168.10.1`
- Set preferred DNS to `192.168.10.10` (DC01)
- Confirmed connectivity: `ping 192.168.10.10` — successful
- Confirmed DNS resolution: `nslookup lab.local` — resolved correctly
- Opened **System Properties → Computer Name → Change**
- Selected **Domain**, entered `lab.local`
- Authenticated with `LAB\Administrator` credentials
- Restarted the workstation

After reboot, logged in as `LAB\d.sokolov` and verified domain authentication succeeded.

Moved the workstation computer object from the default `Computers` container to `LAB/IT/Computers` using ADUC.

Key concept: By default, domain-joined computers land in `CN=Computers`, not in a custom OU. They must be manually moved (or redirected with `redircmp`) for GPOs linked to custom OUs to apply.

---

### 9. Validating Policy Application

GPO enforcement was verified on the domain-joined workstation.

Validation steps:

- Logged in as `LAB\a.carter` (HR user) on the Windows 10 VM
- Confirmed registry editor was blocked: `regedit` returned an access denied message
- Ran `gpresult /r` to confirm the Desktop Lockdown GPO was applied
- Logged in as `LAB\d.sokolov` (IT admin) and confirmed Remote Desktop was accessible and additional rights were granted
- Ran `gpresult /h gpresult.html` to generate a full policy report for review

---

## Troubleshooting Notes

| Issue | Cause | Resolution |
|---|---|---|
| Workstation cannot find domain during join | DNS pointed to wrong server | Set workstation DNS to DC IP (`192.168.10.10`) |
| GPO not applying after edit | Policy not refreshed | Ran `gpupdate /force` on the affected machine |
| User locked out on first login | Password policy conflict | Reset password in ADUC, unchecked **Must change at next logon** temporarily |
| SRV records missing after promotion | DNS zone not auto-created | Restarted **Net Logon** service to re-register DNS records |
| Computer object still in default container | Not moved after domain join | Moved manually in ADUC to correct department OU |

---

## Commands Reference

```powershell
# Verify AD installation
Get-WindowsFeature AD-Domain-Services

# Run domain controller diagnostics
dcdiag /test:dns
dcdiag /v

# Force Group Policy update
gpupdate /force

# View applied policies for current user/computer
gpresult /r
gpresult /h gpresult.html

# Test DNS resolution
nslookup lab.local
nslookup dc01.lab.local

# Check domain join status
(Get-WmiObject Win32_ComputerSystem).PartOfDomain

# List AD users in an OU
Get-ADUser -Filter * -SearchBase "OU=Users,OU=IT,OU=LAB,DC=lab,DC=local"

# Create a new AD user via PowerShell
New-ADUser -Name "Test User" -GivenName "Test" -Surname "User" `
  -SamAccountName "t.user" -UserPrincipalName "t.user@lab.local" `
  -Path "OU=Users,OU=IT,OU=LAB,DC=lab,DC=local" `
  -AccountPassword (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force) `
  -Enabled $true

# Move a computer object to a new OU
Move-ADObject -Identity "CN=WORKSTATION01,CN=Computers,DC=lab,DC=local" `
  -TargetPath "OU=Computers,OU=IT,OU=LAB,DC=lab,DC=local"
```

---

## Skills Demonstrated

- Windows Server role installation (AD DS, DNS)
- Forest and domain creation
- Domain controller promotion and post-deployment validation
- DNS configuration and SRV record verification
- Organizational Unit design and structure
- User account and security group creation
- Group Policy Object creation, linking, and enforcement
- Workstation domain join and computer object management
- GPO troubleshooting with `gpresult` and `gpupdate`
- PowerShell for AD administration

## Summary

This lab demonstrates a complete Active Directory environment from initial domain controller promotion through workstation domain join and Group Policy enforcement. It reflects the identity, access, and policy management skills relevant to IT support, systems administration, and entry-level security roles.
