# Roadmap

## Documented baseline

- [x] Describe reported firewall, NAT, VLAN20, DHCP, IDS and threat-intelligence work.
- [x] Document access/trunk and state-tracking lessons without inventing incident evidence.
- [x] Define a Wazuh monitoring architecture and three detection use cases.
- [x] Provide reproducible validation and evidence templates.

## Milestone 1 — recover and verify the lab

- [ ] Restore server access and record current versions/hardware/interface mapping.
- [ ] Recover actual rule order, NAT mode, VLAN attachments and dnsmasq scope.
- [ ] Record Suricata capture point, HOME_NET, ET Open configuration and BAD_IPS feed details.
- [ ] Run positive and negative network/security tests; publish sanitized evidence.
- [ ] Confirm management restrictions and IPv6 policy.

Exit criterion: each reported control has a dated result or an explicit unresolved gap.

## Milestone 2 — deploy monitoring

- [ ] Size and deploy Wazuh manager, indexer and dashboard.
- [ ] Establish restricted log collection and clock synchronization.
- [ ] Validate OPNsense parsing, Suricata alert parsing and one endpoint agent.
- [ ] Implement DET-01 through DET-03 using verified fields.
- [ ] Test thresholds, negative cases and duplicate handling.
- [ ] Publish real, redacted alerts and a short investigation walkthrough.

Exit criterion: all three use cases have positive/negative test evidence; ingestion failures are observable.

## Milestone 3 — operate and improve

- [ ] Track feed freshness, rule updates and failed collection.
- [ ] Tune false positives using documented observations.
- [ ] Measure resource use and define retention from actual volume.
- [ ] Test backup/restore and controlled rollback.
- [ ] Evaluate IPS only as a separate change with compatibility, performance and rollback testing.
- [ ] Select and apply a repository license after reviewing scope.
