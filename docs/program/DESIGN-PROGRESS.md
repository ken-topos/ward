# Ward design campaign — progress (living)

> Status: living backbone, Steward-owned. Survives context compaction. The
> build-side analog of `spec/SPEC-PROGRESS.md`: the WP/gate state tracker.

## Where we are

**Phase 0 — Anchor.** Repo scaffolded (initial commit): governance, the fixed
seam (spec/10-seam, anchored to ken B1), the engine section skeletons, the
campaign plan, and the agent federation. Fleet **not yet provisioned**
(moot.toml `space_id` + secrets + `.moot/actors.json` pending). Next: ratify the
seam, open `OQ-ward-stack`.

## Gate status

| Phase | Gate | State |
|---|---|---|
| 0 Anchor | seam agreed, no unflagged divergence from ken | OPEN |
| 1 Translation | `compile` lemma statable | — |
| 2 Engines | L1/L2/L3 normative | — |
| 3 Deferred | discharge attestation resolved | — |
| 4 Stack | `OQ-ward-stack` resolved, freeze | — |

## Work packages

Catalog: `03-program-of-work.md`. Units: `wp/`. None started.

## Open decisions

Tracked in `spec/90-open-decisions.md`: inherited `OQ-discharge-attestation`,
`OQ-sampling-policy`; Ward-internal `OQ-ward-stack`, `OQ-wardformula`,
`OQ-model-target`, `OQ-export-wire`.
