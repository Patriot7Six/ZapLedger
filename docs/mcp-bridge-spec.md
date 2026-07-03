# MCP Bridge for Zapier: Public Specification (v0.1)

Author: Bradley Baker
Date: June 30, 2026

*Status: Public spec. Not shipped in Tier B. Preview for ZapLedger v2.*

## Purpose

Expose any governed Zap as a Model Context Protocol (MCP) tool consumable by Claude, ChatGPT, and Copilot. Every invocation writes an evidence bundle to the ZapLedger Audit Ledger. External AI agents can now invoke governed enterprise automations as tools with cryptographic audit evidence produced live.

## Why This Matters

- MCP is emerging as the standard interface between AI agents and external tools.
- Enterprise Zapier customers will want to expose Zaps to AI agents. They will not want to do it without governance, audit, and human-in-the-loop review.
- Zapier is the natural fabric for this. ZapLedger is the natural governance layer on top of it.

## Design Goals

- Any Zap flagged as governed is automatically MCP-exposable.
- Every tool invocation produces a signed evidence bundle.
- Human approval can be inserted between AI call and execution (async approval flow).
- No sensitive data leaves the Zapier tenant boundary without explicit governance approval.

## MCP-Side Surface

- **List tools**: returns all governed Zaps with metadata (name, description, input schema derived from trigger fields, expected output).
- **Call tool**: takes tool name and arguments. Returns either an execution result (sync mode) or a pending_approval token (async mode).
- **Get approval status**: takes pending_approval token. Returns approved | rejected | pending.

## Zapier-Side Surface

- **Bridge Zap**: MCP server (custom Zapier integration or webhook-triggered Zap).
- **Input validation Sub-Zap**: schema-check inputs against the exposed tool contract.
- **Governance gate Sub-Zap**: check the Zap's governance_state in Zap Inventory. If not governed, refuse. If governed with async_approval, insert a Repair Proposals-style approval row and return the pending_approval token. If governed with sync_execution, proceed.
- **Evidence Sub-Zap**: emit an evidence bundle with actor = AI agent identifier, tool = zap_id, invocation payload hash, and control_ids from Control Map.

## Security Posture

- MCP client identity is enforced via a per-tenant API key issued by ZapLedger.
- Every invocation writes actor identity, timestamp, payload hash to the ledger.
- Tenant admins can quarantine any Zap from MCP exposure without editing the Zap itself (Filter kill switch pattern).

## Open Questions (Flagged for the Community and Zapier)

- Streaming outputs from long-running Zaps: MCP supports it. Zapier's polling model may need patterns.
- Tool discovery latency vs. cost: cache tool list per tenant with TTL.
- Rate limiting per AI agent identity separate from per Zapier account limits.

## Reference Implementation Status

This document is the spec. Reference implementation planned for ZapLedger v2. Feedback welcome via GitHub issues on Patriot7Six/ZapLedger.

*MCP spec version this doc is designed against: 2026-Q2 draft. Timestamp explicit because the spec is moving.*
