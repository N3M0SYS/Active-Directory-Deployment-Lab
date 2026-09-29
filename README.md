# Security Engineering Lab

A hands-on progression from Active Directory administration and penetration testing to AI security engineering.

**Owner:** N3M0SYS  
**Platform:** Authorized company test server running Hyper-V  
**Status (2026-09-29):** N3M0-FS01 deployed and domain joined. Departmental SMB/NTFS access and cross-department denial passed; Finance and HR S: mappings recreated automatically after disconnect/sign-out tests, per operator. FS01 updates complete. Next: separate administration accounts and scoped delegation.

## Start here

- [Roadmap and phase checklists](docs/roadmap.md)
- [Host and VM inventory](docs/inventory.md)
- [Current and planned architecture](docs/architecture.md)
- [DC01 build journal](docs/dc01-build.md)
- [Firewall and DHCP build journal](docs/firewall-dhcp-build.md)
- [Windows clients, domain users, and GPO journal](docs/windows-clients-build.md)
- [FS01, departmental permissions, and drive mappings](docs/fs01-build.md)
- [Recovery and backup status](docs/recovery.md)
- [Documentation and evidence workflow](docs/documentation.md)
- [Change log](CHANGELOG.md)
- [Lab exercise template](templates/lab-exercise.md)
- [Evidence folders](evidence/README.md)

## Learning outcomes

Build and administer a small enterprise Windows domain, assess it using authorized lab Windows clients, detect activity using Wazuh, remediate findings, and demonstrate improvement. Extend those skills into AI application security, including prompt injection, data exposure, agent permissions, and MCP tool access.

## Current state

- All current project workloads remain on the Hyper-V server.
- Private switch: `Lab-Private-Switch`; subnet: `10.50.10.0/24`.
- DC: `N3M0-DC01`, `10.50.10.10`; forest/domain: `n3m0.test`; vCPU: '2'; Memory: '4096 MB'; Generation: '2'  .
- AD DS, DNS, Global Catalog, DNS A/SRV records, and NETLOGON/SYSVOL presence verified through GUI screenshots.
- Windows Server Backup completed to `DC01-Backup (E:)`, transferring 15.32 GB. Restore testing and off-host protection remain pending.
- pfSense N3M0-FW01: WAN via NIC2 / Lab-WAN-Switch; LAN 10.50.10.1/24 on Lab-Private-Switch.
- N3M0-DC01 gateway 10.50.10.1; preferred DNS 10.50.10.10; DNS forwarder 10.50.10.1.
- Windows DHCP on DC01: N3M0-Clients, 10.50.10.100–10.50.10.199; router 10.50.10.1; DNS 10.50.10.10; suffix n3m0.test.
- External DNS and TCP 443 tested successfully; pfSense logs confirmed the upstream router ICMP test was blocked. Client leases are observed and all three domain joins/sign-ins succeeded per operator.
- N3M0-CL01 (IT), CL02 (Finance), and CL03 (HR) use Lab-Private-Switch; observed DHCP addresses are 10.50.10.100, .101, and .102 respectively (not reservations).
- N3M0-Lab contains Users, Workstations, and Groups OUs; all three computer accounts were moved to Workstations.
- Standard domain users rafa, finance.user, and hr.user signed in and changed initial passwords. GG-Finance and GG-HR are global security groups configured in the guided workflow.
- N3M0-Workstations-LogonNotice is linked to Workstations; its sign-in notice appeared on all three clients per operator.
- Client updates completed per operator; Windows 11 Pro activation pending; installation ISOs ejected from all three clients per operator. Separate delegated admin accounts remain planned.
- N3M0-FS01 uses 10.50.10.20/24, gateway 10.50.10.1, and DNS 10.50.10.10 following the guided configuration. Servers OU placement was part of the completed build batch.
- Finances and HR shares use domain-local Modify groups containing GG-Finance and GG-HR. The Shared parent share was removed per operator completion report.
- N3M0-Department-Drives is linked to N3M0-Lab/Users, with user-group item-level targeting for Finance/HR S: mappings. Both automatic recreation tests passed per operator.
- FS01 updates complete per operator; FS01 ISO ejection and activation unconfirmed.
- Existing ninjatest, VulScan, and Wazuh guests are retained.

## Current priorities

1. Create separate administration accounts and define scoped delegated permissions; keep rafa a standard user.
2. Track activation and confirm FS01 ISO ejection; these are not host-backup gates.
3. Later expand with DC02 and monitoring.
4. Later evaluate an attacker VM on a separate simulated external segment; public exposure is not required.

## Working agreement

Explain the procedure first and provide GUI steps in useful batches; request screenshots for errors or meaningful validation rather than every wizard page. Prefer the GUI; use PowerShell when it provides a clear benefit. Reuse confirmed information rather than repeating baseline checks without a reason. Document current state, recovery limitations, and relevant rollback. This is a disposable test server: the operator accepts rebuilding it, and host backups are not a prerequisite. Updates are based on session evidence, not unattended monitoring.

The prior wipe-and-rebuild decision remains relevant to loss of the host. A local DC01 guest backup now exists, superseding the previous blanket statement that no backups are configured. GitHub stores documentation, not VM backups.
