# Blueprint: Route Low-Confidence Failures to Human Triage

**Purpose**: get a human in front of any failure signal the Repair Agent wasn't confident enough to auto-propose a fix for.

**Trigger**: Zapier Tables, New Record in `Failure Signals`.

## Steps

1. **Trigger**: Zapier Tables, New Record (`Failure Signals`).
2. **Action**: Filter by Zapier. Continue only if `confidence < 0.7`.
3. **Action**: Slack, Send Direct Message. Notify the on-call engineer with the Zap name, category, rationale, and a link to the Failure Signals row.

## Table Read From

`Failure Signals` — see `/tables/failure_signals.csv`.
