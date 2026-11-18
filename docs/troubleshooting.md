# Troubleshooting and lessons learned

The topics below come from the reported project work. Exact historical error messages and packet captures are unavailable; diagnostic steps are reproducible guidance, not invented incident transcripts.

| Symptom | Hypothesis | Distinguishing check | Corrective direction |
| --- | --- | --- | --- |
| A connection survives a new block rule | Existing state still allows the flow | Match the connection tuple in the state table; retry a new flow | Remove only the relevant test state and retest |
| VLAN20 client receives no DHCP lease | Access/trunk mismatch, missing VLAN allowance or wrong DHCP binding | Follow DHCP Discover/Offer across guest, bridge and firewall | Make tagging consistent; bind DHCP to the correct interface |
| Gateway works, Internet does not | Rule, route, NAT or upstream failure | Compare ingress/WAN capture and matching rule/state | Fix the layer where forwarding stops |
| IP connectivity works, names fail | DNS configuration or DNS policy problem | Compare direct IP access with a query to the advertised resolver | Correct DHCP DNS option, resolver or permitted DNS flow |
| BAD_IPS does not block the test target | Empty alias, wrong direction, shadowing rule or stale state | Check alias membership, evaluated rules and a new connection | Correct source/destination placement and rule order |
| Suricata produces no test alert | Test traffic misses sensor, signature unloaded or HOME_NET wrong | Observe the packet on capture interface and check SID/rule loading | Correct visibility/configuration before tuning severity |
| Alerts show only firewall source IP | Sensor observes traffic after NAT | Compare internal and WAN tuples | Select a suitable internal observation point or retain NAT context |

## Access versus trunk: the key VLAN lesson

A VM NIC tagged `20` in Proxmox ordinarily provides an access attachment: the guest handles untagged frames. A trunk attachment delivers tags to a VLAN-aware guest. Adding VLAN20 inside a guest whose attachment is already access-tagged can put the two ends in disagreement. Draw where a tag is added and removed before changing DHCP or firewall settings.

## Stateful rules: policy and connection lifetime differ

A firewall's saved state explains why a rule edit is not necessarily visible to an existing session. Distinguish the original connection from a genuinely new one. Do not interpret an established browser session as proof that the new rule is ineffective. Avoid clearing unrelated states, especially the management session.

## DHCP: establish the Layer 2 path first

The lab settled on dnsmasq rather than Kea. That choice alone does not solve a tagging mismatch. First establish that broadcasts reach the intended interface; then inspect the service binding, address pool and competing DHCP servers. Retain the service choice without inventing a version-specific Kea failure.

## Double NAT: follow the tuple at each hop

Track original client address, OPNsense translated address and upstream translation separately. A failed external connection can come from routing, filtering, translation, DNS or the upstream device. NAT is not a substitute for an allow/deny policy.

## Detection versus prevention

Suricata IDS alerts on observed traffic; `BAD_IPS` firewall rules block matching traffic. A signature alert is not proof of a drop, and a reputation-based deny is not proof that the endpoint is infected. Keep detection evidence and prevention evidence separate.

## Minimum useful troubleshooting record

Record symptom, time, source/destination/protocol, expected rule, observed rule, relevant state, one hypothesis, one change and the retest. This makes a portfolio incident reproducible without exposing private configuration.
