# Change log

## 2026-09-08 — Initial planning baseline

- Established the private Security-Engineering-Lab repository.
- Recorded the Dell R630 Hyper-V inventory from read-only PowerShell results.
- Chose to host Kali, Windows clients, and lab infrastructure on the test server.
- Added a proposed nine-VM plan totaling 22 vCPUs, 38 GiB RAM, and 862 GiB virtual disk capacity.
- Corrected the earlier chat's vCPU total of 24 to 22.
- Added a proposed isolated network diagram; uplink and address space remain undecided.
- Added the penetration-testing-to-AI-security roadmap and evidence workflow.
- Recorded Windows notification mode as unresolved.
- Deferred physical disk/RAID details and iDRAC discovery.
- No host configuration, existing VM, or networking changes made.

Next: investigate licensing, verify recovery arrangements, then finalize isolated networking.

## 2026-09-08 — License details reviewed

- Confirmed TIMEBASED_EVAL channel and Notification status with reason `0xC004FC07` from `/dlv`.
- Recorded remaining Windows/SKU rearm counts of 1 without assuming an extension is available.
- Kept activation identifiers and the screenshot out of GitHub.
- Normal activation attempt proposed; outcome pending. No host changes performed in this step.

## 2026-09-08 — Activation attempt and ownership boundary

- Operator ran `/ato`; it failed with `0x80072EE2` (operation timed out). Activation success has not been established.
- Operator clarified the host belongs to the company. Further licensing changes are deferred to the server owner/administrator.
- Planning and read-only inventory may continue. Host evaluation status remains an unresolved reliability dependency before sustained lab use.
- No rearm, key replacement, edition conversion, or reinstall is planned by this project at this point.
