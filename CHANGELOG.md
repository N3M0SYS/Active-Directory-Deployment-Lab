# Change log

## 2026-10-05 — Domain deployment portfolio scope

- Retitled the project documentation Active Directory Deployment Lab and rebuilt the README around demonstrated domain deployment and administration outcomes.
- Replaced the cross-discipline roadmap with a domain deployment checklist, retaining unfinished infrastructure checks and validation limits.
- Moved planned penetration testing/detection and AI application security into a three-project series document; no follow-on implementation is claimed.
- Removed future attacker/monitoring sizing from the current domain architecture. Retained existing guest inventory and historical change records.
- Renamed the GitHub repository to Active-Directory-Deployment-Lab and updated its domain-deployment description and topics; visibility remains private.

## 2026-10-05 — DC02 and bidirectional replication verified

- Deployed N3M0-DC02 at 10.50.10.11 as writable AD DS/DNS/GC; all FSMO roles remain on DC01.
- Corrected public DNS client settings causing DC01 error 8524; final DC01 resolver list is only 10.50.10.10.
- Verified all five AD naming contexts in both directions, automatic AD object and DNS record changes both ways, basic DNS, Advertising, SYSVOL/NETLOGON, and DFSR State 4 on both DCs. Final summary: zero failures.
- Configured root PDC external time and DC02 domain time; disabled guest Hyper-V Windows Time provider on both. Synchronized sources and Leap Indicator 0 observed; transient DC02 resync failure cause remains unproven.
- Updated DHCP scope DNS option to DC01 then DC02; CL01 lease/options captured.
- Verified fresh standard rafa TGT/service tickets issued by DC02 and successful user policy refresh. Removed temporary KDC preference, test group/record, and debug logging.
- Added DC02 journal and refreshed current documentation. No full outage failover, DC02 updates/ISO cleanup, external-forwarder capture, or new backup claimed. DC01 local ADUC status anomaly remains documented.

## 2026-09-29 — FS01 installation media cleanup complete

- Operator confirmed the Windows Server installation ISO was ejected from N3M0-FS01 in Hyper-V.
- Marked FS01 installation-media cleanup complete in the README, FS01 build journal, and roadmap.
- FS01 activation remains intentionally out of scope for this disposable project VM.
- Next major infrastructure milestone is DC02 deployment and AD/DNS replication validation.

## 2026-09-29 — Windows activation intentionally out of scope

- Recorded the operator decision not to purchase or pursue Windows activation for the disposable project VMs.
- Removed activation from the active to-do list for the Windows 11 clients and N3M0-FS01.
- Preserved FS01 installation ISO ejection as an open cleanup item.
- Updated README, Windows client journal, FS01 journal, and roadmap so activation is documented as intentionally out of scope rather than pending.

## 2026-09-29 — Separate administration and least-privilege delegation

- Corrected the current AD user inventory to Finance Team (`finance`), HR Team (`hr`), Lucy Chen (`lchen`, fictional IT user), and Rafa (`rafa`, standard daily-use IT account).
- Added `GG-IT` as a Global/Security department group containing `lchen` and `rafa`; it grants no administrative privilege by itself.
- Added N3M0-Lab/Admins and the separate `rafa-admin` identity. `rafa-admin` is not Domain Admin and is not in GG-IT.
- Installed RSAT Active Directory DS/LDS tools on CL01 and validated the intended workflow: sign into Windows as standard `rafa`, then launch ADUC with **Run as different user** using `rafa-admin`.
- Delegated password reset/force-change on N3M0-Lab/Users to `rafa-admin`.
- Added custom User-object delegation for Read/Write `lockoutTime` to support account unlocking.
- Positive tests passed: `rafa-admin` reset Finance's password, forced change at next sign-in, and Finance authenticated and changed it; `rafa-admin` later unlocked HR after the controlled failed-logon test, and HR authenticated successfully.
- Negative tests passed: `rafa-admin` could not create users (New User unavailable), could not modify GG-IT membership (Add disabled), and standard `rafa` received Access is denied when attempting to reset HR's password.
- Did not claim an Admins-OU password-reset boundary test: ADUC displayed Reset Password on `rafa-admin`, but no reset was submitted.
- Configured/observed Default Domain Policy lockout settings for validation: 5 invalid attempts, 10-minute duration, 10-minute counter reset; Allow Administrator account lockout showed Enabled.
- Refreshed README, client/domain-user journal, FS01 cross-reference, roadmap, and this change log. No passwords, secrets, or raw screenshots were committed.

## 2026-09-29 — FS01, departmental permissions, and automated drives

- Recorded FS01 build, domain sign-in, guided 10.50.10.20/24 networking, Servers OU batch, and 80 GB OS + 70 GB data disk split.
- Created domain-local Modify groups and nested GG-Finance/GG-HR; applied departmental NTFS and share permissions.
- Screenshot established actual Finances -> E:\Shared\Finances and Shared -> E:\Shared mappings. Corrected missing direct HR share; stopped sharing parent Shared per completion report.
- Operator confirmed departmental file operations and reciprocal access denial passed.
- Created N3M0-Department-Drives linked to Users with user-group targeting. Both S: mappings recreated after removing prior manual mappings and signing out/in, per operator.
- FS01 updates complete; ISO ejection and activation unconfirmed at that milestone. No new backups or restore tests claimed.
- Added FS01 journal; refreshed README, inventory, architecture, roadmap, and client/network cross-references. Next: separate administration accounts and scoped delegation.

## 2026-09-17 — Client media cleanup confirmed

- Operator confirmed installation ISOs ejected from all three Windows clients.
- Activation was still listed as pending at that milestone; the 2026-09-29 decision above supersedes it for the disposable lab.
- Next session starts with N3M0-FS01 and Finance/HR file permissions; no file server has been created.

## 2026-09-17 — Windows clients, domain identities, and workstation GPO

- Deployed Windows 11 Pro clients N3M0-CL01 (IT), N3M0-CL02 (Finance), and N3M0-CL03 (HR) on Lab-Private-Switch using the guided 2-vCPU / 4-GB / 80-GB configuration.
- Resolved the DVD boot-prompt problem using a physical keyboard/mouse; operator identified ScreenConnect input as the cause. Secure Boot and alternate-ISO tests had not resolved it.
- Reviewed DC01 DHCP leases: CL01 10.50.10.100, CL02 .101, CL03 .102; these are dynamic leases, not reservations.
- Joined all three clients to n3m0.test and moved their computer objects into N3M0-Lab/Workstations.
- Initial documentation used stale/currently incorrect user names in places; the 2026-09-29 administration milestone above records the corrected current identities.
- Validated domain sign-ins and initial password changes per operator; kept local setup accounts separate.
- Created N3M0-Workstations-LogonNotice and verified its sign-in notice on all three clients per operator.
- Client updates completed per operator. Windows 11 Pro activation was still listed as pending at that milestone; the 2026-09-29 project decision above supersedes that item.
- Added client build journal and refreshed README, inventory, architecture, DHCP journal, and roadmap. Next: N3M0-FS01 and departmental permissions; separate administration identities remain pending.

## 2026-09-11 — pfSense, DNS forwarding, and Windows DHCP

- Deployed pfSense CE N3M0-FW01 with NIC2 / Lab-WAN-Switch uplink and 10.50.10.1/24 LAN on Lab-Private-Switch.
- Configured the DNS exception above the logged RFC1918 destination block; disabled the default IPv6 LAN allow rule.
- Set DC01 gateway and DNS forwarder to 10.50.10.1; retained DC01 itself as preferred DNS at 10.50.10.10.
- Verified external DNS, outbound TCP 443, AD SRV discovery, and logged ICMP denial to the upstream router.
- Installed/authorized Windows DHCP and configured N3M0-Clients, 10.50.10.100–199/24, with gateway/DNS/domain options (operator reported completion).
- Updated current diagram, inventory, journals, and roadmap. No Windows client has been created; client DHCP and domain join are next and remain untested.
- Clarified the disposable test-server rebuild policy: host backups are not a prerequisite. Existing DC01 backup predates these changes.

## 2026-09-10 — DC01 CPU allocation corrected

- Operator confirmed N3M0-DC01 is now configured with 2 vCPUs, matching the original proposal.
- Updated current configuration in the README, architecture, inventory, and build journal; marked the CPU review item complete in the roadmap.
- Preserved the operator's existing architecture-table edit to 2 vCPUs.
- Earlier 20-vCPU observations remain historical; no current CPU discrepancy is open.

## 2026-09-10 — DC01 deployment, verification, and backup

- Updated current architecture to keep the lab on the Hyper-V test server.
- Recorded Lab-Private-Switch, 10.50.10.0/24, and reserved gateway/server/client addresses.
- Deployed N3M0-DC01 with Windows Server 2025, AD DS, DNS, GC, n3m0.test/N3M0, and 2025 functional levels.
- Verified DC object, host A record, LDAP/Kerberos SRV records, NETLOGON, and SYSVOL through screenshots.
- Recorded VM boot and duplicate-VHDX troubleshooting and host-versus-guest context lesson.
- Recorded 4 GB RAM, 60 GB OS disk, and initially observed 20 vCPUs; subsequently corrected to 2 vCPUs as recorded above.
- Added a 100 GB GPT/NTFS backup disk; Windows Server Backup completed 15.32 GB to E:, including System State and bare-metal recovery.
- Replaced obsolete no-backup statements with current DC01 coverage and explicit limits: same host storage, no restore test, no recurring schedule or off-host copy.
- Added GUI-first guidance and a detailed DC01 build journal. Raw screenshots and secrets were not uploaded.
- Firewall selection, controlled uplink, guest activation/updates, and client validation remain open. No firewall product selected by the operator.

## 2026-09-08 — Initial planning baseline

- Established the private Security-Engineering-Lab repository.
- Recorded the Dell R630 Hyper-V inventory from read-only PowerShell results.
- Chose to host Kali, Windows clients, and lab infrastructure on the test server.
- Added a proposed nine-VM plan totaling 22 vCPUs, 38 GiB RAM, and 862 GiB virtual disk capacity.
- Corrected the earlier chat's vCPU total of 24 to 22.
- Added a proposed isolated network diagram; uplink and address space remain undecided.
- Added the penetration-testing-to-AI-security roadmap and evidence workflow.
- Deferred physical disk/RAID details and iDRAC discovery.
- No host configuration, existing VM, or networking changes made.

Next at that milestone: verify recovery arrangements, then finalize isolated networking.

## 2026-09-08 — Operator VM cleanup and backup check

- Operator checked Ninja and reported no backup configuration/history.
- Operator deleted six prior VMs; screenshot confirms only ninjatest (Off), VulScan (Off), and Wazuh (Saved) remain.
- Preserve the remaining guests; future deletion is only a possibility, not currently authorized.
- Virtual disk removal and reclaimed storage have not been verified.
- Next recovery task: identify separate storage for backups/exports of retained guests before changes affecting them.
