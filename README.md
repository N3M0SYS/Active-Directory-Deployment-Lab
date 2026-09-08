# Security Engineering Lab

A hands-on progression from Active Directory administration and penetration testing to AI security engineering.

**Owner:** N3M0SYS  
**Platform:** Authorized company test server running Hyper-V  
**Status:** Inventory complete for initial planning; deployment has not started.

## Start here

- [Roadmap and phase checklists](docs/roadmap.md)
- [Verified host inventory](docs/inventory.md)
- [Proposed architecture and VM sizing](docs/architecture.md)
- [Recovery and deployment gates](docs/recovery.md)
- [Documentation and evidence workflow](docs/documentation.md)
- [Change log](CHANGELOG.md)
- [Lab exercise template](templates/lab-exercise.md)
- [Evidence folders](evidence/README.md)

## Learning outcomes

Build and administer a small Windows domain, assess it using Kali, detect activity using Wazuh, remediate findings, and demonstrate the improvement. Extend those skills into AI application security, including prompt injection, data exposure, agent permissions, and MCP tool access.

## Current priorities

1. Investigate the host's Windows notification-mode result.
2. Confirm recovery arrangements and shared resource expectations.
3. Finalize an isolated network and controlled remote access.
4. Deploy the firewall, AD, Windows clients, Kali, and monitoring in phases.

All architecture and VM allocations are proposals until deployment evidence is recorded. The existing nine VMs have not been modified. GitHub tracks documentation and reviewed scripts; it is not the VM backup destination.

## Working agreement

Show the full procedure first, then run one command at a time and review the output. Document the current state, recovery plan, and backup status before infrastructure changes. Update this repository during working sessions after verifying results. Screenshots and recordings supplied by the operator become supporting evidence after review.
