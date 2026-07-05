# Blueprint: Apply Approved Repair (Sub-Zap)

**Purpose**: execute a repair once a human has approved it in the Interface queue, and log that fix as evidence in its own right.

**Trigger**: Zapier Tables, Updated Record in `Repair Proposals`, where `status` changes to `approved`. This update is fired by the Approve button on the Repair Proposals Interface page.

## Steps

1. **Trigger**: Zapier Tables, Updated Record (`Repair Proposals`, filtered to `status = approved`).
2. **Action**: varies by `action_payload.action`. Common cases: Webhooks by Zapier Custom Request to refresh an OAuth connection, or an app-specific action matching the payload (for example, adding a delay step's configuration via the Platform API). This step is necessarily specific per repair type; treat the payload's `action` field as a dispatch key.
3. **Action**: Sub-Zap call to Blueprint 2 (Emit Evidence), passing `action_summary` as the event payload, so the fix itself produces an audit trail entry.
4. **Action**: Zapier Tables, Update Record. Set `reviewer` and `reviewed_at` on the `Repair Proposals` row.

## Table Written To

`Repair Proposals` — see `/tables/repair_proposals.csv`.
