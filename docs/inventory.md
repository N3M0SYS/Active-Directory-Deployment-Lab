# Host inventory

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
| License query | Windows is in Notification mode; no expiration date returned |
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

## Proxmox comparison — historical, not re-verified

| Item | Prior reported baseline |
|---|---|
| CPU | AMD Ryzen AI 9 HX 370; 12 cores / 24 threads |
| RAM | 32 GB |
| Storage | 1 TB SSD |
| Networking | USB-C Ethernet adapter |
| Current free resources and guests | Not re-verified |

The Hyper-V host offers more installed RAM, storage capacity, and cores. No CPU or storage benchmarks were performed; greater core count does not prove greater workload performance.

## Outstanding information

- License details and valid activation/evaluation state.
- Backup destination, recovery access, and restore evidence.
- Approved resource reserve for coworkers' test tools.
- Lab address range, uplink design, and isolation rules.
- Drive media, RAID level, and individual drive health if performance or recovery planning requires them.

## License detail follow-up — 2026-09-08

The operator's `slmgr.vbs /dlv` output confirmed ServerStandardEval, TIMEBASED_EVAL channel, license status Notification, and notification reason `0xC004FC07`. Remaining Windows rearm count and SKU rearm count both show 1. This does not prove an available extension duration or successful activation. Activation identifiers and partial product key are intentionally omitted. No rearm, conversion, or reinstall performed. Subsequent `/ato` attempt failed with `0x80072EE2` (timeout). Further licensing changes are deferred to the server administrator.

## Current VMs after operator cleanup — 2026-09-08

The operator reports deleting the other six VMs. Hyper-V Manager now shows only:

| Name | Current state |
|---|---|
| ninjatest | Off |
| VulScan | Off |
| Wazuh | Saved |

Preserve these three guests. Possible future deletion was discussed but is not an instruction to delete them now. Original allocations were 4, 8, and 8 GiB respectively; not re-queried after cleanup. Removal of VM entries does not establish deletion of their virtual disks or reclaimed space. Current disk free space has not been re-measured. No new lab guests deployed.
