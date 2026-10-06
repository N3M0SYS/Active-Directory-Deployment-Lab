# N3M0-DC02 deployment and AD/DNS replication

## Verified milestone — 2026-10-05

DC02 is deployed as an additional writable AD DS/DNS Global Catalog in n3m0.test. Bidirectional directory replication, automatic AD object and DNS record replication, SYSVOL/NETLOGON checks, and fresh Kerberos authentication for standard user rafa through DC02 passed.

Evidence is reviewed session screenshots and explicit operator confirmations across 2026-09-30 and 2026-10-05. Raw screenshots, credentials, tickets, and debug logs are not committed. Guest display times/time zones are not treated as authoritative session chronology.

## Build and current configuration

| Setting | Recorded state / evidence |
|---|---|
| VM / Windows name | N3M0-DC02 |
| OS | Windows Server 2025 Standard Evaluation, Desktop Experience; installation/rename confirmed by operator |
| Hyper-V | Generation 2, 4096 MB, 2 vCPUs, Lab-Private-Switch observed in setup screenshots |
| Disk | N3M0-DC02.vhdx; 80 GB dynamically expanding disk was guided, capacity not independently recaptured |
| Memory/security | Fixed RAM and Microsoft Windows Secure Boot were instructed; final settings not independently exported |
| IPv4 / mask / gateway | 10.50.10.11 / 255.255.255.0 / 10.50.10.1, captured |
| DNS client | After promotion: 10.50.10.10 plus IPv4/IPv6 loopback; no public resolver in reviewed promotion-state output |
| Domain / OU / site | n3m0.test; Domain Controllers; Default-First-Site-Name |
| Roles | Writable AD DS, DNS, GC; both DCs shown as GC |
| FSMO | All five roles remain on N3M0-DC01, verified with netdom query fsmo |
| DHCP | DC01 only; no DHCP failover configured |
| DNS forwarder | DC01's previously verified forwarder is 10.50.10.1; DC02 forwarder 10.50.10.1 captured 2026-10-06; root-hints fallback enabled |
| Time | DC01 uses time.windows.com,0x8; DC02 uses N3M0-DC01.n3m0.test |

Installed/renamed Windows, configured static networking and DC01 DNS, joined the existing domain, then installed AD DS using Server Manager. Promotion used the existing domain, DNS and GC enabled, RODC unchecked, DC01 as guided replication source, private DSRM password, and default NTDS/log/SYSVOL paths. Prerequisites passed with the expected isolated-domain parent DNS delegation warning. Promotion and restart succeeded.

## Validation

| Test | Result |
|---|---|
| Inbound replication on DC02 from DC01 | All five naming contexts successful: domain, Configuration, Schema, DomainDnsZones, ForestDnsZones |
| Inbound replication on DC01 from DC02 | All five successful after DNS correction and KCC/synchronization |
| Final repadmin /replsummary | Both DCs listed as source and destination; 0/5 failures for each, no errors |
| DC02 replication GUID CNAME | Direct queries to 10.50.10.10 and 10.50.10.11 both returned n3m0-dc02.n3m0.test |
| Basic DNS diagnostics | dcdiag DNS /DnsBasic: Authentication and Basic passed on both DCs |
| SYSVOL / NETLOGON | dcdiag SysVolCheck and NetLogons passed on both; both SYSVOL Policies paths opened with the four existing GPO folders |
| DFS Replication state | SYSVOL Share State 4 (Normal) on each DC |
| Advertising | Both DCs passed after time corrections |
| AD test object | Empty Global/Security ZZ-Replication-Test; change on DC02 appeared on DC01 without forced sync; deletion on DC01 appeared on DC02; removed from both |
| DNS test record | zz-dns-repl-test -> 192.0.2.123 created on DC02; appeared in direct query to DC01 after initial delay; deletion on DC01 appeared on DC02; removed from both |
| DHCP option 006 | DC01 scope now advertises 10.50.10.10 then 10.50.10.11 |
| CL01 lease/options | N3M0-CL01, DHCP enabled, 10.50.10.100/24, gateway 10.50.10.1, suffix n3m0.test, DHCP server 10.50.10.10, both DNS servers captured after renewal |
| Fresh standard-user authentication | CL01 normal session whoami = n3m0\\rafa; tickets purged; klist get host/N3M0-DC02.n3m0.test succeeded; new TGT and host ticket both named DC02 as Kdc Called |
| Client policy | rafa's gpupdate /target:user /force completed successfully |
| Temporary cleanup | klist purge_bind succeeded in elevated CL01 window; time debug logging disabled on both DCs |

The initial test-group screenshot used Notes rather than Description; operator corrected the field, then explicitly confirmed the reverse update. The successful AD deletion and DNS create/delete checks establish both directions without forcing those changes to synchronize.

## Problems and corrections

### DNS client settings

Initial member-server join displayed a primary DNS-name warning, but after restart DC02 reported PartOfDomain=True and n3m0.test suffix. DC discovery/SRV lookup succeeded; no rejoin was needed. A public alternate DNS address on DC02 was removed before promotion.

DC01 was later found using 8.8.8.8 and 1.1.1.1 rather than the documented internal DNS. This caused error 8524 and KCC replica-link warnings for DC02 -> DC01. Restoring preferred DNS to 10.50.10.10 allowed topology creation and synchronization:

```powershell
ipconfig /flushdns
repadmin /kcc N3M0-DC01
repadmin /syncall N3M0-DC01 /Ade
```

A remaining 1.1.1.1 alternate caused the first basic DNS diagnostic to fail. The final correction and verification were:

```powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "10.50.10.10"
Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
```

The final list contained only 10.50.10.10; basic DNS diagnostics then passed. A pre-sync CNAME lookup failed, but later direct queries passed on both DNS servers.

### Domain time

Both DCs initially selected VM IC Time Synchronization Provider with Leap Indicator 3 (not synchronized). DC01 failed Advertising because it was not advertising as a time server. FSMO verification established DC01 as the forest-root PDC.

DC01 was configured using:

```powershell
w32tm /config /manualpeerlist:"time.windows.com,0x8" /syncfromflags:manual /reliable:yes /update
```

On DC02:

```powershell
w32tm /config /syncfromflags:domhier /reliable:no /update
```

On each DC, the guest Windows Time VM provider was disabled, then Windows Time restarted and synchronization requested:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\TimeProviders\VMICTimeProvider" -Name Enabled -Value 0
Restart-Service W32Time
w32tm /resync /rediscover
```

DC01 synchronized to time.windows.com with Leap Indicator 0. DC02 initially returned no time data despite discovering DC01 with TIMESERV/GTIMESERV and receiving stripchart responses. Events showed valid responses and later unreachable-peer reports; the exact transient cause was not established. A later resync succeeded, with Leap Indicator 0 and DC01 as source. Reported dispersion remained elevated; these are point-in-time health results, not a long-term accuracy guarantee. Debug logging was disabled afterward.

DC01's local ADUC selector still labeled itself Unavailable despite successful Advertising, direct RootDSE query (synchronized/GC-ready), and IPv4 LDAP connectivity. From DC02 both DCs showed Online and ADUC changes succeeded. The local-console label's cause remains unresolved; hosts file and direct DNS AAAA check did not establish an incorrect mapping. IPv6 was not disabled.

### Authentication test privilege boundaries

klist add_bind and purge_bind require elevation. An elevated CL01 window temporarily preferred DC02; rafa's separate normal session purged its own tickets and requested fresh tickets. The screenshot confirmed Client rafa and Kdc Called DC02 for both TGT and host ticket. Cleanup succeeded in the elevated window. No privilege was added to rafa or rafa-admin.

## Limits, cleanup, and recovery

- A full DC01 outage/failover exercise was not performed. Fresh live Kerberos through DC02 passed while both DCs stayed online.
- DC02 updates and ISO ejection were confirmed 2026-10-06; external DNS and forwarder configuration were verified that day. A new backup is not claimed.
- CL02/CL03 DNS-option renewal was not recaptured; FS01 retains its existing DNS configuration. Departmental shares were not retested during this milestone.
- Test AD group and DNS record removed; temporary Kerberos preference and debug logging removed.
- Both DCs share one Hyper-V host. This adds directory/DNS service redundancy, not host-level availability.
- Windows activation remains intentionally out of scope. Host backups are not a prerequisite; no new restore test or backup coverage is claimed.
- Rollback was not exercised. If withdrawing DC02, remove its DHCP DNS advertisement and perform supported AD DS demotion while healthy, then verify AD/DNS cleanup before retiring the VM. Avoid deleting a promoted DC VM as the normal rollback.
- The time-provider registry change can be reversed by setting Enabled to 1; returning DC01 to domain hierarchy alone would leave the forest-root PDC without the intended external source.

## References

- [Microsoft DNS client settings](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/best-practices-for-dns-client-settings)
- [Replication error 8524](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/replication-error-8524)
- [Windows Time tools](https://learn.microsoft.com/en-us/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Root PDC time source](https://learn.microsoft.com/en-us/services-hub/microsoft-engage-center/health/remediation-steps-ad/configure-the-root-pdc-with-an-authoritative-time-source-and-avoid-widespread-time-skew)
- [klist](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/klist)


## 2026-10-06 — DC02 housekeeping and external DNS

Operator confirmed Windows updates completed and the installation ISO was ejected in Hyper-V. Resolve-DnsName www.microsoft.com -Server 10.50.10.11 returned CNAME, AAAA, and A records (observed IPv4 answer 173.223.1.196). DNS Manager on N3M0-DC02 showed forwarder 10.50.10.1 (N3M0-FW01.home.arpa), with root-hints fallback enabled. This verifies external resolution and the configured forwarder; it does not independently establish whether that specific query used forwarding, cache, or root hints. No raw screenshots were committed.


## 2026-10-06 — Post-update replication check

Reviewed repadmin /replsummary output captured at guest time 07:24:19. DC01 and DC02 each showed 0/5 failures (0%) in both Source DSA and Destination DSA tables, with no error entries. Largest deltas ranged from 30m10s to 36m27s. This confirms successful replication in the summary after the operator-confirmed DC02 updates; it does not establish full outage failover. No raw screenshot committed.
