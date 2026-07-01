# 20 — Translation (`τ`) and its faithfulness

> Status: DRAFT v0 — open design. Ken pins the *obligations* and proves the
> property half faithful **once, at the compiler level** (ken
> `spec/70-behavioral/71 §5`, `72 §6.3`, `spec/20-verification/23 §4`;
> `OQ-classical-bridge`, DECIDED in ken); Ward designs the **targets** those
> obligations translate into and realizes the bridge discipline here.

Ward's engines consume model-checker input and monitor formulas, not Ken's
datatypes, so a translation `τ` mediates the seam (§10) and the engines
(§30–§50). An **unfaithful** `τ` — a green check on a spec that does not match
the code — is worse than none: it launders a translation bug into a false
assurance, the one failure mode the whole project exists to prevent. So `τ` is
not a convenience layer; it is where the single-logic guarantee is either kept
or lost, and its faithfulness is a **first-class deliverable**, not an assumed
property.

`τ` is **two layers**, on both the property and the system side (DESIGN SET,
2026-07-01): an internal **ideal** language holding Ken's full expressiveness,
which Ken proves faithful **losslessly**; and **lossy, classified projections**
down to whatever concrete (replaceable, §01) checker is mounted. Faithfulness
lives on the lossless compile; fidelity lives on the projections.

- [`21-property-translation.md`](21-property-translation.md) —
  **`WardFormula`**, the ideal μ-calculus-over-`Σ` IR;
  `compile : Temporal Σ → WardFormula` (lossless, Ken's one lemma); the
  **projection interface + fidelity classification** (exact / sound over- or
  under-approx / not-projectable) that routes the μ-calculus gap honestly.
  `OQ-wardformula`.
- [`22-model-translation.md`](22-model-translation.md) — **`WardModel`**, the
  faithful transition system **generated** from `Σ` + the space semantics (no
  authoring drift); projections to the checker's model language where
  finite-state **abstraction** lives, classified the same way.
  `OQ-model-target`.
- [`23-faithfulness.md`](23-faithfulness.md) — how the parts compose (now
  **five**: compile faithfulness, no drift, **projection soundness**, trace
  conformance, the one honest assumption), the intuitionistic↔classical bridge,
  the version pin (§12), and the honest residual gap that flows to §50.
