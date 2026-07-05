# Blueprint: Fabric Map Inventory Sync

**Purpose**: keep the Zap Inventory table current with every Zap in the workspace and its computed blast radius.

**Trigger**: Schedule by Zapier, every hour. (Alternative: a webhook fired from an Interface "Refresh" button for on-demand runs.)

## Steps

1. **Trigger**: Schedule by Zapier, Every Hour.
2. **Action**: Webhooks by Zapier, Custom Request (GET), calling the Zapier Platform API to list Zaps in the workspace, including trigger app, action apps, and step count per Zap. Verify the exact endpoint and required scope against current Zapier Platform API docs before building; this is unconfirmed.
3. **Action**: Formatter by Zapier, Utilities, or a Zapier Agent step. Parse the API response and compute `blast_radius` (0-100) as a weighted sum of: action step count, whether any action app touches PII, whether any action app is flagged business-critical, and the count of downstream Zaps (Zaps triggered by this Zap's Sub-Zap calls or Table writes).
4. **Action**: Zapier Tables, Update Record (with Create Record fallback for new Zaps). Upsert into the Zap Inventory table using `zap_id` as the match key. Fields written: `zap_name`, `owner_email`, `team`, `trigger_app`, `action_apps`, `pii_touch`, `blast_radius`, `last_run_at`, `last_run_status`.

## Not a Zap Step

The dependency-graph rendering itself lives in a Zapier Interfaces page reading from the Zap Inventory table, not in this Zap.

## Table Written To

`Zap Inventory` — see `/tables/zap_inventory.csv` for schema and seed data.
