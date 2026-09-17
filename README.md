# Security Engineering Lab

A hands-on progression from Active Directory administration and penetration testing to AI security engineering.

**Owner:** N3M0SYS  
**Platform:** Authorized company test server running Hyper-V  
**Status (2026-09-17):** Three Windows 11 Pro clients deployed and joined to n3m0.test. DHCP leases, domain user sign-ins/password changes, and the workstation logon-notice GPO validated. Client updates completed per operator; activation pending. Next: N3M0-FS01 and departmental file permissions.

## Start here

- [Roadmap and phase checklists](docs/roadmap.md)
- [Host and VM inventory](docs/inventory.md)
- [Current and planned architecture](docs/architecture.md)
- [DC01 build journal](docs/dc01-build.md)
- [Firewall and DHCP build journal](docs/firewall-dhcp-build.md)
- [Windows clients, domain users, and GPO journal](docs/windows-clients-build.md)
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
- Existing ninjatest, VulScan, and Wazuh guests are retained.

## Current priorities

1. Deploy N3M0-FS01 and validate Finance/HR share and NTFS permissions.
2. Create separate administration accounts and define delegated permissions.
3. Track client activation; later expand with DC02 and monitoring.
4. Later evaluate an attacker VM on a separate simulated external segment; public exposure is not required.

## Working agreement

Explain the procedure first and provide GUI steps in useful batches; request screenshots for errors or meaningful validation rather than every wizard page. Prefer the GUI; use PowerShell when it provides a clear benefit. Reuse confirmed information rather than repeating baseline checks without a reason. Document current state, recovery limitations, and relevant rollback. This is a disposable test server: the operator accepts rebuilding it, and host backups are not a prerequisite. Updates are based on session evidence, not unattended monitoring.

The prior wipe-and-rebuild decision remains relevant to loss of the host. A local DC01 guest backup now exists, superseding the previous blanket statement that no backups are configured. GitHub stores documentation, not VM backups.
