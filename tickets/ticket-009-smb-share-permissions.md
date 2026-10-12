# Ticket #009 — SMB Share Permission Troubleshooting

**Ticket Type:** File Access / Permissions  
**Environment:** Windows Server 2025, Windows 11 Pro, Active Directory  
**Systems:** DC01 (File Server), CLIENT01 (Workstation)  
**Status:** Resolved  
**Exercise Type:** Hands-on Lab — Simulated Incident

## 1. Issue Description

A domain user, `HOMELAB\john.smith`, reported being able to open files on the IT department's network share but being unable to save changes.

**Affected Resource:** `\\DC01\IT`

**Expected Behavior:** The user should be able to create, read, modify, rename, and delete files within the authorized IT share.

## 2. Investigation

The following troubleshooting steps were performed:

1. Reviewed the SMB share permissions on DC01.
2. Confirmed the NTFS permissions granted the `IT_File_Modify` security group Modify access.
3. Verified that the user could initially create and modify a test file from CLIENT01.
4. Intentionally changed the SMB share permission for `Everyone` from Full Control to Read to reproduce the issue.
5. Attempted to modify and save `Ticket009-Test.txt` from CLIENT01.
6. Confirmed the user could read the file but could not save modifications.

## 3. Root Cause

The SMB share permission was restricted to **Read**, while the user's NTFS permissions allowed **Modify**.

When accessing a file over an SMB network share, both share permissions and NTFS permissions apply. The effective access is constrained by the more restrictive applicable permissions.

Although the user had NTFS Modify permission through Active Directory security group membership, the SMB share's Read permission prevented writing changes over the network.

## 4. Resolution

On DC01, restored the SMB share permission:

**Everyone → Full Control**

The existing NTFS permissions were preserved:

**IT_File_Modify → Modify**

This configuration allows NTFS permissions to enforce the intended access restrictions for the IT folder.

## 5. Verification

After restoring the share permissions:

1. Accessed `\\DC01\IT` from CLIENT01.
2. Opened `Ticket009-Test.txt` using the authorized domain user account.
3. Successfully modified and saved the file.
4. Reopened the file and confirmed the changes persisted.

**Result:** Write access was successfully restored.

## 6. Lessons Learned

- SMB share permissions and NTFS permissions are separate access-control layers.
- A user can have NTFS Modify permission and still be prevented from writing through a restrictive SMB share permission.
- Always verify both permission layers when troubleshooting network share access.
- Reproduce the reported issue, identify the root cause, apply the correction, and verify functionality afterward.

## 7. Skills Demonstrated

- SMB share administration
- NTFS permission analysis
- Active Directory security group access control
- Windows file access troubleshooting
- Root cause analysis
- Post-remediation verification