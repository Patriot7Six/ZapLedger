# Zap Blueprints

Zapier doesn't support a native file-based export/import for Zaps. The only built-in sharing mechanism is a Zap Template: a live link generated from a Zap you've already built in a real account with connected apps, which someone else then copies and reconfigures with their own credentials. Zaps using Code by Zapier, Paths, or private/legacy apps can't be shared as templates at all.

So these six files aren't importable Zapier files. They're build specs: exact enough that anyone (you, a Zapier employee evaluating this, another developer) can reconstruct each Zap by hand in the Zap editor. Once a blueprint is actually built in a live Zapier workspace, it can be turned into a real Zap Template and shared as a link. That link should replace the corresponding blueprint reference in the main README when it exists.

## The Six

1. `01-fabric-map-inventory-sync.md`
2. `02-emit-evidence-subzap.md`
3. `03-failure-detection-diagnosis.md`
4. `04-route-low-confidence-to-human.md`
5. `05-apply-approved-repair-subzap.md`
6. `06-log-rejected-repair.md`

## Before Building

Two things in these blueprints are unverified against Zapier's current API surface and should be checked first, not last:

- **Blueprint 1, Step 2**: assumes the Zapier Platform API can list all Zaps in a workspace. Confirm the exact endpoint and required scopes against current Zapier Platform API docs before building.
- **Blueprint 3, Step 1**: assumes Zapier supports a subscribable "Zap failure" event across a workspace. This is the single least-certain piece of the whole architecture. If it doesn't exist as a clean workspace-wide subscription, the fallback is polling Zap history via the Platform API on a schedule, which changes Blueprint 3's trigger and timing.
