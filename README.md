# ZapLedger

*A governance and observability layer for Zapier estates, built entirely on Zapier.*

## Why This Exists

Enterprise Zapier customers hit three walls at scale.

- "We have 400 Zaps across 12 teams and nobody knows what half of them do or what breaks if they fail."
- "Our auditor asked us to prove our automations are governed. We can't."
- "A Zap failed 6 hours ago and cost us serious money. Why didn't we know?"

Zapier already governs its own platform well: SSO, SCIM, role-based access, approval workflows, audit logs across every product. None of that maps a specific Zap run to a specific control, like NIST 800-171 practice 3.1.1 or SOC 2 CC6.2, or produces a signed evidence bundle an auditor can use without translation. ZapLedger is that layer, built entirely on Zapier.

## What Ships in Tier B (This Repo)

- **Zap Fabric Map**: interactive dependency graph of the workspace's Zap estate.
- **Control Attestation Engine**: cryptographic evidence for every governed Zap run, mapped to NIST 800-171, SOC 2, HIPAA, and CMMC.
- **Autonomous Repair Agent**: Zapier Agent that detects failures, diagnoses root cause, proposes fixes, and waits for human approval.

Built with: Zaps, Zapier Tables, Zapier Interfaces, Zapier Agents, Zapier Canvas, Sub-Zaps, Zapier Platform API. No external stack.

## Working Example: Evidence Bundle for One Zap Run

```
record_id: 00123
zap_id: zap-onboard-finance
timestamp: 2026-07-01T10:12:00Z
actor: hr-system
target: jdoe@company.com
event_type: user_provisioned
control_ids: [3.1.1, 3.5.1, CC6.2, 164.312(a)(1)]
payload_hash: a8f9c4d1e...
ai_narrative: "User provisioning event demonstrates practice 3.1.1 by restricting system access to authorized users only through role-based group assignment."
review_status: approved
```

## How to Run It

1. Clone the repo. `/tables` contains five ready-to-import CSV files with schema and example data. `/exports` contains six Zap blueprints (build specs, not native Zapier import files, since Zapier doesn't support file-based Zap import).
2. Create the five Tables in your Zapier workspace: Zap Inventory, Audit Ledger, Control Map, Failure Signals, Repair Proposals. Import each corresponding CSV from `/tables`.
3. Rebuild each of the six Zaps from the blueprints in `/exports`, in order. Blueprints 1 and 3 each carry an unverified assumption about Platform API capabilities, flagged in `/exports/README.md`; check those before building the rest.
4. Once a Zap is built and working, turn it into a real Zapier Template (Zap editor menu, Create Template) and swap the corresponding blueprint link in this README for the live template link.
5. Build the Interfaces pages for the Fabric Map dependency graph and the Repair Proposals approval queue.
6. Enable the Zapier Agent step in Blueprint 3.

## Demo

- Practitioner Loom (3 min): [link]
- Executive Loom (60 sec): [link]
- MCP Bridge spec (v2 preview): [docs/mcp-bridge-spec.md](docs/mcp-bridge-spec.md)
- Architecture: [docs/architecture.md](docs/architecture.md)

## Why This Pattern Matters

- Replaces manual audit evidence collection.
- Real-time compliance traceability.
- Keeps AI in a controlled, reviewable role.
- Aligns automation with governance, not just efficiency.
- Turns automation platforms into first-class enterprise infrastructure.

## Cost Model (500-User Scale, Indicative)

Numbers to be confirmed against live Zapier pricing before publication.

## License

MIT — see [LICENSE](LICENSE).

## Author

Bradley Baker | bbaker@bradbaker-it.com
