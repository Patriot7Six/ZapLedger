# ZapLedger: Architecture (Tier B)

## Overview

A meta-platform built entirely on Zapier for governance, observability, and compliance evidence across an enterprise Zap estate. Tier B ships three components: Zap Fabric Map, Control Attestation Engine, and Autonomous Repair Agent.

## Component 1: Zap Fabric Map

Purpose: give enterprise CS and compliance teams a live dependency graph of their Zap estate.

**Data flow:**

1. Trigger: Schedule (hourly) or manual refresh from an Interface button.
2. Zapier Platform API call: list Zaps in the workspace. Pull trigger, actions, apps.
3. Formatter: parse and normalize. Compute blast radius score (weighted sum of: # of actions, PII apps touched, business-critical apps touched, downstream Zaps).
4. Zapier Tables: upsert to Zap Inventory table. Fields: zap_id, name, owner, trigger_app, action_apps, pii_touch (bool), blast_radius, last_run_status.
5. Interface page: render Zap Inventory as a filterable list plus a dependency graph rendered as embedded HTML/SVG from a stored template.

Fields on Zap Inventory table: zap_id, zap_name, owner_email, team, trigger_app, action_apps (array), pii_touch, blast_radius (0-100), last_run_at, last_run_status, governance_state (governed | ungoverned | quarantined).

## Component 2: Control Attestation Engine

Purpose: turn every governed Zap run into a signed, control-mapped evidence bundle.

**Data flow:**

6. Trigger: any governed Zap fires a Sub-Zap called Emit Evidence at end of run.
7. Formatter: build the evidence bundle. SHA-256 of the payload digest. UTC timestamp. Actor (user or system). Target (user, resource, or record). Control_id lookup from Control Map table.
8. Zapier Tables: insert to Audit Ledger with fields: record_id, zap_id, timestamp, actor, target, event_type, control_ids (array), payload_hash, ai_narrative, review_status.
9. External mirror: POST to SharePoint or Airtable Immutable Audit Store.
10. Slack: post summary to #compliance with a link back to the ledger row.

**Control Map coverage:**

- NIST 800-171: 3.1.1, 3.1.5, 3.1.6, 3.5.1, 3.9.2, 3.14.6
- SOC 2: CC6.1, CC6.2, CC7.2, CC7.3, CC8.1
- HIPAA: 164.312(a)(1), 164.312(b), 164.312(c)(1)
- CMMC 2.0: AC.L2-3.1.1, AC.L2-3.1.5, PS.L2-3.9.2, AU.L2-3.3.1

## Component 3: Autonomous Repair Agent

Purpose: detect Zap failures, diagnose them, propose a fix, wait for human approval.

**Data flow:**

11. Trigger: Zapier task failure event (subscription).
12. Zapier Agent step: prompt = "You are a Zapier reliability engineer. Given this failure payload and Zap definition, classify the root cause into one of: bad_auth, schema_drift, rate_limit, downstream_5xx, logic_error, data_shape. Return category, confidence (0-1), suggested_action, and a one-line rationale."
13. Zapier Tables: insert to Failure Signals table. Fields: signal_id, zap_id, failure_ts, category, confidence, suggested_action, rationale.
14. Formatter: if confidence >= 0.7, generate a Repair Proposal. Otherwise route to human triage.
15. Zapier Tables: insert to Repair Proposals table. Fields: proposal_id, zap_id, action_summary, action_payload (JSON), status (pending|approved|rejected), reviewer, reviewed_at.
16. Interface page: Repair Proposals queue with Approve and Reject buttons.
17. On Approve: fire a Sub-Zap that applies the fix and writes evidence to the Audit Ledger (fix events are also evidence).
18. On Reject: append the rejection reason to Failure Signals as a training row.

## Governance Layer

- Every path has a top-level Filter kill switch that can be flipped without deleting the Zap.
- Every AI output is flagged as "AI-drafted, pending human approval" until reviewed.
- Evidence bundles get integrity verification on read (recompute SHA-256, compare).
- No silent operations. Every action produces a ledger entry.

## Data Model Summary

- Zap Inventory (Fabric Map)
- Audit Ledger (Attestation Engine)
- Control Map (Attestation Engine)
- Failure Signals (Repair Agent)
- Repair Proposals (Repair Agent)

## Scaling Notes

- Zapier Tables ceiling: monitor row counts. Production pattern documented: mirror to external data warehouse via scheduled Sub-Zap.
- Ledger pattern is SIEM-ingestion-ready.
- Fabric Map graph renders up to ~500 Zaps in-Interface. Beyond that, export to Graphviz or Neo4j (noted as v2 work).

## Design Principles

- Evidence is generated, not collected.
- Automation must be auditable.
- AI assists, never finalizes.
- Every event ties to a control.
- Every failure produces a signal, not just a page.
