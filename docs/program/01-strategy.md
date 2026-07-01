# Ward design strategy

> Status: DRAFT v0.

## Thesis

Ward is Ken's temporal/modal complement — **one logic, two engines**
(spec/00-overview). The campaign turns Ken's fixed seam (the assumption-boundary
export) into a coherent Ward design: the translation, the three engines, and the
two artifacts Ken defers to Ward (the discharge attestation, the runtime CT
validation).

## Fixed input vs open design

- **Fixed — do not re-litigate:** the Ken seam — the five-field export IR, its
  invariants I1–I5, content-addressing, and the one-way flow (spec/10-seam; ken
  spec/70-behavioral). Ward *consumes* it. Where Ward needs a change, that is a
  **coordination request back to Ken**, not a local edit.
- **Open — the campaign's work:** everything downstream — the `WardFormula`
  target + `compile` image; the model/monitor targets; L1/L2/L3; the sampling
  policy; the discharge attestation + runtime CT; and Ward's own implementation
  stack.

## Success criteria

- **G1 — Faithful seam.** Ward parses + validates the export against I1–I5, and
  the `compile` faithfulness lemma is statable (`WardFormula` defined).
- **G2 — One logic honored.** A property means the same thing on both sides; the
  one-way gate is never violated (no Ward result promoted to a Ken `proved`).
- **G3 — Two Ken-deferred artifacts designed.** The discharge attestation
  (`OQ-discharge-attestation`) and the runtime CT validation, with their
  Ken-visible fields coordinated back through ken `63 §5a`.
- **G4 — Three engines specified.** L1 model-checking, L2 test-gen + sampling
  policy, L3 RV / trace-conformance / agentic envelope — each with its discharge
  contribution.
- **G5 — Stack decided.** `OQ-ward-stack` resolved: Ward's implementation
  architecture chosen, with rationale.

## Workstreams

- **WS-Seam** — the Ken interface (consume + the two deferred artifacts).
- **WS-Translate** — `τ` + faithfulness.
- **WS-Engines** — L1 / L2 / L3.
- **WS-Stack** — Ward's architecture + implementation plan.
