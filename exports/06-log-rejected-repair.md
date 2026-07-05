# Blueprint: Log Rejected Repair

**Purpose**: when a human rejects a proposed fix, keep the reasoning as training data instead of letting it disappear.

**Trigger**: Zapier Tables, Updated Record in `Repair Proposals`, where `status` changes to `rejected`.

## Steps

1. **Trigger**: Zapier Tables, Updated Record (`Repair Proposals`, filtered to `status = rejected`).
2. **Action**: Zapier Tables, Create Record. Append a new row to `Failure Signals` carrying the original signal plus the rejection rationale. Requires adding an `is_training_row` boolean field to the Failure Signals schema if not already present; note this as a schema addition when building.
3. **Action**: Zapier Tables, Update Record. Set `reviewer` and `reviewed_at` on the `Repair Proposals` row.

## Tables Read/Written

Reads and writes `Repair Proposals`, writes `Failure Signals` — see `/tables/repair_proposals.csv` and `/tables/failure_signals.csv`.
