# Implementation guide

This is an ordered reconstruction guide derived from the project brief, not a configuration export. UI labels depend on the installed release. Record actual versions and interface mappings in the [inventory](../examples/lab-inventory.md) before making changes.

## 1. Establish a recovery path

Use the Proxmox/OPNsense console and keep a private backup of working configuration. Record how to restore the previous bridge, VM NIC and firewall settings. Perform one network change at a time. Keep management reachable; do not use a broad firewall reset as a troubleshooting shortcut.

## 2. Build the VLAN path

1. Identify the existing OPNsense WAN connection without changing it.
2. Enable VLAN awareness on `vmbr1`; verify allowed VLANs include 20.
3. Choose the firewall trunk or access model described in [architecture](architecture.md).
4. For the trunk model, connect the internal OPNsense NIC to `vmbr1` without an access tag; allow VLAN20 on that trunk. Create and assign VLAN20 on the correct parent inside OPNsense.
5. Attach the test VM to `vmbr1` with Proxmox tag `20`. Do not tag again inside an ordinary access guest.
6. Assign a non-overlapping subnet and gateway to VLAN20. Actual values must come from the inventory.

Checkpoint: guest traffic reaches the intended firewall interface. If not, inspect tagging and interface counters before modifying Layer 3 rules.

## 3. Configure dnsmasq DHCP

The recorded choice is **dnsmasq instead of Kea**; the source does not establish a Kea defect. Configure dnsmasq on VLAN20 with a pool inside its subnet, excluding the gateway and reserved addresses. Advertise the correct gateway and intended DNS resolver. Ensure Kea or another DHCP server is not simultaneously serving this segment. Record DNS separately: using dnsmasq for DHCP does not prove it provides recursive DNS.

Checkpoint: a fresh client lease has the intended subnet, mask, gateway and DNS values. Then test gateway reachability and DNS separately. Use the [official dnsmasq guide](https://docs.opnsense.org/manual/dnsmasq.html) for release-specific fields.

## 4. Implement stateful policy

Define required flows before entering rules. Start with the [illustrative policy matrix](../examples/firewall-policy.md). For VLAN20-originated traffic, inspect the inbound VLAN20 rules and any earlier floating/group rules. Add clear descriptions and log intentional denies. Preserve necessary DHCP/bootstrap handling and authorized management access.

Rule changes must be tested with **new connections**. Existing states can preserve a previously permitted flow. Inspect and remove only the test flow's state if needed; avoid flushing every connection. Record the matching rule and direction. See [OPNsense rules](https://docs.opnsense.org/manual/firewall.html).

## 5. Check outbound NAT and double NAT

Record the actual outbound NAT mode rather than assuming automatic, hybrid or manual. Ensure VLAN20 traffic has the intended translation on WAN. In the recorded double-NAT design, the upstream router performs a second translation. Diagnose route, allow rule, OPNsense translation and upstream reachability independently. Check any private-WAN filtering against the intended upstream design.

Checkpoint: compare a permitted test flow on the internal interface and WAN. Internet reachability alone does not prove the expected rule or translation matched. Do not add inbound port forwards merely to fix ordinary outbound connectivity.

## 6. Enable Suricata IDS with ET Open

Record the actual capture interface, HOME_NET, ET Open categories and update timestamps. Confirm IDS/alert mode; this repository does not claim IPS/drop mode. Enable only the required rulesets and check successful rule loading. Use a safe, controlled test signature before evaluating an appropriate ET Open signature with benign traffic. A local test signature proves the alert pipeline, not ET Open coverage.

Checkpoint: correlate one generated event with timestamp, signature ID, source and destination. Review false positives and encrypted-traffic limits. See [OPNsense Suricata documentation](https://docs.opnsense.org/manual/ips.html).

## 7. Apply threat-intelligence filtering

Record the actual source and alias type for `BAD_IPS`. If it is feed-backed, inspect the refresh result and populated entries; an empty or stale alias is not a working intelligence control. Place destination-based blocking for internal clients before broader outbound permits. Source-based WAN blocking is a separate direction and does not replace outbound destination blocking. Do not claim both directions are deployed without inspecting the rules.

Checkpoint: in an isolated test, temporarily add an owned lab target to the alias, start a new flow, and confirm the intended block rule. Remove the test entry and verify allowed behavior returns. Do not contact known malicious infrastructure. See [alias documentation](https://docs.opnsense.org/manual/aliases.html).

## 8. Capture evidence, then add monitoring

Run the [validation matrix](testing-validation.md). Record failures as well as successes. Wazuh remains a separate future milestone: follow the [monitoring design](wazuh-design.md) only when server access is restored and collection/parsing can be tested.
