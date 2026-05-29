## Active Directory Home Lab


This project is a fully functional Active Directory home lab built on Proxmox, designed to simulate a real-world enterprise IT environment. The goal was to go beyond theory and get hands-on experience with the tools and technologies that sysadmins and IT support professionals work with every day.

The lab covers the full lifecycle of an enterprise environment. from deploying and configuring a Windows Server 2025 Domain Controller, to managing users, groups, and organizational units in Active Directory, enforcing Group Policy, configuring DNS and DHCP, setting up shared folders with proper NTFS permissions, implementing account lockout and security auditing, and automating administrative tasks with PowerShell.

Every component was deliberately configured and documented to reflect how a real enterprise environment operates. not just to make it work, but to understand why it works.

---

## Network Architecture

<img width="659" height="550" alt="Screenshot 2026-05-28 at 7 50 13 PM" src="https://github.com/user-attachments/assets/d45328d0-76a7-427d-9dae-597b92da7b89" />



---

## Lab Specifications

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE 9.1 — 192.168.1.150 |
| Domain Controller | Windows Server 2025 — DC01 — 192.168.1.10 |
| Domain | lab.local |
| Workstation | Windows 11 — workstation-01 — domain joined |
| Network | 192.168.1.x subnet, gateway 192.168.1.1 |

---

## Phase 1 — Virtualization with Proxmox

Deployed both VMs on Proxmox — DC01 running Windows Server 2025 and workstation-01 running Windows 11. Configured CPU, RAM, and disk for each VM and installed VirtIO drivers for network connectivity.

<img width="3433" height="1437" alt="Screenshot 2026-05-28 at 6 38 08 AM" src="https://github.com/user-attachments/assets/583ac587-e3ba-492e-b480-1425ba257abc" />


<img width="3429" height="1435" alt="Screenshot 2026-05-28 at 6 39 10 AM" src="https://github.com/user-attachments/assets/d6f82db3-c2d4-4d78-a3b1-fc413566b22a" />


---

## Phase 2 — Active Directory Structure

Built the OU structure with LabCorp as the top-level OU with IT, HR, Finance, and Computers underneath. Created domain users and security groups, then assigned each user to their department OU and group.

<img width="2007" height="1253" alt="Screenshot 2026-05-28 at 6 41 32 AM" src="https://github.com/user-attachments/assets/a4f879a9-42b6-4f1e-9767-028d865b4807" />


<img width="2005" height="1248" alt="Screenshot 2026-05-28 at 6 44 07 AM" src="https://github.com/user-attachments/assets/1b5f7ed9-f2ab-40c5-9001-ea7d3b632b4a" />


<img width="1998" height="1250" alt="Screenshot 2026-05-28 at 6 45 46 AM" src="https://github.com/user-attachments/assets/093cf53c-1f69-4731-b045-01150f737637" />

---

## Phase 3 — Group Policy

Configured multiple GPOs linked to specific OUs — desktop wallpaper policies, drive mapping (Z: to \\DC01\SharedFiles), firewall rules for remote management, and domain-wide password and lockout policies.

<img width="2005" height="1253" alt="Screenshot 2026-05-28 at 6 48 07 AM" src="https://github.com/user-attachments/assets/34af74d7-4160-4309-b94b-33db617281ad" />

<img width="2008" height="1249" alt="Screenshot 2026-05-28 at 6 49 23 AM" src="https://github.com/user-attachments/assets/dcaa1541-3a40-4b7f-b0fb-21e4d448f268" />

---

## Phase 4 — Shared Folders & NTFS Permissions

Created department shares at C:\shares\ with NTFS permissions scoped per security group. IT-Team gets Full Control over the IT share, HR-Team over HR, Finance-Team over Finance, and Domain Users get Modify on the Shared folder.

<img width="2002" height="1248" alt="Screenshot 2026-05-28 at 6 50 31 AM" src="https://github.com/user-attachments/assets/e435876a-7705-4195-b5c7-30b10ad988b0" />

<img width="2004" height="1248" alt="Screenshot 2026-05-28 at 6 51 05 AM" src="https://github.com/user-attachments/assets/cfbb78e5-43e5-4b31-b4a0-4640ff4d3310" />

---

## Phase 5 — DNS & DHCP

Configured DNS on DC01 with Google forwarders and verified A records for both DC01 and workstation-01. Set up a DHCP scope (LabCorp-Scope) with a range of 192.168.1.100–200, excluded the Proxmox host at 192.168.1.150, and configured scope options to point clients to the right gateway and DNS server.

<img width="2002" height="1250" alt="Screenshot 2026-05-28 at 6 55 57 AM" src="https://github.com/user-attachments/assets/90bb7333-4f73-4e41-b3fe-d0cf2dffa283" />

<img width="2006" height="1250" alt="Screenshot 2026-05-28 at 6 58 57 AM" src="https://github.com/user-attachments/assets/47062007-71fa-4bc9-af7b-25834b2e39ad" />

<img width="2002" height="1250" alt="Screenshot 2026-05-28 at 6 59 24 AM" src="https://github.com/user-attachments/assets/bb13effc-25cf-407c-9020-20e5322e8a2c" />

---

## Phase 6 — PowerShell Automation

Wrote a library of admin scripts to automate common sysadmin tasks. All scripts live in C:\Scripts\ on DC01.

### New-LabUser.ps1
Creates a new AD user, places them in the correct OU, adds them to their department security group, and automatically sets DisplayName and Department attributes.

<img width="2004" height="1250" alt="newlab-user" src="https://github.com/user-attachments/assets/5a972941-6667-4c7f-92e9-9528bc95a092" />

<img width="1743" height="978" alt="Screenshot 2026-05-28 at 7 06 26 AM" src="https://github.com/user-attachments/assets/e742eceb-b642-400e-8029-579a72492f31" />

---

### Get-UserReport.ps1
Pulls all users from the LabCorp OU and exports a status report showing Name, Username, Department, Enabled, and LockedOut status to C:\Scripts\UserReport.txt.

<img width="2000" height="1248" alt="getuser-report" src="https://github.com/user-attachments/assets/08fcda65-c3d6-46f3-8e86-3b441653be08" />

<img width="1743" height="978" alt="Screenshot 2026-05-28 at 7 06 26 AM" src="https://github.com/user-attachments/assets/e742eceb-b642-400e-8029-579a72492f31" />


---

### Bulk-GPUpdate.ps1
Queries AD for all computers in the Computers OU and remotely forces a GPO refresh on each one using Invoke-GPUpdate.

<img width="2004" height="1251" alt="Bulk-GPUpdate" src="https://github.com/user-attachments/assets/cc5daeee-4b52-4443-ae0d-56a504aceaa3" />

<img width="1740" height="978" alt="Screenshot 2026-05-28 at 7 07 28 AM" src="https://github.com/user-attachments/assets/82d59e94-1ce9-4b0d-b2ab-85ae526357ff" />


---

## Phase 7 — Account Lockout & Security Auditing

Configured account lockout policy (5 invalid attempts, 30-minute lockout) and audit policy for logon events, account management, object access, and policy changes. Tested by intentionally locking out a user and verifying the event was captured in the Security log on DC01.

<img width="2002" height="1244" alt="Screenshot 2026-05-28 at 7 12 45 AM" src="https://github.com/user-attachments/assets/1760277a-33eb-480e-b982-5ae71b049e3b" />

<img width="2004" height="1254" alt="Screenshot 2026-05-28 at 7 14 37 AM" src="https://github.com/user-attachments/assets/0528cb4f-455c-4716-aeb0-4839499ab9f8" />

---

## Real-World Troubleshooting

Some of the most valuable learning came from problems I ran into and had to work through:

- **DHCP conflict** — home router was competing with DC01's DHCP server, causing the workstation to get the wrong lease and DNS server. Fixed by setting DNS statically on the workstation and registering the correct A record in DC01's DNS manually.
- **Kerberos clock sync** — gpupdate /force was failing because the workstation's clock was out of sync with DC01. Fixed by forcing a time sync with w32tm /resync /force.
- **Remote Scheduled Tasks firewall rule** — Invoke-GPUpdate was timing out even though WinRM worked fine. Tracked it down to a missing firewall rule group and enabled it remotely via Invoke-Command.

---

## Technologies Used

- Windows Server 2025 administration
- Active Directory — users, groups, OUs, GPO
- DNS and DHCP configuration and troubleshooting
- NTFS and SMB share permissions
- Remote management via WinRM, Invoke-Command, Invoke-GPUpdate
- PowerShell scripting for AD automation
- Virtualization with Proxmox
- Security auditing and Event Viewer analysis
- Real-world network troubleshooting

---

## Certifications

- CompTIA A+ (certified)
- CompTIA Network+  (in progress)
