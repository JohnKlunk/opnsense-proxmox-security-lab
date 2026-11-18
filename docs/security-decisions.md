# Security decisions

“Recorded” means present in the project context. “Proposed” means guidance added for reconstruction or a future phase. None of these entries substitutes for live verification.

| Decision | Basis | Rationale | Tradeoff / follow-up |
| --- | --- | --- | --- |
| Use OPNsense on Proxmox | Recorded | Practice real routing and policy in a manageable virtual lab | Host or virtual-network faults can affect the firewall; preserve console access |
| Segment the lab with VLAN20 on VLAN-aware vmbr1 | Recorded | Separate a lab broadcast domain and apply policy at routed boundaries | Same-VLAN peers may bypass firewall inspection; prove isolation with negative tests |
| Account for double NAT | Recorded | Work within an upstream-routed environment | More translation state and harder inbound troubleshooting |
| Use dnsmasq for DHCP rather than Kea | Recorded | Matches the selected lab implementation | Avoid overlapping DHCP services; verify actual lease options |
| Run Suricata as IDS with ET Open | Recorded | Build traffic visibility and signature-analysis skills | Alerts do not block traffic; signature quality and placement matter |
| Block reputation matches with BAD_IPS | Recorded | Add explicit intelligence-informed filtering | False positives, stale entries and feed failures need operational handling |
| Design Wazuh before deployment | Recorded | Specify what to collect and detect while server access is unavailable | No validated ingestion, correlation or dashboards yet |
| Restrict management and unnecessary inter-zone traffic | Proposed acceptance target | Limit administrative exposure and lateral movement | Requires actual inventory and rule review |
| Review IPv6 explicitly | Proposed acceptance target | Prevent a parallel path from escaping IPv4-only policy | IPv6 operation is unknown; validate separately if enabled |

## Threat model

Protect management access, lab workloads and configuration integrity. Relevant scenarios include unwanted cross-zone access, unsolicited inbound traffic, internal attempts to reach a listed destination, and repeated connection attempts. Host compromise, malicious traffic hidden inside encryption, hypervisor compromise and threats absent from the feeds/signatures remain possible.

## Rule design principles

Use explicit source/destination objects, minimal required services and meaningful descriptions. Review effective evaluation order, including floating/group rules, rather than relying only on the interface list. Permit approved DNS/NTP and management exceptions deliberately. Log actionable denies while monitoring volume.

Treat the [example policy](../examples/firewall-policy.md) as a target to evaluate, not a claim about the current configuration. IPv4 NAT does not implement an IPv6 policy.

## Information handling

Keep raw exports, credentials, packet captures and unredacted logs outside the public repository. Sanitized evidence should preserve relationships needed for analysis while replacing identifiers consistently. Record a timezone and retain originals privately. Check staged files manually: `.gitignore` is a guardrail, not a secret scanner and not a way to remove already tracked data.
