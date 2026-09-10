# Security Engineering Lab

A hands-on progression from Active Directory administration and penetration testing to AI security engineering.

**Owner:** N3M0SYS  
**Platform:** Authorized company test server running Hyper-V  
**Status (2026-09-10):** N3M0-DC01 deployed; initial AD/DNS checks and first guest backup complete. Firewall selection and controlled internet access are next.

## Start here

- [Roadmap and phase checklists](docs/roadmap.md)
- [Host and VM inventory](docs/inventory.md)
- [Current and planned architecture](docs/architecture.md)
- [DC01 build journal](docs/dc01-build.md)
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
- DC: `N3M0-DC01`, `10.50.10.10`; forest/domain: `n3m0.test`.
- AD DS, DNS, Global Catalog, DNS A/SRV records, and NETLOGON/SYSVOL presence verified through GUI screenshots.
- Windows Server Backup completed to `DC01-Backup (E:)`, transferring 15.32 GB. Restore testing and off-host protection remain pending.
- No lab firewall, gateway, DHCP scope, or controlled internet path has been deployed.
- Existing ninjatest, VulScan, and Wazuh guests are retained.

## Current priorities

1. Select the firewall platform and design an approved uplink with explicit isolation rules.
2. Provide controlled activation/update access; guest activation and patching are not yet verified.
3. Review DC01's observed 20-vCPU allocation against the proposed 2 vCPUs.
4. Test recovery, then expand with clients, DC02, file services, and monitoring.
5. Later evaluate an attacker VM on a separate simulated external segment; public exposure is not required.

## Working agreement

Explain the procedure first, then guide one step at a time and review results. Prefer the GUI; use PowerShell when it provides a clear benefit. Reuse confirmed information rather than repeating baseline checks without a reason. Document current state, recovery limitations, and rollback before changes. Updates are based on session evidence, not unattended monitoring.

The prior wipe-and-rebuild decision remains relevant to loss of the host. A local DC01 guest backup now exists, superseding the previous blanket statement that no backups are configured. GitHub stores documentation, not VM backups.
