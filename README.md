# Security Engineering Lab

A hands-on progression from Active Directory administration and penetration testing to AI security engineering.

**Owner:** N3M0SYS  
**Platform:** Authorized company test server running Hyper-V  
**Status (2026-09-29):** N3M0-FS01 is deployed and departmental access is verified. Separate administration identities and scoped least-privilege delegation are also verified. Windows activation is intentionally not being pursued for this disposable lab. FS01 installation media is now ejected. Next major infrastructure milestone: DC02 and replication.

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
- DC: `N3M0-DC01`, `10.50.10.10`; forest/domain: `n3m0.test`; vCPU: `2`; Memory: `4096 MB`; Generation: `2`.
- AD DS, DNS, Global Catalog, DNS A/SRV records, and NETLOGON/SYSVOL presence verified through GUI screenshots.
- Windows Server Backup completed to `DC01-Backup (E:)`, transferring 15.32 GB. Restore testing and off-host protection remain pending.
- pfSense N3M0-FW01: WAN via NIC2 / Lab-WAN-Switch; LAN 10.50.10.1/24 on Lab-Private-Switch.
- N3M0-DC01 gateway 10.50.10.1; preferred DNS 10.50.10.10; DNS forwarder 10.50.10.1.
- Windows DHCP on DC01: N3M0-Clients, 10.50.10.100–10.50.10.199; router 10.50.10.1; DNS 10.50.10.10; suffix n3m0.test.
- External DNS and TCP 443 tested successfully; pfSense logs confirmed the upstream router ICMP test was blocked. Client leases are observed and all three domain joins/sign-ins succeeded per operator.
- N3M0-CL01 (IT), CL02 (Finance), and CL03 (HR) use Lab-Private-Switch; observed DHCP addresses are 10.50.10.100, .101, and .102 respectively (not reservations).
- N3M0-Lab contains Admins, Users, Workstations, Servers, and Groups OUs; all three client computer accounts are in Workstations.
- Current standard domain users are `finance` (Finance Team), `hr` (HR Team), `lchen` (Lucy Chen, fictional IT user), and `rafa` (standard daily-use IT account).
- `GG-Finance` and `GG-HR` represent Finance/HR membership. `GG-IT` is Global/Security and contains `lchen` and `rafa`; it grants no administrative privilege by itself.
- `rafa-admin` exists in N3M0-Lab/Admins as a separate administrative identity. It is not a Domain Admin and is not in GG-IT.
- Delegation on N3M0-Lab/Users grants `rafa-admin` password reset/force-change and custom Read/Write `lockoutTime` on User objects for account-unlock support.
- Positive validation: `rafa-admin` reset Finance's password and forced a password change; Finance authenticated and changed it successfully. HR was intentionally subjected to failed logons under the configured lockout policy; `rafa-admin` unlocked HR and HR then authenticated successfully.
- Negative validation: `rafa-admin` could not create users (New User unavailable) and could not modify GG-IT membership (Add disabled). Standard `rafa` attempted to reset HR's password and received Access is denied.
- The separate Admins-OU password-reset boundary was not tested: ADUC displayed Reset Password on `rafa-admin`, but no reset was attempted.
- CL01 is the IT management workstation and has the RSAT Active Directory DS/LDS tools installed. CL02 and CL03 remain ordinary department clients and do not require RSAT.
- Default Domain Policy account-lockout settings observed after configuration: threshold 5 invalid attempts, duration 10 minutes, reset counter after 10 minutes; Allow Administrator account lockout showed Enabled.
- N3M0-Workstations-LogonNotice is linked to Workstations; its sign-in notice appeared on all three clients per operator.
- Client updates completed per operator; installation ISOs ejected from all three clients per operator. Windows activation is intentionally not being purchased or pursued for these disposable project VMs.
- N3M0-FS01 uses 10.50.10.20/24, gateway 10.50.10.1, and DNS 10.50.10.10 following the guided configuration. Servers OU placement was part of the completed build batch.
- Finances and HR shares use domain-local Modify groups containing GG-Finance and GG-HR. The Shared parent share was removed per operator completion report.
- N3M0-Department-Drives is linked to N3M0-Lab/Users, with user-group item-level targeting for Finance/HR S: mappings. Both automatic recreation tests passed per operator.
- FS01 updates complete and installation ISO ejected per operator. Activation is intentionally not being pursued for this lab VM.
- Existing ninjatest, VulScan, and Wazuh guests are retained.

## Current priorities

1. Deploy DC02 and verify AD/DNS replication.
2. Later expand monitoring/detection with Wazuh.
3. Later evaluate an attacker VM on a separate simulated external segment; public exposure is not required.

## Working agreement

Explain the procedure first and provide GUI steps in useful batches; request screenshots for errors or meaningful validation rather than every wizard page. Prefer the GUI; use PowerShell when it provides a clear benefit. Reuse confirmed information rather than repeating baseline checks without a reason. Document current state, recovery limitations, and relevant rollback. This is a disposable test server: the operator accepts rebuilding it, and host backups are not a prerequisite. Updates are based on session evidence, not unattended monitoring.

The prior wipe-and-rebuild decision remains relevant to loss of the host. A local DC01 guest backup now exists, superseding the previous blanket statement that no backups are configured. GitHub stores documentation, not VM backups.
