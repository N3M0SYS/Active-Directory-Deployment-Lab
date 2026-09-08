# Recovery and deployment gates

Status: **not yet completed**. No infrastructure changes were made during inventory.

Before changing networking or creating project infrastructure:

1. Resolve the host licensing question.
2. Record affected adapters, IP configuration, routes, DNS, switches, VM attachments, and access method in an appropriate private location.
3. Identify what existing guests/data could be affected and coordinate shared use.
4. Verify the backup destination and recovery procedure; record a restore check where available.
5. Define the exact change, success criteria, rollback steps, and stop conditions.
6. Confirm physical console access and required administrative credentials are available to the operator.
7. Apply one change, validate access/isolation, then update the change log.

Do not treat Hyper-V checkpoints as independent backups. GitHub documentation does not back up VMs. Do not place VM disks, backup archives, credentials, or host recovery secrets in this repository.

## Open record

| Item | Status |
|---|---|
| Backup location and retention | No backup configured/history reported in Ninja by operator; other methods unverified |
| Latest successful backup | Unknown |
| Restore validation | Not performed |
| Local recovery access | Physical proximity confirmed; usable console/credentials not yet verified |
| Network rollback procedure | Pending final design |
| License resolution | Pending; notification mode observed |

## Operator cleanup and preservation scope — 2026-09-08

Operator removed six VM entries and retained ninjatest (Off), VulScan (Off), and Wazuh (Saved), confirmed by Hyper-V Manager screenshot. Preserve the three remaining VMs. Their backup/export destination and recoverability remain unresolved. Identify separate storage for recovery copies before changes that could affect them. Cleanup did not establish removal of unused virtual disk files. No further deletion is requested.
