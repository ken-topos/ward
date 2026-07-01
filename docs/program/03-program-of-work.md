# Ward program of work

> Status: DRAFT v0. The work-package catalog. Each WP is a self-contained,
> assignable design unit; one file per WP under `wp/`, mapped 1:1 to a git branch
> `wp/<ID>-<slug>` and a mootup thread. WP-ID letters encode the workstream:
> `S*` seam, `Tr*` translation, `E*` engines, `A*` architecture/stack.

| WP | Title | WS | Feeds |
|---|---|---|---|
| S0 | Ratify the export-consumer contract (§11) | Seam | Tr1, E1 |
| S1 | Design the discharge attestation (§12) | Seam | E1, E2, E3 |
| S2 | Design the runtime CT validation (§13) | Seam | S1 |
| Tr1 | `WardFormula` + `compile` image (§20) | Translate | E1 |
| Tr2 | Model-translation target (§20) | Translate | E1 |
| E1 | L1 model-checking (§30) | Engines | S1 |
| E2 | L2 test-gen + sampling policy (§40) | Engines | S1 |
| E3 | L3 RV / trace-conformance / agentic (§50) | Engines | S1 |
| A0 | `OQ-ward-stack` — architecture + stack (§00) | Stack | all |

Each `wp/<ID>-*.md` opens with a Steward frame (Owner / Size / Risk / Branch /
Feeds), then Scope, deliverables, acceptance, guardrails — see `wp/README.md`.
