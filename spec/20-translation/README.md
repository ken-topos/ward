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

`τ` splits along the two things Ward translates — the *properties* and the
*system they range over* — and closes with the argument that binds them:

- [`21-property-translation.md`](21-property-translation.md) —
  `compile : Temporal Σ → WardFormula`: the `WardFormula` target Ward defines,
  the `compile` image, and the semantics-preservation lemma Ken proves over it.
  `OQ-wardformula`.
- [`22-model-translation.md`](22-model-translation.md) — the transition system
  Ward explores, **generated** from the space semantics and the `Σ`
  perform-nodes so authoring drift is impossible; the model target (Quint module
  / Apalache TLA+ / an IR). `OQ-model-target`.
- [`23-faithfulness.md`](23-faithfulness.md) — how the two translations compose
  into the seam's faithfulness argument, the intuitionistic↔classical bridge
  discipline Ward realizes, the version pin that anchors it (§12), and the
  honest residual gap that flows to conformance (§50).
