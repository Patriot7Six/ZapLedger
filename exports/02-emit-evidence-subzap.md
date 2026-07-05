# Blueprint: Emit Evidence (Sub-Zap)

**Purpose**: turn any governed Zap's run into a signed, control-mapped evidence bundle. Called by other Zaps, not run on its own.

**Trigger**: Sub-Zap trigger. The parent Zap passes `zap_id`, `actor`, `target`, `event_type`, and the run's payload.

## Steps

1. **Trigger**: Sub-Zap, invoked by a parent Zap at the end of its run.
2. **Action**: Formatter by Zapier, Utilities, Hash. Compute a SHA-256 digest of the payload.
3. **Action**: Zapier Tables, Find Record. Look up the `Control Map` table filtered by `event_type` to get the applicable `control_ids`.
4. **Action**: Zapier Tables, Create Record. Insert into `Audit Ledger`: `record_id` (generated), `zap_id`, `timestamp` (UTC), `actor`, `target`, `event_type`, `control_ids`, `payload_hash`, `ai_narrative` (short AI-drafted description of what the event demonstrates, tagged pending review), `review_status` set to `pending`.
5. **Action**: Webhooks by Zapier, Custom Request (POST). Mirror the record to an external immutable store (SharePoint list or Airtable base configured for append-only access).
6. **Action**: Slack, Send Channel Message. Post a summary to `#compliance` with a link back to the new Audit Ledger row.

## Tables Read/Written

Reads `Control Map`, writes `Audit Ledger` — see `/tables/control_map.csv` and `/tables/audit_ledger.csv`.
