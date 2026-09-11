# Security Engineering Lab

A hands-on progression from Active Directory administration and penetration testing to AI security engineering.

**Owner:** N3M0SYS  
**Platform:** Authorized company test server running Hyper-V  
**Status (2026-09-11):** pfSense N3M0-FW01 deployed; DC01 DNS, internet connectivity, and an upstream block test passed. Windows DHCP scope N3M0-Clients configured. No Windows client VM has been created; client deployment and lease validation are next.

## Start here

- [Roadmap and phase checklists](docs/roadmap.md)
- [Host and VM inventory](docs/inventory.md)
- [Current and planned architecture](docs/architecture.md)
- [DC01 build journal](docs/dc01-build.md)
- [Firewall and DHCP build journal](docs/firewall-dhcp-build.md)
- [Recovery and backup status](docs/recovery.md)
- [Documentation and evidence workflow](docs/documentation.md)
- [Change log](CHANGELOG.md)
- [Lab exercise template](templates/lab-exercise.md)
- [Evidence folders](evidence/README.md)

## Learning outcomes

Build and administer a small Windows domain, assess it using authorized lab targets, detect activity using Wazuh, remediate findings, and demonstrate improvement. Extend those skills into AI application security, including prompt injection, data exposure, agent permissions, and MCP tool access.

## Current state

- All current project workloads remain on the Hyper-V server.
- Private switch: `Lab-Private-Switch`; subnet: `10.50.10.0/24`.
- DC: `N3M0-DC01`, `10.50.10.10`; forest/domain: `n3m0.test`; CPU: 2 vCPUs (operator confirmed).
- AD DS, DNS, Global Catalog, DNS A/SRV records, and NETLOGON/SYSVOL presence verified through GUI screenshots.
- Windows Server Backup completed to `DC01-Backup (E:)`, transferring 15.32 GB. Restore testing and off-host protection remain pending.
- pfSense N3M0-FW01: WAN via NIC2 / Lab-WAN-Switch; LAN 10.50.10.1/24 on Lab-Private-Switch.
- DC01 gateway 10.50.10.1; preferred DNS 10.50.10.10; DNS forwarder 10.50.10.1.
- Windows DHCP on DC01: N3M0-Clients, 10.50.10.100–10.50.10.199; router 10.50.10.1; DNS 10.50.10.10; suffix n3m0.test.
- External DNS and TCP 443 tested successfully; pfSense logs confirmed the upstream router ICMP test was blocked. Client DHCP and domain join are not yet tested.
- Existing ninjatest, VulScan, and Wazuh guests are retained.

## Current priorities

1. Create the first Windows client on Lab-Private-Switch and verify a lease from N3M0-Clients.
2. Join the client to n3m0.test and validate authentication, DNS, and Group Policy.
3. Verify guest activation/updates, then expand with DC02, file services, and monitoring.
4. Later evaluate an attacker VM on a separate simulated external segment; public exposure is not required.

## Working agreement

Explain the procedure first and provide GUI steps in useful batches; request screenshots for errors or meaningful validation rather than every wizard page. Prefer the GUI; use PowerShell when it provides a clear benefit. Reuse confirmed information rather than repeating baseline checks without a reason. Document current state, recovery limitations, and relevant rollback. This is a disposable test server: the operator accepts rebuilding it, and host backups are not a prerequisite. Updates are based on session evidence, not unattended monitoring.

The prior wipe-and-rebuild decision remains relevant to loss of the host. A local DC01 guest backup now exists, superseding the previous blanket statement that no backups are configured. GitHub stores documentation, not VM backups.
