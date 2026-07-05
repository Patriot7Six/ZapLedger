# Blueprint: Failure Detection and Diagnosis

**Purpose**: catch a Zap failure, classify the root cause with an AI agent, and open a repair proposal when confidence is high enough.

**Trigger**: Zapier task failure event (subscription). **Unverified**: confirm this exists as a clean, workspace-wide subscribable trigger in the current Zapier Platform API before building. If it doesn't, the fallback is a scheduled poll of Zap run history via the Platform API, which changes the trigger step below but not the rest of the chain.

## Steps

1. **Trigger**: Zap failure event (webhook subscription, or polling fallback per the note above).
2. **Action**: Zapier Agent step. Prompt: "You are a Zapier reliability engineer. Given this failure payload and Zap definition, classify the root cause into one of: bad_auth, schema_drift, rate_limit, downstream_5xx, logic_error, data_shape. Return category, confidence (0-1), suggested_action, and a one-line rationale." Output tagged as AI-drafted, pending human approval until a proposal is reviewed.
3. **Action**: Zapier Tables, Create Record. Insert into `Failure Signals`: `signal_id`, `zap_id`, `failure_ts`, `category`, `confidence`, `suggested_action`, `rationale`.
4. **Action**: Filter by Zapier. Continue only if `confidence >= 0.7`.
5. **Action**: Zapier Tables, Create Record. Insert into `Repair Proposals`: `proposal_id`, `zap_id`, `action_summary`, `action_payload` (JSON), `status` set to `pending`.

## Where Low-Confidence Signals Go

Signals below 0.7 confidence stop at step 3 and are picked up by Blueprint 4 for human triage instead of an auto-generated proposal.

## Tables Written To

`Failure Signals`, `Repair Proposals` — see `/tables/failure_signals.csv` and `/tables/repair_proposals.csv`.
