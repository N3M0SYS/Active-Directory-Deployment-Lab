# Host and VM inventory

**Current update: 2026-09-10.** Historical host measurements below are retained; the current deployment section supersedes earlier switch, guest, and backup status.

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

## Current deployment — 2026-09-10

- Later host C: screenshot showed 3,723 GiB total and 2,940.3 GiB free before DC01 installation/backup. Current free space after the backup has not been measured.
- Switch: Lab-Private-Switch, Private.
- N3M0-DC01 deployed and running; the three retained guests have not been removed.
- DC01: Generation 2; 4096 MB RAM; 20 virtual processors shown in settings (2 originally proposed; adjustment not recorded).
- Disks: 60 GB OS VHDX and 100 GB dynamic backup VHDX on host storage.
- IPv4 10.50.10.10/24; preferred DNS 10.50.10.10; gateway and alternate DNS blank; IPv6 enabled.
- AD domain/forest n3m0.test, NetBIOS N3M0, Windows Server 2025 functional levels; DNS and GC enabled.
- Windows Server Backup completed to guest E: (DC01-Backup), 15.32 GB. This is DC01-only coverage, not host or retained-guest coverage.

See [DC01 build journal](dc01-build.md), [address plan](architecture.md), and [backup limits](recovery.md). Firewall, DHCP, guest internet access, activation, restore test, and client validation remain pending.
