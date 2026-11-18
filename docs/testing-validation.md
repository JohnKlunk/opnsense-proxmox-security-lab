# Testing and validation

**All tests below are Not run in this documentation session. No pass rate is claimed.** Implementation reported in the project history is distinct from reproducible evidence.

Use owned lab endpoints, record versions and timestamps, and preserve management access. Set up the inventory and baseline first. Use a fresh connection for policy changes. Store sanitized results using the [record template](../evidence/test-record-template.md).

| ID | Test and method | Acceptance criterion | Evidence to collect | Status |
| --- | --- | --- | --- | --- |
| NET-01 | Inspect vmbr1 VLAN awareness and both VM NIC attachments | VLAN20 allowed; guest access and firewall attachment agree | Sanitized bridge/NIC settings | Not run |
| DHCP-01 | Request a new lease from a VLAN20 client | Address, mask, gateway and DNS match the intended scope; one expected DHCP responder | Lease details and Discover/Offer metadata | Not run |
| NET-02 | Reach VLAN20 gateway; then query intended DNS | Gateway and authorized DNS work independently | Commands, timestamps and responses | Not run |
| FW-01 | Connect to an explicitly allowed service on an owned target | New connection succeeds through the intended rule | Client result, rule ID and state | Not run |
| FW-02 | Attempt a disallowed cross-zone service on an owned listening target | Block rule matches; target gets no application connection | Firewall log plus target listener/capture | Not run |
| FW-03 | Open permitted flow, apply test block, inspect state, retry fresh flow | Existing-state behavior documented; new connection denied | Rule change, tuple, state and two outcomes | Not run |
| NAT-01 | Trace one allowed IPv4 outbound flow inside and at WAN | Expected OPNsense source translation observed | Paired sanitized tuples | Not run |
| NAT-02 | Inspect upstream NAT for same flow where accessible | Second translation confirmed; otherwise record inconclusive | Upstream mapping or capture | Not run |
| IDS-01 | Generate harmless traffic matching a temporary local test SID | Alert has matching SID, time, source and destination | Signature, generation method and alert | Not run |
| IDS-02 | Review ET Open loading and exercise one suitable rule with benign matching traffic | Correct ET Open SID alerts for the intended flow | Ruleset version, SID, input and alert | Not run |
| TI-01 | Add an owned test target temporarily to BAD_IPS; connect using fresh state | Intended BAD_IPS block matches | Alias membership, rule ID and deny log | Not run |
| TI-02 | Remove test entry; repeat an otherwise permitted flow | Connectivity returns; no leftover test entry | Cleanup record and successful control | Not run |
| MGMT-01 | Attempt management access from unauthorized and authorized lab clients | Unauthorized denied; authorized access retained | Paired results and matching rules | Not run |
| IP6-01 | Inventory IPv6 and repeat isolation checks if enabled | IPv6 cannot bypass intended boundaries; or disabled state documented | Addresses, routes, rules and results | Not run |
| MON-01 | Deliver one event from each proposed source | Parsed fields and source identities preserved | Collector/raw event and parsed output | Blocked: Wazuh not deployed |
| MON-02 | Exercise positive and negative cases for each detection | Only intended cases alert with required context | Inputs, rule version and outputs | Blocked: Wazuh not deployed |

## Interpreting results

A timeout alone is insufficient proof of a firewall block: the target might be down. Demonstrate a listening service and a permitted control path, then correlate the denial with the intended rule. Likewise, successful Internet access does not prove isolation; positive and negative tests are both required.

For IDS-01, a temporary local ICMP echo-request rule against an owned lab target can provide a harmless trigger. Assign an unused local SID, record its exact text and remove it afterward. This validates pipeline visibility only. ET Open loading and IDS-02 are separate checks; do not relabel a local-signature alert as an ET Open result.

For TI-01, do not replace the whole live feed merely to test one entry. Use a reversible change appropriate to the alias type, record its previous state and restore it. Feed availability is a separate check from rule matching.

## Completion gate

A control becomes “verified” only after a dated record links the exact setup, input, expected result, observed result and sanitized artifact. Mark inconclusive results honestly. Documentation/link checks performed while preparing this repo are not live lab validation.
