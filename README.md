# TechNova IT Support — Ticket #1042

## Windows Server 2022 — SMB & NTFS Permissions Troubleshooting

**Ticket Status:** Resolved  
**Priority:** Medium  
**Category:** File Sharing / Access Management  
**Environment:** Windows Server 2022, Windows 11 Pro, VMware Workstation  
**Lab Type:** Simulated IT Support Incident

---

## 1. Incident Description

An employee named Pedro reported that he could not access a restricted company folder through a network share.

The IT department was tasked with investigating the access issue and providing temporary read-only access following management approval.

**Business Requirement:** Pedro must be able to open and read files in the restricted folder without being able to create, modify, or delete company data.

## 2. Lab Environment

| Component | Configuration |
|---|---|
| File Server | DC01 — Windows Server 2022 |
| Client Workstation | PC01 — Windows 11 Pro |
| Virtualization | VMware Workstation |
| Network | VMware NAT — 192.168.72.0/24 |
| File Server IP | 192.168.72.10 |
| Shared Folder | `\\192.168.72.10\empresa` |
| Restricted Folder | `C:\empresa\it privado test` |
| Test Account | `DC01\pedro` |
| Authentication | Local Windows account |

**Note:** This lab uses local Windows accounts and SMB authentication. Active Directory domain authentication is outside the scope of this ticket.

## 3. Troubleshooting and Resolution

### Step 1 — Investigate the Access Issue

Connected from PC01 to the shared folder hosted on DC01.

Verified the network share and identified that the restricted folder required additional NTFS permissions.

### Step 2 — Verify User Authentication

Used the following command to authenticate with Pedro's local account:

`net use \\192.168.72.10\empresa /user:DC01\pedro *`

Verified the authenticated session using Windows Server Shared Folders management.

During troubleshooting, a failed authentication attempt was investigated using Windows Security Event ID 4625.

### Step 3 — Review SMB Share Permissions

Reviewed the SMB share permissions for the `empresa` share.

The share was configured to allow Everyone the **Read** and **Change** permissions.

Effective access was further restricted using NTFS permissions.

### Step 4 — Review NTFS Permissions

Opened the Security settings of:

`C:\empresa\it privado test`

Reviewed inheritance and the folder's access control entries.

The initial access test confirmed that Pedro could not open the restricted folder.

### Step 5 — Obtain Authorization

Before changing access permissions, management approval was required.

Temporary read-only access was approved for Pedro.

This followed the principle of least privilege.

### Step 6 — Grant Read-Only Access

Added the account `DC01\pedro` to the folder's NTFS permissions.

Granted:

- Read & execute
- List folder contents
- Read

Did not grant Modify, Write, or Full Control.

### Step 7 — Validate Access from PC01

Authenticated to the SMB share using Pedro's account.

Confirmed that Pedro could open the restricted folder.

Attempted to save a text file inside the folder.

**Result:** Windows displayed a permission error indicating that Pedro did not have permission to save the file in that location.

### Step 8 — Verify the NTFS Access Control List

On DC01, executed:

`Get-Acl "C:\empresa\it privado test" | Format-List`

The output confirmed that `DC01\pedro` had ReadAndExecute permissions, while administrative and system accounts retained elevated permissions.

## 4. Root Cause Analysis

Pedro initially lacked the required NTFS permissions to access the restricted folder.

The SMB share was accessible, but NTFS permissions prevented the user from opening the protected directory.

After management authorization, the folder permissions were updated to permit read-only access.

## 5. Final Resolution

**Final result:**

- SMB authentication succeeded.
- Pedro could access the shared folder.
- Pedro could open the restricted directory.
- Pedro was denied permission to save changes.
- Read-only access was verified through functional testing and ACL inspection.

**Ticket Status: Resolved**

## 6. Screenshots and Evidence

Screenshots will document:

1. SMB authentication and user session verification.
2. Initial access denial.
3. NTFS permission configuration.
4. PowerShell ACL verification.
5. Successful read access.
6. Denied write operation.

## 7. Technical Skills Demonstrated

- Windows Server 2022 administration
- Windows 11 troubleshooting
- SMB file sharing
- NTFS permissions and inheritance
- Local user authentication
- Windows Security Event Viewer
- PowerShell ACL inspection
- Least-privilege access control
- Access troubleshooting and validation
- IT ticket documentation

## 8. Lessons Learned

SMB share permissions and NTFS permissions work together to determine access to shared files.

Successful authentication does not automatically authorize access to protected resources.

Access changes should be approved, limited to business requirements, and verified using the affected user's credentials.

---

**TechNova Solutions — Hands-on IT Support Portfolio**  
**Ticket #1042 | SMB & NTFS Permissions Troubleshooting**
