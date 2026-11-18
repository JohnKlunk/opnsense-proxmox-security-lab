# Illustrative firewall policy — proposed target, not an export

Apply to new flows entering from VLAN20 unless stated otherwise. Actual rule IDs and installed policy remain unverified. Inspect floating/group/automatic rules as well as interface rules.

| Relative order | Source | Destination | Service | Action / purpose |
| --- | --- | --- | --- | --- |
| Bootstrap | VLAN20 DHCP clients | VLAN20 DHCP service | Required DHCP exchange | Preserve service handling; inspect generated rules |
| 1 | Authorized admin hosts only | Specific management endpoints | Required admin ports | Allow only if these hosts legitimately originate here |
| 2 | VLAN20 net | Approved DNS and NTP endpoints | DNS TCP/UDP 53; NTP UDP 123 as needed | Allow infrastructure services |
| 3 | VLAN20 net | BAD_IPS | Any | Block and log intelligence match |
| 4 | VLAN20 net | Protected/internal networks | Any | Block unauthorized cross-zone and upstream-private access |
| 5 | VLAN20 net | Approved external destinations | Required services, e.g. TCP 80/443 | Allow scoped egress |
| Default | Any remaining | Any | Any | Deny; choose useful logging |

Define protected networks from the real topology, including the upstream private subnet and management addresses. Preceding exceptions must be deliberate. If a BAD_IPS entry overlaps an infrastructure exception, document which takes precedence. Extend the design explicitly for IPv6 if enabled.

An optional WAN rule with source `BAD_IPS` addresses a different flow direction. It is not a substitute for an internal-interface destination block. This document does not claim an exact deployed WAN rule.
