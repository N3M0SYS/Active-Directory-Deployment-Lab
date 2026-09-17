# Windows clients, domain users, and Group Policy

## Status — 2026-09-17

N3M0-CL01, N3M0-CL02, and N3M0-CL03 run Windows 11 Pro on the Hyper-V test server and are joined to n3m0.test. DHCP leases were reviewed in a screenshot. Domain sign-ins, initial password changes, Windows updates, and the workstation sign-in notice succeeded per operator reports. Activation remains pending. ISO ejection was announced but not explicitly confirmed.

This journal covers work performed during September 15–17. It records guided configuration separately from screenshots and operator-reported outcomes. No credentials or raw screenshots are committed.

## Build settings and installation

| Setting | Guided value |
|---|---|
| Platform | Existing Hyper-V test server |
| Clients | N3M0-CL01, N3M0-CL02, N3M0-CL03 |
| Guest OS | Windows 11 Pro |
| Generation | 2 |
| Virtual processors | 2 each |
| Memory | 4096 MB fixed each |
| OS disk | 80 GB dynamically expanding VHDX each |
| Network | One adapter on Lab-Private-Switch |
| Secure Boot | Enabled, Microsoft Windows template |
| vTPM | Enabled |
| Installation media | Win11_25H2_English_x64_v2 (1).iso |
| Installed build number | Not captured |

CL01 settings screenshots showed 2 processors, 4096 MB, the private switch, and enabled Secure Boot/vTPM before troubleshooting. Other per-VM sizing and final security settings were not independently exported. Secure Boot was temporarily disabled as a diagnostic test; restoring it was instructed before installation.

Created clients through Hyper-V Manager, attached the Windows ISO, installed Pro to the new virtual disks, and completed initial setup using the work/school flow and Domain join instead to create local accounts. Domain joining was then performed separately from Windows System properties.

## Boot troubleshooting and resolution

- CL01 displayed the Hyper-V UEFI boot summary: DVD bootloader failed, no OS on the new disk, and no network boot image.
- Confirmed DVD-first boot order and the attached x64 Windows ISO.
- Confirmed Microsoft Windows Secure Boot template and enabled vTPM.
- Mounted the ISO on the host: Properties showed 7.88 GB (8,471,603,200 bytes), and efi/boot/bootx64.efi existed. This checked readability, not cryptographic integrity.
- Temporarily disabling Secure Boot did not resolve the failure.
- Testing the previously successful Windows Server 2025 ISO produced the same problem; Server was not installed on the client.
- Operator identified ScreenConnect console input as the issue: connecting a physical keyboard/mouse allowed the boot prompt to be accepted.
- Reattached Windows 11 ISO and completed installation. No evidence established an ISO defect or Secure Boot rejection.

Operational lesson: confirm boot-prompt keystrokes reach the VM when accessing Hyper-V through a remote-control session.

## Client and account mapping

| Computer | Role | Local setup account | Domain account | Observed DHCP address |
|---|---|---|---|---|
| N3M0-CL01 | IT workstation | IT Admin | rafa@n3m0.test | 10.50.10.100 |
| N3M0-CL02 | Finance workstation | Finance Team | finance.user@n3m0.test | 10.50.10.101 |
| N3M0-CL03 | HR workstation | HR Team | hr.user@n3m0.test | 10.50.10.102 |

Local setup accounts remain separate from domain identities. The standard domain users were not granted administrative group membership in this workflow. CL01's IT role does not itself confer elevated privileges.

DHCP server: N3M0-DC01, scope N3M0-Clients. Scope configuration remains /24, gateway 10.50.10.1, DNS 10.50.10.10, and suffix n3m0.test. The Address Leases screenshot showed all three client names and addresses. These are dynamic observations, not static assignments or reservations. Operator reported leasing works properly; a client-side Details screenshot showing every option was not supplied.

## Domain joins and OU layout

Joined each client through Settings > System > About > Domain or workgroup > Change, selecting n3m0.test and supplying domain Administrator credentials. CL01's welcome message and restart were explicitly reported. The subsequent guided CL02/CL03 joins, computer-object moves, and successful domain sign-ins were confirmed through operator completion reports.

Created the following organization in Active Directory Users and Computers:

| OU path under n3m0.test | Contents |
|---|---|
| N3M0-Lab/Users | rafa, finance.user, hr.user |
| N3M0-Lab/Workstations | N3M0-CL01, N3M0-CL02, N3M0-CL03 |
| N3M0-Lab/Groups | GG-Finance, GG-HR |

Moved the three client computer objects from the default Computers container to Workstations. No domain controller move was instructed.

Created each standard user with a temporary password and User must change password at next logon. Operator confirmed rafa signed in and changed the password, followed by successful Finance and HR desktop sign-ins after the same guided process.

## Departmental groups

| Group | Scope/type | Guided member |
|---|---|---|
| GG-Finance | Global / Security | finance.user |
| GG-HR | Global / Security | hr.user |

Group creation and membership were part of the completed guided batch; no independent membership screenshot/export was supplied. These groups are prepared for future resource permissions. They do not by themselves restrict workstation sign-ins, grant administration, or isolate network traffic. Separate administration identities/delegation remain pending.

## First workstation Group Policy

Created and linked N3M0-Workstations-LogonNotice to N3M0-Lab/Workstations in Group Policy Management.

Path: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options.

| Policy | Defined value |
|---|---|
| Interactive logon: Message title for users attempting to log on | N3M0 Security Lab |
| Interactive logon: Message text for users attempting to log on | Authorized lab use only. This workstation is part of the n3m0.test training environment. |

CL02 showed the notice following restart. Operator subsequently reported the remaining baseline checks were good, confirming notices on CL01 and CL03 and completion of client updates. This validates this visible computer policy; it is not a comprehensive hardening assessment or policy-results report.

## Validation and remaining work

| Check | Result / evidence |
|---|---|
| Three client DHCP leases | Reviewed DC01 Address Leases screenshot |
| CL01 domain join | Welcome message and restart reported |
| All client computer objects in Workstations | Operator confirmed moves |
| Domain user sign-ins and password changes | Operator reported successful completion |
| Logon-notice GPO | CL02 explicitly passed; other clients confirmed in baseline completion report |
| Windows client updates | Complete per operator; individual KB/build inventory not captured |
| Windows 11 Pro activation | Pending on clients |
| Installation ISO ejection | Operator intended to eject; completion unconfirmed |
| Client DNS registrations and detailed applied-policy report | Not independently captured |
| New backup or restore test | None claimed |

Next: deploy N3M0-FS01 (planned address 10.50.10.20) and test Finance/HR share and NTFS permissions. DC02, separate delegated administration accounts, and monitoring integration remain future work.

## Recovery notes

This remains an authorized disposable lab; host backups are not a prerequisite. Existing DC01 backup predates these AD changes and client deployment; no refreshed backup or client backup is claimed. Local accounts remain available for local recovery. If the notice policy needs to be withdrawn, clear both defined notice values and allow clients to process that change before removing the GPO link; rollback has not been tested.
