# Project series

Three separate repositories organize the portfolio around distinct outcomes. Each follow-on project references the prior foundation rather than mixing all work into one deployment journal.

| Order | Project | Focus | Status |
|---|---|---|---|
| 1 | Active-Directory-Deployment-Lab | Deploy and configure AD DS, DNS, DHCP, clients, Group Policy, file services, delegation, and replication | Current repository; core build validated |
| 2 | AD-Penetration-Testing-and-EDR-Lab | Authorized AD assessment paired with endpoint telemetry, detection, investigation, remediation, and retesting | Planned; proposed repository name |
| 3 | AI-Application-Security-Lab | Threat-model and assess an AI application, including prompt injection, information disclosure, and tool authorization | Planned; proposed repository name |

## Project 1: domain deployment

Deliver a reproducible infrastructure build and explain what was validated. Keep domain troubleshooting and administrative permission tests here. Track remaining deployment checks in the [checklist](roadmap.md).

## Project 2: penetration testing and endpoint detection

Combines the former phases 3 and 4. Use the domain environment as the assessment foundation. Define scope, starting access, target weaknesses, and reset procedure for every authorized exercise.

Pair findings with endpoint evidence: attack sequence, collected telemetry, detection behavior, investigation timeline, remediation, and repeated tests. Select and validate the endpoint security tooling before claiming EDR capability. The retained Wazuh guest has not yet been validated for this project.

Planned deliverables: scope and topology, assessment report, attack-to-detection case studies, remediation/retest results, and documented visibility gaps.

## Project 3: AI application security

Build a small test application with synthetic data. Reuse identity, least-privilege, trust-boundary, evidence, and remediation lessons from the first two projects.

Assess prompt injection, unintended disclosure, restricted tool/MCP access, authorization boundaries, secrets, logging, and approval controls. Record fixes and a regression test set.

Planned deliverables: application/data-flow diagram, threat model, reproducible security tests, findings, mitigations, and regression results.

## Linking the portfolio

When follow-on repositories exist, add reciprocal links and identify the specific foundation revision used. Keep each README focused on its own demonstrated work and clearly separate planned outcomes from completed results. No follow-on repository or security assessment is claimed to exist yet.
