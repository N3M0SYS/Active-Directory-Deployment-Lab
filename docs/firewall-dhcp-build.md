# Firewall, DNS, and DHCP build journal

## Status — 2026-09-17

pfSense CE N3M0-FW01 is installed and configured. DC01 DNS and internet tests passed, and pfSense logged the upstream router block. Windows DHCP scope **N3M0-Clients** is configured per operator report. Three Windows 11 Pro clients now exist; their leases were observed in DC01 DHCP and domain joins/sign-ins succeeded per operator.

Evidence consists of reviewed screenshots and operator reports from the session; raw screenshots and credentials are not committed. Guided settings completed by the operator are distinguished from directly observed test results below.

## Hyper-V and firewall build

- Existing NIC1 remains the physical host management connection.
- Connected NIC2 to a spare port on the existing managed switch; both NIC1 and NIC2 showed Up at 1 Gbps.
- Created Lab-WAN-Switch through Virtual Switch Manager: External, NIC2 (Intel I350-t rNDC #4), management OS sharing unchecked; VLAN and SR-IOV unchecked.
- Created N3M0-FW01 following the GUI instructions: Generation 2, 2 vCPUs, 4096 MB fixed RAM, 32 GB VHDX, Secure Boot disabled.
- WAN virtual adapter uses Lab-WAN-Switch; LAN adapter uses Lab-Private-Switch. No VLAN IDs configured.
- Installed pfSense CE using the Netgate installer; guided storage choices were ZFS, GPT, single-disk stripe. Exact installed CE release was not captured.
- ISO instructions: extract the .gz to obtain the .iso, do not extract the ISO contents; keep it attached during installation, then eject and boot the hard disk. Successful installed-system boot was observed.
- Console showed WAN hn0 receiving an upstream DHCP lease and LAN hn1 at 10.50.10.1/24.
- Completed web wizard from DC01: hostname N3M0-FW01, domain home.arpa, America/Chicago, WAN DHCP, private-network WAN blocking unchecked, bogon blocking checked, new admin password. Guided DNS fields were blank with override enabled.
- LAN DHCP server remains disabled per the guided plan; Windows DHCP provides the lab scope.

## LAN policy

Alias PRIVATE_NETWORKS (Network type): 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.

| Order | Action | Source | Destination | Protocol/port |
|---|---|---|---|---|
| 1 | Existing anti-lockout | Existing setting | LAN address | Web management |
| 2 | Pass: Allow lab DNS to pfSense | LAN subnets | LAN address | IPv4 TCP/UDP 53 |
| 3 | Block/log: Block lab access to private networks | LAN subnets | PRIVATE_NETWORKS | IPv4 any |
| 4 | Default allow LAN to any | LAN subnets | Any | IPv4 any |
| 5 | Disabled default allow | LAN subnets | Any | IPv6 |

A screenshot initially showed the block above the DNS exception; the operator confirmed correcting the order and applying it. Later DNS and firewall tests succeeded. Private-address filtering does not isolate guests from one another within the same subnet. It can also block additional services addressed to the firewall itself; future exceptions should be explicit.

## DC01 DNS and DHCP

| Setting | Value |
|---|---|
| DC01 IP / mask | 10.50.10.10 / 255.255.255.0 |
| Default gateway | 10.50.10.1 |
| Preferred DNS | 10.50.10.10 |
| Alternate DNS | Blank |
| DNS forwarder | 10.50.10.1 |
| AD domain / DNS suffix | n3m0.test |
| DHCP server | N3M0-DC01; installed and authorized per completed post-install wizard |
| Scope name | **N3M0-Clients** |
| Pool | 10.50.10.100–10.50.10.199 |
| Prefix | /24 |
| Lease duration | 8 days |
| Exclusions | None; infrastructure addresses are outside the pool |
| Option 003 Router | 10.50.10.1 |
| Option 006 DNS Servers | 10.50.10.10 then 10.50.10.11 (verified 2026-10-05) |
| Option 015 DNS Domain Name | n3m0.test |
| WINS | Not configured |
| Scope activation | Completed per operator |
| Client validation | CL01 10.50.10.100, CL02 .101, CL03 .102 observed in Address Leases; operator reports leasing works |

The scope name is N3M0-Clients, not the initially suggested Lab-Clients. Domain clients receive DC01 and DC02 for DNS; DC01's existing external forwarder is pfSense. DC02 external forwarding has not been separately captured.

## Validation evidence

| Check | Observed result | Limit |
|---|---|---|
| Resolve-DnsName www.microsoft.com -Server 10.50.10.10 | Returned CNAME, A, and AAAA records | Does not independently prove which forwarding/fallback path answered |
| Test-NetConnection 1.1.1.1 -Port 443 | TcpTestSucceeded True, operator reported | Verifies this outbound TCP path |
| ping -n 2 to upstream router from DC01 | Timed out; reviewed pfSense logs show two matching ICMP blocks by the named private-network rule | Verifies tested destination/protocol, not all isolation paths |
| DNS Manager zones | n3m0.test and _msdcs.n3m0.test are AD-integrated and Running | Not a complete AD health check |
| Resolve-DnsName _ldap._tcp.dc._msdcs.n3m0.test -Type SRV -Server 10.50.10.10 | Returned n3m0-dc01.n3m0.test and A record 10.50.10.10 | Later client joins and authentication succeeded per operator |
| DHCP installation and scope | Operator reported successful completion | Client-side option values not separately captured |
| Client DHCP leases | Reviewed Address Leases screenshot: CL01 10.50.10.100, CL02 .101, CL03 .102 | Dynamic leases, not reservations; does not prove DHCP containment |
| Domain sign-ins and workstation GPO | All three clients passed per operator | See client journal; no packet capture or policy-results export |

AAAA records do not establish working IPv6 connectivity. Browser HTTPS access was requested but no explicit success report was supplied.

## Recovery and next step

This is an authorized disposable test server; full rebuild is accepted. No host-backup prerequisite applies. The earlier local DC01 backup predates this work. No new backup, configuration export, or restore test is claimed.

Hyper-V console access remains available. To isolate the uplink if needed, disconnect the firewall WAN virtual adapter while preserving NIC1 host management and the private switch; rollback has not been tested.

Client deployment, DHCP leases, joins, domain authentication, and the logon-notice GPO milestone are complete. Update 2026-09-29: FS01 and departmental permission tests are complete; see [FS01 journal](fs01-build.md). Next: separate administration accounts and scoped delegation. Client-side option capture and broader isolation validation remain separate pending checks. See [Windows client journal](windows-clients-build.md).

## 2026-10-05 — DC02 DNS and DHCP option update

DC02 10.50.10.11 is deployed with verified AD-integrated DNS replication. DHCP remains only on DC01; scope option 006 now advertises DC01 then DC02. CL01 renewal and full ipconfig verified both DNS servers, /24 mask, gateway 10.50.10.1, suffix n3m0.test and DHCP server 10.50.10.10. Other clients' option renewal was not recaptured. No firewall rule change or DHCP failover was performed. DC01 public DNS client settings were corrected to internal DNS only; external forwarding remains a DNS-server setting. See [DC02 journal](dc02-build.md).
