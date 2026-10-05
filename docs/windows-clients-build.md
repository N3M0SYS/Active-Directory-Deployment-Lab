# Windows clients, domain users, and Group Policy

## Status — 2026-09-29

N3M0-CL01, N3M0-CL02, and N3M0-CL03 run Windows 11 Pro on the Hyper-V test server and are joined to n3m0.test. DHCP leases, domain sign-ins, workstation notice, client updates, departmental drive mappings, and the current least-privilege administration workflow have been validated as described below. Installation ISOs were ejected from all three clients per operator. Windows activation is intentionally not being purchased or pursued for these disposable project VMs.

No credentials or raw screenshots are committed.

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

| Computer | Role | Local setup account | Standard domain account | Observed DHCP address |
|---|---|---|---|---|
| N3M0-CL01 | IT management workstation | IT Admin | lchen@n3m0.test and rafa@n3m0.test are IT users; rafa is the operator's daily-use account | 10.50.10.100 |
| N3M0-CL02 | Finance workstation | Finance Team | finance@n3m0.test | 10.50.10.101 |
| N3M0-CL03 | HR workstation | HR Team | hr@n3m0.test | 10.50.10.102 |

Local setup accounts remain separate from domain identities. Standard domain users were not granted administrative group membership merely because of their workstation or department role.

DHCP server: N3M0-DC01, scope N3M0-Clients. Scope configuration remains /24, gateway 10.50.10.1, DNS 10.50.10.10, and suffix n3m0.test. The Address Leases screenshot showed all three client names and addresses. These are dynamic observations, not static assignments or reservations.

## Domain joins and OU layout

Joined each client through Settings > System > About > Domain or workgroup > Change, selecting n3m0.test and supplying domain Administrator credentials. CL01's welcome message and restart were explicitly reported. The subsequent CL02/CL03 joins, computer-object moves, and successful domain sign-ins were confirmed through operator completion reports.

Current Active Directory layout relevant to these clients:

| OU path under n3m0.test | Contents |
|---|---|
| N3M0-Lab/Admins | rafa-admin |
| N3M0-Lab/Users | finance, hr, lchen, rafa |
| N3M0-Lab/Workstations | N3M0-CL01, N3M0-CL02, N3M0-CL03 |
| N3M0-Lab/Groups | GG-Finance, GG-HR, GG-IT, DL-FS01-Finance-Modify, DL-FS01-HR-Modify |

The standard-user identities currently observed are:
- Finance Team: `finance@n3m0.test`
- HR Team: `hr@n3m0.test`
- Lucy Chen: `lchen@n3m0.test` (fictional IT user)
- Rafa: `rafa@n3m0.test` (standard daily-use IT account)

Finance and HR had Password never expires removed during the 2026-09-29 cleanup. Rafa was created as a standard user. Lucy remained a standard user.

## Departmental and IT groups

| Group | Scope/type | Current members / role |
|---|---|---|
| GG-Finance | Global / Security | finance |
| GG-HR | Global / Security | hr |
| GG-IT | Global / Security | lchen, rafa |
| DL-FS01-Finance-Modify | Domain Local / Security | contains GG-Finance for FS01 resource permissions |
| DL-FS01-HR-Modify | Domain Local / Security | contains GG-HR for FS01 resource permissions |

`GG-IT` models IT department membership only; it does not grant Domain Admin, local admin, GPO, or delegated AD rights.

## Separate administration identity and scoped delegation

A separate `N3M0-Lab/Admins` OU was created and `rafa-admin@n3m0.test` was added there as the operator's privileged identity. `rafa-admin` is not a Domain Admin and is not a member of GG-IT.

The intended workflow is:
- Sign in to CL01 using standard `N3M0\rafa`.
- Launch Active Directory Users and Computers with **Run as different user** using `N3M0\rafa-admin`.
- Use `rafa-admin` only for delegated AD administration.

CL01 received the RSAT **Active Directory Domain Services and Lightweight Directory Services Tools** feature. CL02 and CL03 do not require RSAT.

Delegation is scoped to `N3M0-Lab/Users`:
- Reset user passwords and force password change at next logon.
- Custom delegation on User objects: Read `lockoutTime` and Write `lockoutTime` for account unlocking.

No Domain Admin membership, user-creation delegation, group-membership delegation, GPO delegation, server-administration delegation, or broad domain-level privilege was granted in this workflow.

### Delegation validation — 2026-09-29

| Test | Expected | Result |
|---|---|---|
| rafa-admin resets Finance password | Allowed | Passed |
| Force Finance password change at next logon | Allowed | Passed |
| Finance signs in with temporary password, changes it, reaches desktop | Allowed | Passed |
| rafa-admin creates a new user in N3M0-Lab/Users | Denied | Passed: New User was unavailable |
| rafa-admin modifies GG-IT membership | Denied | Passed: Add was disabled |
| rafa-admin unlocks HR after intentional failed-logon test | Allowed | Passed |
| HR signs in with correct password after delegated unlock | Allowed | Passed |
| Standard rafa resets HR password | Denied | Passed: Access is denied |

ADUC displayed **Reset Password** on the `rafa-admin` account in the Admins OU, but no reset was attempted. Therefore the separate Admins-OU password-reset boundary is not claimed as validated.

## Domain account-lockout policy

To validate delegated account unlocking, the Default Domain Policy was configured and observed as:

| Setting | Value |
|---|---|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 10 minutes |
| Reset account lockout counter after | 10 minutes |
| Allow Administrator account lockout | Enabled |

HR was used for the controlled lockout/unlock validation. After the failed-logon sequence, `rafa-admin` successfully applied the unlock action and HR subsequently authenticated with the correct password.

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
| Standard user sign-ins | Operator reported successful completion |
| Logon-notice GPO | CL02 explicitly passed; other clients confirmed in baseline completion report |
| Windows client updates | Complete per operator; individual KB/build inventory not captured |
| Windows activation | Intentionally not pursued for disposable project VMs; no license purchase planned |
| Installation ISO ejection | Complete on all three clients per operator confirmation |
| CL01 RSAT AD tools | Installed and ADUC launched successfully |
| Separate admin/delegation workflow | Passed positive and negative tests described above |
| Client DNS registrations and detailed applied-policy report | Not independently captured |
| New backup or restore test | None claimed |

## 2026-09-29 — Departmental drive mappings verified

N3M0-Department-Drives is linked to N3M0-Lab/Users. User Configuration > Preferences > Windows Settings > Drive Maps contains two Update items, Reconnect checked, drive S:. Finance maps \\N3M0-FS01\Finances with label Finance and user-security-group targeting GG-Finance; HR maps \\N3M0-FS01\HR with label HR and targeting GG-HR. Default Authenticated Users GPO filtering retained per guided workflow.

Operator initially had manual S: mappings, so their initial presence was not sufficient evidence. After disconnecting the mappings and signing out/in with finance on CL02 and hr on CL03, the operator confirmed both S: drives returned automatically and opened. Departmental file create/edit/save/reopen/rename/delete tests and reciprocal access denial also passed per operator.

## Recovery notes

This remains an authorized disposable lab; host backups are not a prerequisite. Existing DC01 backup predates these AD changes, client deployment, FS01 permissions, and delegation changes; no refreshed backup or client backup is claimed. Local client setup accounts remain available for local recovery. Rollback of the new delegation or lockout policy has not been exercised.

## 2026-10-05 — Dual DNS and live DC02 authentication

DC01 DHCP scope option 006 now advertises 10.50.10.10 then 10.50.10.11. CL01 renewed its lease; ipconfig /all captured hostname N3M0-CL01, DHCP enabled, 10.50.10.100/24, gateway 10.50.10.1, n3m0.test primary/connection suffix, DHCP server 10.50.10.10, and both DNS servers. CL02/CL03 options were not recaptured.

In CL01's standard rafa session, whoami showed n3m0\\rafa. An elevated Administrator window temporarily set DC02 as preferred KDC with klist add_bind. Rafa's normal window purged its own tickets and successfully requested host/N3M0-DC02.n3m0.test. New TGT and service ticket both showed Client rafa, AES-256 encryption, and Kdc Called N3M0-DC02.n3m0.test. This proves fresh online Kerberos through DC02 beyond cached interactive sign-in; full DC01 outage failover was not tested. klist purge_bind succeeded in an elevated window to remove the preference. Rafa's gpupdate /target:user /force passed. No standard-user or delegated-admin privileges were expanded. See [DC02 journal](dc02-build.md).
