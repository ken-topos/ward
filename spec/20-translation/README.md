# 20 — Translation (`τ`) and its faithfulness

> Status: DRAFT v0 — open design. Ken pins the *obligations* (ken `72 §3`,
> `71 §5`); Ward designs the translations, and Ken proves the property half
> faithful.

Ward consumes model-checker input, not Ken's datatypes, so a translation `τ`
mediates. An *unfaithful* `τ` — a green check on a spec that does not match the
code — is worse than none. `τ` splits in two:

- **Property translation** `compile : Temporal Σ → WardFormula`. Both sides are
  syntax over the same alphabet `Σ`, so Ken proves `compile` **semantics-
  preserving once, at the compiler level** (`⟦φ⟧ = ⟦compile φ⟧` over `Σ`-traces,
  a structural induction) — amortized to zero per obligation. Ward defines
  `WardFormula` (its target — Quint / Apalache / LTL) so this lemma is
  *statable*.
- **Model translation** — the transition system Ward explores corresponds to the
  program's denoted behaviors. The model is **generated** from the code (`Σ` *is*
  the perform-node signatures; the state machine derives from the space
  semantics), so authoring drift is impossible by construction. The residual
  concrete-vs-abstract gap is the **conformance** story (§50) plus an honest
  assumption — not a single Ward proof.

**Ward design deliverables:** the `WardFormula` target and the `compile` image;
the model-translation target (Quint module / Apalache TLA+ / IR); the
intuitionistic↔classical bridge discipline (`OQ-classical-bridge`, decided in
Ken — realize it here); and the version pin that anchors faithfulness (§12).
