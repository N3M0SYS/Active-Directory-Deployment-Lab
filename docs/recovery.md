# Recovery approach

## Current decision — 2026-09-08

The operator reports that the Security Engineer approved using this disposable test server for the project. Local/cloud backup storage cannot be configured for it. If the environment breaks, the agreed recovery approach is to wipe and rebuild. **Backups are intentionally not configured; backup setup is not a deployment gate.**

This supersedes the earlier requirement to obtain backups of retained guests. The decision accepts loss of local VM data, configuration, and lab progress. GitHub preserves documented procedures, not VM data. The remaining ninjatest, VulScan, and Wazuh guests will stay in place unless removal is needed and directed. No wipe or deletion is being performed now.

## Before each change

1. Record the relevant starting state and intended outcome.
2. Prefer a small reversible change and document its rollback.
3. Apply one step and inspect its result.
4. Record verified outcomes in GitHub so a rebuild is repeatable.

## First network change — planned, not executed

- Baseline: previous Get-VMSwitch returned no switches; NIC1 was the active 1 Gbps adapter.
- Proposed action: create a private switch named LAB-PRIVATE.
- Scope: new isolated switch only; no physical adapter binding or existing VM attachment changes.
- Validation: output must show LAB-PRIVATE with SwitchType Private.
- Rollback: remove the newly created switch through Hyper-V Virtual Switch Manager while it has no attached guests.
- Later uplink changes require their own network baseline and isolation validation.

## Other open items

Host evaluation activation failed with a timeout and remains the server administrator's responsibility. This is tracked as a reliability issue, not a reason to continue activation changes without the owner. Recovery installation media and access should be documented as the build proceeds.
