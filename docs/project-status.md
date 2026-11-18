# Project status and provenance

Documentation baseline: 2026-11-18.

## Source boundaries

The project brief explicitly identifies OPNsense/Proxmox, state tracking, NAT/double NAT, VLAN20, VLAN-aware `vmbr1`, access/trunk debugging, dnsmasq rather than Kea, Suricata/ET Open and `BAD_IPS` filtering. These are recorded as **reported implementation**, not observations made while writing this repository.

The available continuation of “Cyber Security Einstieg” confirms the high-level completion summary and the Wazuh design phase. It also states that server access was unavailable after a move. The retrievable conversation returned only the final five exchanges, with no older-page cursor. Earlier detailed configuration exchanges, screenshots and live exports were not available. No synced project reference files were present.

The monitoring discussion defines three intended detections: high-priority Suricata alerts, repeated firewall blocks from one source, and internal connections to `BAD_IPS`. Thresholds, transport details and acceptance criteria in this repository are **new design proposals**, not previously deployed settings.

## Facts still to collect

| Item | Status |
| --- | --- |
| Proxmox, OPNsense, Suricata versions | Unknown |
| VM resources and physical NIC mapping | Unknown |
| Exact IP subnets, WAN address and upstream gateway | Unknown; deliberately not invented |
| OPNsense internal interface names and final access/trunk settings | Unknown |
| Rule IDs, floating/group rules and NAT mode | Unknown |
| ET Open categories, rule versions, HOME_NET and capture interface | Unknown |
| BAD_IPS feed URL/provider, type, refresh interval and count | Unknown |
| IPv6 and management-plane isolation | Unverified |
| Live Wazuh components, connected agents, alerts | **None deployed in the documented project** |

## Status vocabulary

- **Reported implemented:** described as completed by the project context.
- **Proposed:** a reconstruction choice or future design.
- **Not run:** no executed test result is available.
- **Verified:** reserved for a dated observation with linked evidence.

Use these labels consistently when adding future evidence. A configuration screenshot alone does not demonstrate that a security control works.
