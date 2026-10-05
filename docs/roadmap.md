# Roadmap

**Current milestone (2026-10-05):** DC02 deployment and replication verified: all five AD partitions both ways, automatic AD/DNS changes both ways, SYSVOL/NETLOGON, DFSR State 4, synchronized time, and fresh rafa Kerberos tickets issued by DC02. DHCP remains on DC01 and advertises both DNS servers. DC02 updates/media cleanup and external DNS forwarding are separate follow-ups.

Goal: penetration testing foundations, detection and remediation, then AI security engineering. Check an item only after its outcome is verified.

## Phase 0 — Baseline and recovery

- [x] Inventory CPU, RAM, volumes, existing guests, switches, and physical adapters.
- [x] Choose Hyper-V as the main lab platform for current servers and clients; later attacker placement remains open.
- [x] Establish a private GitHub documentation repository.
- [ ] Agree on available resources and shared VM ownership.
- [ ] Record current host network settings securely.
- [x] Document recovery approach; DC01 now has a local guest backup, with wipe-and-rebuild still the fallback for host loss.
- [ ] Confirm local recovery access and a rollback procedure.
- [x] Select lab IPv4 addressing and domain name.
- [x] Configure NIC2 uplink and initial IPv4 isolation rules; broader isolation tests remain below.

Deliverable: verified baseline, decision log, and recovery plan.

## Phase 1 — Isolated networking

- [x] Select and deploy pfSense CE as N3M0-FW01.
- [x] Create the private lab virtual switch: `Lab-Private-Switch` (properties reviewed 2026-09-09; operator reports applied).
- [x] Validate client-to-domain services through DHCP leases, joins, and domain sign-ins.
- [x] Configure Lab-WAN-Switch on NIC2 and LAN private-network block with DNS exception.
- [x] Verify DC01 external DNS, outbound TCP 443, and a logged block to the upstream router.
- [ ] Verify lab DHCP remains contained.
- [ ] Verify required access works and access to company/client networks is blocked.
- [x] Document firewall configuration, validation limits, and console-based rollback.

Deliverable: network diagram and isolation test evidence.

## Phase 2 — Windows business environment

- [x] Deploy N3M0-DC01 with AD DS and DNS in n3m0.test.
- [x] Verify DC object, DNS A/SRV records, NETLOGON, and SYSVOL.
- [x] Complete first Windows Server Backup to E: (15.32 GB).
- [ ] Optional later recovery exercise: test restoration; not a prerequisite for this disposable lab.
- [x] Complete Windows 11 client updates (operator reported).
- [x] Record Windows activation as intentionally out of scope for disposable project VMs; no license purchase planned.
- [x] Confirm client installation ISOs ejected (operator confirmed all three).
- [x] Correct DC01 CPU allocation to 2 vCPUs (operator confirmed 2026-09-10).
- [x] Deploy DC02 and verify replication; see [DC02 journal](dc02-build.md) for evidence and limits.
- [ ] Finish DC02 updates, ISO ejection, and explicit external DNS/forwarder verification.
- [x] Configure N3M0-Lab Users, Workstations, Groups, Servers, and Admins OUs with current standard IT/Finance/HR users and departmental groups.
- [x] Create separate administration identity and define scoped delegated privileges; positive and negative authorization tests passed.
- [x] Install RSAT AD tools on CL01 and validate the standard-session / run-as-different-user administration workflow.
- [x] Configure and validate delegated password reset/force-change and account unlock on N3M0-Lab/Users.
- [x] Configure domain account-lockout policy used for the controlled unlock test: 5 attempts, 10-minute duration, 10-minute counter reset.
- [x] Deploy FS01 and test departmental file operations plus cross-department share/NTFS denial (operator reported).
- [x] Configure AGDLP departmental groups and N3M0-Department-Drives; validate automatic S: recreation for both departments after disconnect/sign-out.
- [x] Complete FS01 Windows updates (operator reported).
- [x] Confirm FS01 installation ISO ejected (operator confirmed 2026-09-29).
- [x] Record FS01 activation as intentionally out of scope for this disposable project VM.
- [x] Install/authorize Windows DHCP and configure scope N3M0-Clients (operator reported).
- [x] Deploy N3M0-CL01, N3M0-CL02, and N3M0-CL03 for IT, Finance, and HR.
- [x] Verify client DHCP leases in DC01 DHCP console; operator reports leasing works properly.
- [x] Capture CL01 client-side gateway, DNS, mask, and suffix; 2026-10-05 ipconfig confirms both DNS servers. CL02/CL03 option recapture remains unclaimed.
- [x] Join all three clients to n3m0.test and move computer objects to N3M0-Lab/Workstations.
- [x] Validate domain user sign-ins and initial password changes.
- [x] Validate N3M0-Workstations-LogonNotice on all three clients (operator reported).
- [ ] Capture client DNS registration and a detailed Group Policy results report if needed for later troubleshooting.
- [x] Document the initial DC01 configuration and backup baseline.
- [x] Refresh client/domain-user documentation and change log after the delegation milestone.

Deliverable: domain design, permissions matrix, and validation screenshots.

## Phase 3 — Penetration testing foundations

- [ ] Choose and deploy attacker placement; consider a separate simulated external segment after the internal lab is stable.
- [ ] Define exercise targets, scope, starting access, and reset procedure.
- [ ] Practice discovery and service enumeration.
- [ ] Assess deliberately introduced lab weaknesses.
- [ ] Practice controlled AD attack-path exercises.
- [ ] Write findings with evidence, impact, and remediation.
- [ ] Apply fixes and retest.

Deliverable: reproducible assessment report with before/after evidence.

## Phase 4 — Detection and response

- [ ] Assess the existing Wazuh guest for authorized reuse.
- [ ] Deploy/configure monitoring and selected log sources.
- [ ] Verify log arrival and time consistency.
- [ ] Repeat a documented lab exercise and inspect telemetry.
- [ ] Create an investigation timeline and tune detection.
- [ ] Retest and record visibility gaps.

Deliverable: linked attack, detection, and remediation case study.

## Phase 5 — AI application security

- [ ] Build a small AI test application using synthetic data.
- [ ] Document the model endpoint, data flow, and trust boundaries.
- [ ] Test prompt injection and unintended information disclosure.
- [ ] Add restricted tool/MCP access and test permission boundaries.
- [ ] Evaluate approval controls, secret handling, and logging.
- [ ] Implement mitigations and regression-test the same cases.

Deliverable: AI threat model, test set, findings, and mitigation evidence.

## Phase 6 — Portfolio

- [ ] Select completed case studies and diagrams.
- [ ] Remove company identifiers, secrets, and unrelated information.
- [ ] Confirm permission before publishing company-derived material.
- [ ] Prepare a public-safe portfolio version and concise demonstrations.

This repository remains private unless deliberately changed. Completing a plan is not evidence that a control works; attach test results.

Recovery update (2026-09-10): a DC01 guest backup is now verified complete, superseding the earlier blanket no-backup status. Restore testing, retained-guest coverage, and off-host backup remain unverified.
