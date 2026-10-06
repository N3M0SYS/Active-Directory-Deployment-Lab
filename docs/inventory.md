# Host and VM inventory

**Current update: 2026-10-05.** Historical host measurements below are retained; the current deployment section supersedes earlier switch, guest, and backup status.

Observed from operator-provided PowerShell screenshots on 2026-09-08. Values are snapshots, not live monitoring. Commands labeled GB used PowerShell binary units (GiB).

## Hyper-V host — verified

| Item | Observed value |
|---|---|
| Manufacturer / model | Dell PowerEdge R630 |
| Processors | 2 × Intel Xeon E5-2630 v4 @ 2.20 GHz |
| Total CPU | 20 physical cores / 40 logical processors |
| Installed / usable RAM | Approximately 64 GB / 63.9 GiB |
| Free RAM at capture | 55 GiB |
| OS | Windows Server 2025 Standard Evaluation |
| C: filesystem | NTFS |
| C: capacity / free | 3,723 GiB / 2,963 GiB |
| Storage presented to Windows | DELL PERC H730 Mini; RAID bus; 3,724 GiB; Healthy |
| Physical media / RAID level | Unknown; controller output does not establish these |
| Network adapters | Four Intel Gigabit 4P I350-t rNDC ports |
| NIC1 | Up, 1 Gbps |
| NIC2 / NIC3 / NIC4 | Disconnected |
| Hyper-V switches | Get-VMSwitch returned no entries |
| Access | Operator uses ScreenConnect and has physical access |
| iDRAC | Address/access unconfirmed; discovery deferred |
| RACADM | Not on command path or found in searched Dell Program Files folders |
| Backups | Operator checked Ninja: no backup configured/history reported; other methods unverified |

## Initial VMs — historical baseline before operator cleanup

All have 20 configured vCPUs, dynamic memory disabled, and 0 assigned RAM at capture.

| Name | State | Startup RAM (GiB) |
|---|---|---:|
| Buntu-Server | Off | 8 |
| ninjatest | Off | 4 |
| Pen-Test Machine | Off | 16 |
| Test_1 | Off | 16 |
| Test_2 | Off | 6 |
| Test_Intune | Saved | 4.9 |
| Testtt | Off | 8 |
| VulScan | Off | 8 |
| Wazuh | Saved | 8 |

The displayed startup allocations total approximately 78.9 GiB, exceeding host RAM if all were started together. Saved/off VMs are not currently consuming assigned guest RAM. Review shared usage before starting guests. Existing Wazuh suitability remains unknown.

## Outstanding information at the initial baseline

- Backup destination, recovery access, and restore evidence.
- Approved resource reserve for coworkers' test tools.
- Lab address range, uplink design, and isolation rules.
- Drive media, RAID level, and individual drive health if performance or recovery planning requires them.

## VMs after operator cleanup — historical 2026-09-08 baseline

The operator reports deleting the other six VMs. Hyper-V Manager now shows only:

| Name | Current state |
|---|---|
| ninjatest | Off |
| VulScan | Off |
| Wazuh | Saved |

Preserve these three guests. Possible future deletion was discussed but is not an instruction to delete them now. Original allocations were 4, 8, and 8 GiB respectively; not re-queried after cleanup. Removal of VM entries does not establish deletion of their virtual disks or reclaimed space. Current disk free space has not been re-measured. No new lab guests deployed.

## Current deployment — 2026-09-17

- Later host C: screenshot showed 3,723 GiB total and 2,940.3 GiB free before DC01 installation/backup. Current free space after the backup has not been measured.
- Switches: Lab-Private-Switch (Private) and Lab-WAN-Switch (External, NIC2, management OS sharing disabled).
- NIC1 remains host management; NIC2 was cabled to the existing physical switch and confirmed Up at 1 Gbps. NIC2 maps to Intel(R) Gigabit 4P I350-t rNDC #4.
- N3M0-FW01 deployed: pfSense CE, Generation 2, 2 vCPUs, 4096 MB fixed RAM, 32 GB VHDX, Secure Boot disabled per guided build. WAN hn0 via Lab-WAN-Switch; LAN hn1 at 10.50.10.1/24 via Lab-Private-Switch. Exact installed CE release not captured.
- N3M0-DC01 deployed and running; the three retained guests have not been removed.
- DC01: Generation 2; 4096 MB RAM; 2 virtual processors (operator confirmed the correction on 2026-09-10).
- Disks: 60 GB OS VHDX and 100 GB dynamic backup VHDX on host storage.
- IPv4 10.50.10.10/24; preferred DNS 10.50.10.10; gateway 10.50.10.1 and alternate DNS blank; IPv6 enabled.
- AD domain/forest n3m0.test, NetBIOS N3M0, Windows Server 2025 functional levels; DNS and GC enabled.
- Windows Server Backup completed to guest E: (DC01-Backup), 15.32 GB. This is DC01-only coverage, not host or retained-guest coverage.

- DC01 DNS forwards to 10.50.10.1. DHCP role installed and post-install authorization completed per operator.
- DHCP scope N3M0-Clients configured and activated per operator: 10.50.10.100–199/24, eight-day leases, gateway 10.50.10.1, DNS 10.50.10.10, suffix n3m0.test.
- Three Windows 11 Pro clients deployed on Lab-Private-Switch; DHCP leases observed and domain joins/sign-ins confirmed by operator.

| Client | Role | Observed DHCP IP | Local account | Tested domain account |
|---|---|---|---|---|
| N3M0-CL01 | IT workstation | 10.50.10.100 | IT Admin | rafa@n3m0.test |
| N3M0-CL02 | Finance workstation | 10.50.10.101 | Finance Team | finance@n3m0.test |
| N3M0-CL03 | HR workstation | 10.50.10.102 | HR Team | hr@n3m0.test |

Client build settings: Generation 2, 2 vCPUs, 4096 MB fixed RAM, 80 GB dynamically expanding OS VHDX, Secure Boot using Microsoft Windows template, and vTPM. These are guided settings, not a fresh configuration export of all three VMs. CL01 screenshots showed 2 processors, 4096 MB, and enabled Secure Boot/vTPM; Secure Boot was temporarily disabled for diagnosis and re-enabling was instructed before installation. Final security state was not separately recaptured.

All client computer objects are in N3M0-Lab/Workstations. Domain users remain standard users; departmental groups do not imply administrative privileges or workstation logon restrictions. Updates and GPO notices passed per operator on all clients. Activation pending; installation ISOs ejected from all three clients per operator. DHCP addresses can change.

See [DC01 build journal](dc01-build.md), [firewall/DHCP journal](firewall-dhcp-build.md), [address plan](architecture.md), and [backup limits](recovery.md). External DNS, TCP 443, and the upstream router block were verified; client authentication and logon-notice policy tests now pass per operator. Activation, infrastructure update verification, restore testing, and broader security validation remain pending. See [client journal](windows-clients-build.md).

## FS01 deployment — 2026-09-29

N3M0-FS01 is deployed and joined to n3m0.test; domain administrator sign-in succeeded per operator. Guided settings: Windows Server 2025, Generation 2, 2 vCPUs, 4096 MB fixed RAM, 80 GB OS VHDX plus 70 GB dynamically expanding data VHDX, Secure Boot Microsoft Windows template, one adapter on Lab-Private-Switch. Exact edition/build and VM settings were not independently exported.

Guided network configuration completed per operator: 10.50.10.20/24, gateway 10.50.10.1, DNS 10.50.10.10. Servers OU creation and FS01 move were included in the completed build batch. DepartmentData (E:) reported Healthy; screenshot separately showed the 70 GB volume.

Actual paths: E:\Shared\Finances and E:\Shared\HR; direct shares Finances and HR. Shared parent share removed per batch completion report. Department access/denial and automatic S: recreation passed on CL02 and CL03 per operator. FS01 Windows updates complete and ISO ejection confirmed; activation intentionally out of scope. See [FS01 journal](fs01-build.md).

## DC02 deployment and client DNS — 2026-10-05

N3M0-DC02 deployed: Generation 2, 2 vCPUs, 4096 MB, Lab-Private-Switch observed. Guided 80 GB OS VHDX named N3M0-DC02.vhdx; capacity/security/fixed-memory final export not captured. Windows Server 2025 Standard Evaluation Desktop Experience installation completed per operator. Static 10.50.10.11/24, gateway 10.50.10.1 captured. Writable AD DS/DNS/GC in n3m0.test, Domain Controllers OU, Default-First-Site-Name. All FSMO roles stay on DC01.

Both-way replication and basic DNS/Advertising/SYSVOL/NETLOGON checks passed; SYSVOL DFSR State 4 on both. DC01 final DNS client list is only 10.50.10.10. DC02 observed DNS list contains DC01 and local loopback after promotion. DC01 uses time.windows.com,0x8; DC02 synchronizes from DC01. Guest VM time provider disabled on both.

DHCP remains on DC01. Scope option 006 now lists 10.50.10.10 then 10.50.10.11. CL01 renewal/ipconfig captured 10.50.10.100/24, gateway 10.50.10.1, n3m0.test suffix, DHCP server 10.50.10.10, both DNS servers. Fresh standard rafa Kerberos through DC02 and user policy update passed. CL02/CL03 renewals were not recaptured. DC02 updates and ISO ejection confirmed 2026-10-06; external DNS resolution and forwarder 10.50.10.1 with root-hints fallback verified. No new backup. See [DC02 journal](dc02-build.md).
