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

1. Clone the repo and open `/exports`.
2. Import the six Zap templates into your Zapier workspace.
3. Create the five Tables: Zap Inventory, Audit Ledger, Control Map, Failure Signals, Repair Proposals. Seed data in `/tables`.
4. Configure the Zapier Platform API connection for Fabric Map.
5. Enable the Zapier Agent step in Repair Agent.
6. Open the Interfaces and start exploring.

> Note: `/exports` and `/tables` aren't populated yet. This repo currently ships the architecture and spec; the importable templates are next.

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
