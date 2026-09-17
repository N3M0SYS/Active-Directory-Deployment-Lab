# Change log

## 2026-09-17 — Windows clients, domain identities, and workstation GPO

- Deployed Windows 11 Pro clients N3M0-CL01 (IT), N3M0-CL02 (Finance), and N3M0-CL03 (HR) on Lab-Private-Switch using the guided 2-vCPU / 4-GB / 80-GB configuration.
- Resolved the DVD boot-prompt problem using a physical keyboard/mouse; operator identified ScreenConnect input as the cause. Secure Boot and alternate-ISO tests had not resolved it.
- Reviewed DC01 DHCP leases: CL01 10.50.10.100, CL02 .101, CL03 .102; these are dynamic leases, not reservations.
- Joined all three clients to n3m0.test and moved their computer objects into N3M0-Lab/Workstations.
- Created Users, Workstations, and Groups OUs, standard users rafa/finance.user/hr.user, and global security groups GG-Finance/GG-HR through the guided workflow.
- Validated domain sign-ins and initial password changes per operator; kept local setup accounts separate.
- Created N3M0-Workstations-LogonNotice and verified its sign-in notice on all three clients per operator.
- Client updates completed per operator. Windows 11 Pro activation remains pending; ISO ejection was announced but not confirmed.
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
