# Domain deployment checklist

This project covers Windows domain deployment and administration. The former penetration-testing and detection phases move to a separate follow-on project; AI application security becomes a third project. See the [project series](project-series.md).

Core deployment and replication validation are complete as of October 5, 2026. Completion marks reflect reviewed evidence or explicitly identified lab notes, not a claim that every operational scenario has been tested.

## 1. Hyper-V and network foundation

- [x] Inventory the host and preserve retained guests.
- [x] Deploy the private lab switch and pfSense gateway.
- [x] Configure controlled uplink, DNS exception, and logged private-address blocking.
- [x] Verify external DNS, outbound TCP 443, and the tested upstream-router ICMP block.

## 2. Forest and domain services

- [x] Deploy DC01 with AD DS, DNS, and Global Catalog.
- [x] Create n3m0.test and validate initial DNS/service records and SYSVOL/NETLOGON.
- [x] Install and authorize DHCP; configure N3M0-Clients scope.
- [x] Complete an initial local DC01 guest backup and document its limits.

## 3. Clients, identity, and Group Policy

- [x] Deploy and join IT, Finance, and HR Windows 11 clients.
- [x] Create OUs, standard users, and departmental groups.
- [x] Validate domain sign-ins and the workstation logon-notice policy.
- [x] Install RSAT on CL01 and separate daily-use/admin identities.
- [x] Delegate password reset and account unlock; validate allowed and denied operations.
- [x] Complete client updates and media cleanup during lab validation.

## 4. File services and permissions

- [x] Deploy domain-joined FS01.
- [x] Configure departmental AGDLP groups and share/NTFS permissions.
- [x] Validate departmental file operations and cross-department denial during lab validation.
- [x] Configure group-targeted drive mappings and verify automatic recreation during lab validation.
- [x] Complete FS01 updates and media cleanup during lab validation.

## 5. Second domain controller and replication

- [x] Deploy DC02 as a writable AD DS/DNS/GC server.
- [x] Correct DC01 DNS client settings and resolve replication error 8524.
- [x] Validate all five directory partitions in both directions with zero final failures.
- [x] Validate automatic AD object and DNS record changes in both directions; remove test artifacts.
- [x] Verify basic DNS, Advertising, SYSVOL/NETLOGON, and DFSR State 4 on both DCs.
- [x] Configure external time on DC01 and domain time on DC02; verify synchronized sources.
- [x] Advertise both DNS servers through DHCP and capture CL01 renewal.
- [x] Verify fresh standard-user Kerberos tickets issued by DC02 and successful user policy refresh.
- [x] Remove temporary KDC preference and disable diagnostic logging.
- [x] Finish DC02 updates and ISO ejection during lab validation; verify external DNS resolution and pfSense forwarder configuration.

## 6. Documentation and portfolio finish

- [x] Maintain build journals, architecture, inventory, recovery limits, and change history.
- [x] Give this repository a domain-deployment-only README and scope.
- [x] Rename repository to Active-Directory-Deployment-Lab and update its description/topics.

