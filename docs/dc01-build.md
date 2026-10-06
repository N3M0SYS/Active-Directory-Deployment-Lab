# N3M0-DC01 build journal

## Scope and evidence

Work completed across the 2026-09-09–10 session; documentation updated 2026-09-11. Results below come from operator reports. No credentials, keys, or backup images are included.

## Final configuration

| Setting | Recorded state |
|---|---|
| Hypervisor | Hyper-V on the authorized test server |
| VM and Windows name | N3M0-DC01 |
| Generation | 2 |
| Secure Boot | On; MicrosoftWindows template in reviewed firmware output |
| RAM | 4096 MB |
| CPU | 2 virtual processors |
| OS disk | N3M0-DC01.vhdx; 60 GB |
| OS | Windows Server 2025 Standard Evaluation, Desktop Experience selected during guided installation |
| Network | Lab-Private-Switch; Private |
| IPv4 / mask | 10.50.10.10 / 255.255.255.0 |
| Gateway | 10.50.10.1 |
| DNS client | Preferred 10.50.10.10; alternate blank |
| IPv6 | Enabled; automatic settings |
| Domain / forest | n3m0.test |
| NetBIOS | N3M0 |
| Functional levels | Windows Server 2025 for domain and forest |
| Roles | AD DS, DNS, DHCP Server, Global Catalog; writable DC |
| DNS forwarder | 10.50.10.1 |
| DHCP scope | N3M0-Clients; 10.50.10.100–199/24; client lease validation pending |
| AD database and logs | `C:\Windows\NTDS` |
| SYSVOL | `C:\Windows\SYSVOL` |
| Backup disk | N3M0-DC01-Backup.vhdx; 100 GB dynamic; GPT/NTFS; E:; DC01-Backup |
| DVD media | Operator guided to eject after installation; ISO retained for possible recovery |

## Procedure completed

1. Verified the private switch and absence of DC01 in the initial guest inventory.
2. Created the VM and 60 GB VHDX; attached Windows Server installation media.
3. Renamed Windows through System/About and restarted. The VM display name alone did not rename Windows.
4. Configured static IPv4 settings; re-enabled IPv6 after discussing Windows guidance.
5. Installed AD DS through Server Manager; promoted the server into a new forest, n3m0.test, with DNS and GC enabled.
6. Set a private DSRM password; retained default database/log/SYSVOL paths.
7. Prerequisite checks passed. DNS delegation warning was expected because no parent-zone delegation was available for this isolated test domain.
8. Completed promotion, automatic restart, and domain Administrator sign-in.
9. Verified AD registration, DNS records, and shared folders.
10. Installed Windows Server Backup; created and formatted the separate guest backup disk.
11. Ran Backup Once, Full server, VSS Copy Backup to E:. All listed items completed; 15.32 GB transferred.

## Verification results

| Check | Observed result |
|---|---|
| Active Directory Users and Computers | n3m0.test exists; N3M0-DC01 appears in Domain Controllers as GC |
| Host A record | n3m0-dc01.n3m0.test → 10.50.10.10 |
| Domain apex A record | 10.50.10.10 |
| _msdcs.n3m0.test → dc → _tcp | _ldap SRV target n3m0-dc01.n3m0.test, port 389 |
| Same SRV folder | _kerberos target n3m0-dc01.n3m0.test, port 88 |
| File Explorer inside DC01 | NETLOGON and SYSVOL visible at `\\N3M0-DC01` |
| Windows Server Backup | Completed, 15.32 GB to E:, including System State and bare-metal recovery |

These are initial GUI checks. They do not establish client domain-join success, replication, comprehensive DC health, or successful recovery.

## Troubleshooting and lessons

- Initial wizard summary showed no hard disk; installation required attaching/creating the OS disk.
- Recreating the VM hit 0x80070050 because the chosen VHDX path already existed. Avoid recreating disks blindly.
- UEFI boot failure persisted after downloading another ISO. DVD-first order and MicrosoftWindows Secure Boot were confirmed; the ISO mounted successfully on the host.
- Operator reported seeing the DVD keypress prompt and subsequently reached Setup after console-input guidance. Exact cause was not conclusively established; ISO corruption was not demonstrated.
- Accessing `\\N3M0-DC01` from the Hyper-V host failed with 0x80070043. The same path worked inside DC01. This was a host-versus-guest context error; Network Discovery does not connect a private switch to the host.
- Installation media can be ejected after installation; avoid booting into Setup again during OS restarts.
- Prefer GUI instructions and distinguish host actions from guest actions.

## 2026-09-11 — Firewall integration and DHCP

- Set gateway to 10.50.10.1. The IPv4 screenshot showed loopback DNS 127.0.0.1; changed preferred DNS to the explicit DC01 address 10.50.10.10 following the session guidance.
- Added DNS forwarder 10.50.10.1 to the previously empty forwarder list.
- External DNS query through 10.50.10.10 returned A/AAAA records; TCP 443 to 1.1.1.1 succeeded.
- AD SRV lookup returned n3m0-dc01.n3m0.test with its A record 10.50.10.10.
- Installed DHCP Server and completed post-install configuration/AD authorization per operator.
- Created and activated N3M0-Clients per operator; this exact name supersedes the suggested Lab-Clients name.
- No Windows client exists yet. See [firewall and DHCP journal](firewall-dhcp-build.md).

## Open work

- Activate/update the guest using the approved path.
- Restore capability remains untested; it is not a prerequisite for continued work on this disposable lab.
- Deploy a client and verify actual domain join, authentication, and Group Policy.
- Add OUs, separate admin/user accounts, DC02, FS01, and monitoring in later steps.

## References

- [Microsoft AD DS installation](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-)
- [Microsoft DNS client recommendations](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/best-practices-for-dns-client-settings)
- [Microsoft IPv6 guidance](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/configure-ipv6-in-windows)
- [RFC 2606: reserved test domains](https://www.rfc-editor.org/info/rfc2606/)

## 2026-10-05 — DC02 replication and DC01 corrections

DC01 retains all five FSMO roles. KCC and inbound synchronization succeeded afterward. Both DCs now pass basic DNS, Advertising, SysVolCheck, NetLogons and show SYSVOL DFSR State 4. Final replication summary has zero failures for both sources/destinations. Automatic AD/DNS tests passed both ways and were cleaned up.

DC01 is configured as reliable forest-root PDC using time.windows.com,0x8; its guest VMICTimeProvider Enabled value was set to 0, Windows Time restarted, and Leap Indicator 0/external source verified. DC02 follows the domain hierarchy. DHCP scope option 006 advertises both 10.50.10.10 and 10.50.10.11; CL01 lease and fresh rafa authentication through DC02 verified. Local DC01 ADUC selector still showed Unavailable despite successful direct queries; both DCs showed Online from DC02. No outage failover or new backup is claimed. Details and actual commands: [DC02 journal](dc02-build.md).
