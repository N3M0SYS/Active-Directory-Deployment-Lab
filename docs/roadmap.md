# Roadmap

Goal: penetration testing foundations, detection and remediation, then AI security engineering. Check an item only after its outcome is verified.

## Phase 0 — Baseline and recovery

- [x] Inventory CPU, RAM, volumes, existing guests, switches, and physical adapters.
- [x] Choose Hyper-V as the main lab platform, including Kali and Windows clients.
- [x] Establish a private GitHub documentation repository.
- [ ] Investigate Windows notification mode and establish valid licensing status.
- [ ] Agree on available resources and shared VM ownership.
- [ ] Record current host network settings securely.
- [ ] Verify backups or document an agreed recovery approach for affected resources.
- [ ] Confirm local recovery access and a rollback procedure.
- [ ] Finalize addressing and network design.

Deliverable: verified baseline, decision log, and recovery plan.

## Phase 1 — Isolated networking

- [ ] Select the lab firewall and validate its requirements.
- [ ] Create the private lab network.
- [ ] Configure the approved uplink and access restrictions.
- [ ] Verify lab DHCP remains contained.
- [ ] Verify required access works and access to company/client networks is blocked.
- [ ] Document configuration and recovery steps.

Deliverable: network diagram and isolation test evidence.

## Phase 2 — Windows business environment

- [ ] Deploy DC01 with AD DS and DNS.
- [ ] Deploy DC02 and verify replication.
- [ ] Configure organizational units, users, groups, and separate admin accounts.
- [ ] Deploy FS01 and test share/NTFS permissions.
- [ ] Deploy IT-ADMIN and two employee clients.
- [ ] Join clients to the domain and verify DNS and Group Policy.
- [ ] Document a clean recovery baseline.

Deliverable: domain design, permissions matrix, and validation screenshots.

## Phase 3 — Penetration testing foundations

- [ ] Deploy Kali inside the lab.
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
