# Recovery and backup status

## Current state — 2026-09-11

The operator explicitly confirms this is a disposable test server and accepts a full rebuild. Host backups are not a prerequisite, and the operator did not request a backup-before-changes policy.

A first Windows Server Backup of N3M0-DC01 completed successfully. This supersedes the previous blanket statement that no local backups are configured. It does not establish host backup coverage or backups of retained shared guests.

| Item | Verified value |
|---|---|
| Backup source | N3M0-DC01 |
| Tool / job | Windows Server Backup; Backup Once; Full server |
| Mode | VSS Copy Backup |
| Destination | DC01-Backup (E:) |
| Destination storage | Separate 100 GB dynamically expanding VHDX on the same host storage |
| Included items | C:, EFI/system and recovery partitions, System State, bare-metal recovery |
| Result | Completed; all listed items completed |
| Data transferred | 15.32 GB |
| Recurring schedule | Not configured |
| Restore test | Not performed |
| Off-host copy | Not configured |

The destination disk was initialized GPT and formatted NTFS with a quick format. It was excluded as a backup source.

## Limits and fallback

This backup may help recover guest configuration/OS problems while the backup remains intact. It cannot protect against loss of the physical host storage, and an attached writable backup can also be affected by guest compromise. Backup success is not proof of restore success.

The operator previously reported approval for a disposable lab with wipe-and-rebuild recovery. Rebuild remains the fallback for host/storage loss. Existing ninjatest, VulScan, and Wazuh are preserved; their backup coverage is unverified. No deletion or restore is currently authorized by this record.

## Recovery planning

1. Retain the installation ISO for recovery; it can remain ejected during normal use.
2. Keep the DSRM recovery password in the operator's private password manager, never GitHub.
3. Plan a restore test in an isolated environment without connecting a duplicate DC to the live lab.
4. Verify AD, DNS, shares, and client behavior after an actual restore.
5. Scheduled backups, retention, and off-host copies are not part of the current task.
6. Refresh the recovery baseline after meaningful configuration changes.

No restore procedure has yet been exercised. Record measured results before marking recovery tested.

## Change handling

Record the starting state, intended outcome, rollback, and result. For new firewall work, preserve console access and document adapter/switch assignments before adding an uplink. Reverting uplink configuration must not disrupt unrelated guests or host management.

Controlled connectivity is now tested; guest activation and updates remain unverified.

## Firewall rollback and backup freshness

Use Hyper-V console access to correct guest networking. If the firewall uplink needs isolation, disconnect N3M0-FW01's WAN virtual adapter; preserve NIC1 host management and Lab-Private-Switch. These rollback steps are documented, not exercised.

The existing DC01 backup predates the gateway, DNS forwarder, and DHCP changes. No new backup or pfSense configuration export was reported. Neither is a gate for creating the first client.
