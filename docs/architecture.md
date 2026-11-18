# Architecture and trust boundaries

## Packet path

For an allowed IPv4 outbound connection, the intended logical path is:

`VLAN20 guest → vmbr1 → OPNsense VLAN20 interface → stateful policy → outbound source NAT → upstream router source NAT → Internet`.

Return traffic follows the established translations and state. A port forward would be a separate inbound design and is not claimed as deployed. Double NAT introduces two translation layers; it does not replace filtering. See [OPNsense NAT](https://docs.opnsense.org/manual/nat.html).

## VLAN20: two valid attachment patterns

| Attachment | Proxmox VM NIC | Guest / firewall sees | Required guest configuration |
| --- | --- | --- | --- |
| Access endpoint | Bridge `vmbr1`, VLAN tag `20` | Untagged Ethernet | Ordinary interface, no VLAN20 subinterface |
| Trunk to OPNsense (reconstruction pattern) | Bridge `vmbr1`, no access tag; allowed trunk VLANs include 20 | Tagged VLAN20 | VLAN20 child on the matching parent NIC; assign and enable it |
| Access-only OPNsense alternative | Bridge `vmbr1`, VLAN tag `20` | Untagged Ethernet | Assign parent NIC directly; do not also add VLAN20 inside OPNsense |

Choose exactly one tagging model per attachment. “VLAN aware” alone does not confirm that the guest NIC, trunk filter, physical switch and firewall agree. An internal-only bridge needs no physical trunk; a bridge with physical members requires matching switch configuration. The actual final lab attachment is not available in the source material.

A minimal **illustration**, not a replacement for `/etc/network/interfaces`:

```text
auto vmbr1
iface vmbr1 inet manual
    bridge-ports none
    bridge-stp off
    bridge-fd 0
    bridge-vlan-aware yes
    bridge-vids 20
```

This illustrates a dedicated internal lab bridge carrying VLAN20. Merge against the actual host design; preserve management and other existing VLANs. Do not paste over a live network file. Proxmox documents bridge and guest tagging behavior in its [administration guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.pdf).

## Trust boundaries and visibility

- **Upstream network:** carries OPNsense WAN traffic; may also contain private household or management hosts. No WAN subnet is assumed.
- **VLAN20:** lab endpoints. Routing policy should prevent unauthorized access to protected networks.
- **Management plane:** Proxmox and OPNsense administration should be restricted to authorized management hosts. Current separation is unverified.
- **Monitoring plane:** future collector and Wazuh components; telemetry access, retention and transport need configuration.

Same-VLAN traffic can remain on the bridge and bypass OPNsense. A virtual NIC does not automatically see every other VM's traffic. Suricata coverage must be established for its actual capture point. WAN-side NAT can obscure the original client address; an internal capture point is proposed for attribution. This does not imply the current IDS uses that placement.

The editable [diagram source](../architecture/topology.mmd) mirrors the README. Both distinguish planned telemetry from network forwarding.
