# 50 — L3: runtime verification — MIGRATED TO KEEP

> Status: **MIGRATED (2026-07-02).** L3 runtime verification is **Keep's**, the
> runtime sibling (Ward ADR 0002 → Keep ADR 0001). Its normative spec now lives
> in `ken-topos/keep` `spec/50-runtime-verification/`. This pointer remains so
> Ward's §5x cross-references resolve and the family's decade numbering stays
> intact.

## Why this is a pointer

Ward owns the **offline** engines (§30 L1, §40 L2) and emits the attestation;
Keep owns the **runtime** engine (§50) and contributes runtime verdicts. Ward
ADR 0002 recorded that §50, §53, and the runtime face of §13 were "Keep's
territory, to be migrated"; Keep ADR 0001 completed that migration. The chapters
below now live in the Keep repository:

- **§51 monitor synthesis** — monitors from `T` (LTL → Büchi, MOP-style) over
  `Σ`, and the instrumentation contract (ken `73`). → keep `spec/50/51`.
- **§52 trace conformance** — does the running implementation refine Ward's L1
  model (§22/§30)? The runtime half of faithfulness (§23, part 4). → keep
  `spec/50/52`.
- **§53 agentic envelope** — the embedded agent as a maximally-nondeterministic
  `P` inside a verified FSM; safety-not-quality; `OQ-agentic-oracle`. → keep
  `spec/50/53`.
- **§54 runtime timing** — the runtime face of the CT method (§13); the offline
  binary-level verifier stays here in §13. → keep `spec/50/54`.

## What stays in Ward

Ward keeps the **shared seam** these chapters consume (§10, §20), the **L1
model** §52 checks against (§30), the **metamorphic surface** §53 reuses (§43),
the **discharge attestation** every runtime verdict contributes to (§12), and
the **offline** CT verifier + the shared CT method (§13). References to §51–§54
elsewhere in this spec point at Keep's chapters under the shared numbering.
