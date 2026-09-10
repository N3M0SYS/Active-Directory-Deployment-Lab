# Change log

## 2026-09-10 — DC01 deployment, verification, and backup

- Updated current architecture to keep the lab on the Hyper-V test server.
- Recorded Lab-Private-Switch, 10.50.10.0/24, and reserved gateway/server/client addresses.
- Deployed N3M0-DC01 with Windows Server 2025, AD DS, DNS, GC, n3m0.test/N3M0, and 2025 functional levels.
- Verified DC object, host A record, LDAP/Kerberos SRV records, NETLOGON, and SYSVOL through screenshots.
- Recorded VM boot and duplicate-VHDX troubleshooting and host-versus-guest context lesson.
- Recorded 4 GB RAM, 60 GB OS disk, and observed 20 vCPUs; original 2-vCPU proposal remains a review item.
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
