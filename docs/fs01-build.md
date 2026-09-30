# N3M0-FS01 — file server and departmental access

## Status — 2026-09-29

FS01 deployment, domain sign-in, departmental file operations, reciprocal access denial, and automatic Finance/HR S: drive recreation passed per operator. Windows updates complete per operator. FS01 installation ISO ejection is confirmed. Windows activation is intentionally not being purchased or pursued for this disposable project VM.

This journal distinguishes guided settings and operator completion reports from reviewed screenshots. No raw screenshots, passwords, or credentials are committed.

## Build and domain configuration

| Setting | Guided value / evidence |
|---|---|
| Platform | Existing disposable Hyper-V test server |
| Guest | N3M0-FS01; Windows Server 2025, GUI installed; exact edition/build not captured |
| VM | Generation 2; 2 vCPUs; 4096 MB fixed RAM |
| OS disk | N3M0-FS01-OS.vhdx, 80 GB |
| Data disk | N3M0-FS01-Data.vhdx, 70 GB dynamically expanding VHDX on SCSI |
| Data volume | GPT/NTFS, E:, DepartmentData; Healthy per operator; screenshot showed 70 GB volume |
| Security | Secure Boot enabled, Microsoft Windows template instructed; not independently recaptured |
| Network | Lab-Private-Switch; 10.50.10.20/24; gateway 10.50.10.1; DNS 10.50.10.10 |
| Domain | n3m0.test / N3M0; domain Administrator sign-in succeeded per operator |
| OU | N3M0-Lab/Servers creation and FS01 move included in completed build batch; not separately exported |
| Role | File Server role installation/check included in completed folder-creation batch |

VM installation and IP/DNS configuration were reported complete before domain joining. Renamed/joined through Server Manager > Local Server > Computer name > Change, restarted, then successfully signed in with domain Administrator. Added data disk through Hyper-V Settings > SCSI Controller and initialized/formatted it inside FS01 Disk Management.

## Actual folders and shares

The initial guidance used E:\Shares\Finance; the operator's actual naming supersedes that proposal:

| Share | Local folder | UNC |
|---|---|---|
| Finances | E:\Shared\Finances | \\N3M0-FS01\Finances |
| HR | E:\Shared\HR | \\N3M0-FS01\HR |

Screenshot initially showed only Finances -> E:\Shared\Finances and Shared -> E:\Shared. HR was reachable through \\N3M0-FS01\Shared\HR but not as a direct share. Created the direct HR share using folder Properties > Sharing > Advanced Sharing. A subsequent screenshot showed the network browse list, not an access error; direct UNC testing was requested and the operator reported the issue fixed.

Stopped sharing the Shared parent through Server Manager > File and Storage Services > Shares; operator confirmed the cleanup/testing batch. This removed the parent SMB share, not its folders or files. No final share-list export was captured.

## AGDLP and permissions

Accounts -> global department groups -> domain-local resource groups -> permissions:

| User | Global security group | Domain-local security group | Resource |
|---|---|---|---|
| finance | GG-Finance | DL-FS01-Finance-Modify | Finances |
| hr | GG-HR | DL-FS01-HR-Modify | HR |

Created domain-local Security groups under N3M0-Lab/Groups. Added the global groups on each domain-local group's Members tab; completion explicitly reported.

Configured each department folder via Properties > Security > Advanced:
- Disabled inheritance and converted inherited entries into explicit entries.
- Retained SYSTEM and local Administrators with Full control.
- Removed broad/general access entries; guided examples included Users, Authenticated Users, Everyone, and CREATOR OWNER if present.
- Added the corresponding domain-local group: Allow Modify, this folder/subfolders/files.
- No explicit Deny entries were instructed.

Configured each share via Advanced Sharing > Permissions:
- Removed Everyone.
- Corresponding domain-local group: Allow Change and Read.
- FS01 local Administrators: Allow Full Control.

These settings were completed per operator; raw ACL exports were not captured. Network tests validate combined SMB/NTFS behavior, not each layer independently.

## Departmental drive GPO

GPO: N3M0-Department-Drives, linked to N3M0-Lab/Users.

Path: User Configuration > Preferences > Windows Settings > Drive Maps.

| Item | Action | Location | Letter | Label | Item-level targeting |
|---|---|---|---|---|---|
| Finance | Update | \\N3M0-FS01\Finances | S: | Finance | User is a member of GG-Finance |
| HR | Update | \\N3M0-FS01\HR | S: | HR | User is a member of GG-HR |

Reconnect checked; default Authenticated Users security filtering retained. The current test users belong to their respective department. Both items use S:, so multi-department membership requires a separate design decision. Department transfer/stale mapping cleanup was not configured or tested.

## Validation

| Test | Result / evidence |
|---|---|
| FS01 domain sign-in | Success, operator reported |
| E: volume health | Healthy, operator reported; 70 GB DepartmentData shown in screenshot |
| CL02 finance -> Finances | Create/edit/save/reopen/rename/delete passed, operator batch confirmation |
| CL02 finance -> HR | Access denied, operator batch confirmation |
| CL03 hr -> HR | Create/edit/save/reopen/rename/delete passed, operator batch confirmation |
| CL03 hr -> Finances | Access denied, operator batch confirmation |
| Finance S: automatic recreation | Disconnected manual S:, signed out/in; returned and opened, operator confirmed |
| HR S: automatic recreation | Operator explicitly confirmed both clients checked using disconnect/sign-out workflow |
| FS01 Windows Update | Up to date, operator reported |
| FS01 ISO ejection | Confirmed by operator |
| FS01 activation | Intentionally not pursued for this disposable project VM |
| New backups / restore testing | None claimed |

Initial drive visibility was insufficient evidence because S: had been manually mapped. The later recreation tests supersede that ambiguity.

## Recovery and next work

This is an authorized disposable test host; host backups are not a prerequisite. DC01's existing backup predates these AD/GPO changes. No FS01 backup or recovery test is claimed.

For troubleshooting, retain console access and distinguish share removal from file deletion. Drive-map policy removal alone does not guarantee existing persistent mappings disappear; clean up mappings explicitly if withdrawing this policy. Rollback procedures have not been exercised.

Separate administration identities and narrowly scoped delegation were completed and validated on 2026-09-29; see the Windows client/domain-user journal for the permission design and tests. FS01 cleanup is complete for the current build. The next major infrastructure milestone is DC02 replication.
