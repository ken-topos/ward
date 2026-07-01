# 23 — Faithfulness and the intuitionistic↔classical bridge

> Status: DRAFT v0 — open design. `OQ-classical-bridge` is **DECIDED in Ken**
> (strictly one-way; Ward results never `proved`; sound by assume-guarantee;
> faithfulness Ken-checked once at the compiler level + generated-model +
> conformance + one honest assumption; trust edge pinned as the Ward version in
> the discharge attestation — ken `71 §5`, `20-verification/23`). This chapter
> **realizes** that decision on Ward's side; it does not reopen it.

## What it is

§21 translates the *properties*; §22 translates the *system*. This chapter is
the argument that the two together are **faithful** — that a green Ward result
about `compile φ` over the generated model is genuine evidence about `φ` over
the real program — and the discipline that keeps that argument honest.
Faithfulness is not one Ward proof; it is a **composition** of five parts, four
structural and one explicitly assumed (ken `OQ-classical-bridge`). The two-layer
translation (§21/§22) splits the old "property faithfulness" into a **lossless**
half Ken proves and a **lossy** half Ward declares:

1. **Compile faithfulness (lossless)** — `⟦φ⟧ = ⟦compile φ⟧` over `Σ`-behaviors,
   `compile : Temporal Σ → WardFormula` into the ideal, proved by Ken once at
   the compiler level (§21; ken `72 §6.3`).
2. **No authoring drift** — `WardModel` is generated, not written, so the ideal
   model cannot silently disagree with the code (§22; ken `71 §1`).
3. **Projection soundness (lossy, Ward-declared)** — each projection `π` from
   the ideal (`WardFormula`/`WardModel`) to a concrete checker carries a
   **declared fidelity** (exact / sound over- or under-approx), and Ward
   discharges the obligation that a verdict over `π` soundly reflects the ideal;
   a `not projectable` obligation is routed, never silently lost (§21/§22). This
   is the new component the ideal-plus-projections design adds.
4. **Trace conformance** — the *running* code stays within `WardModel`
   (implementation refines model), checked at runtime (§52; ken `73 §1`).
5. **The one honest assumption** — that **Ward implements** the axiomatized
   semantics Ken's lemma is stated *relative to*. This is the single explicit,
   version-bounded trust edge, pinned — as the Ward version — in the discharge
   attestation (§12; ken `OQ-classical-bridge`, `OQ-discharge-attestation`).

## The bridge discipline Ward must honor

Ken's logic is intuitionistic; Ward's engines are classical (a model-checker
decides `φ ∨ ¬φ`, a monitor observes a concrete trace). The bridge across that
gap is **strictly one-way and never promotes** — this is the seam's soundness
core (§00), and Ward is the side that must not violate it:

- **Results never re-enter Ken as proof terms.** A depth-`k` model-check is not
  a proof for all `N`; a green monitor is not a proof. A discharged obligation
  stays `delegated`/`tested` and re-enters Ken only as an attestation record,
  never a kernel certificate (§11 I4; ken `71 §5.1`, `72 §5`).
- **Classical and intuitionistic truth coincide only where decidable/finite.**
  Where an obligation is finite-state or decidable, a classical verdict is also
  intuitionistically valid and Ward may state it plainly. Where it is unbounded
  (liveness over all `N`, §33), the classical result is **honest evidence under
  a stated bound**, not truth-for-all-`N` — and must be recorded as such (§12).
- **Soundness is by assume-guarantee, not by trusting Ward's engines.** Ken
  certifies `Q` **given** `P`; that conditional is kernel-checked regardless of
  how Ward later realizes `P`/`T` (ken `74 §2`). Ward's job is to discharge the
  antecedents classically and honestly, not to strengthen the consequent.

## Ward design deliverables

- **The composition argument, written down.** State parts 1–4 as Ward's
  faithfulness story for a given discharge: which parts are structural, which is
  assumed, and exactly what the assumption is. This is the prose the discharge
  attestation (§12) points to when it claims "this discharge is faithful."
- **The version-pin mechanism.** Faithfulness is *relative to* a `compile`/
  `WardFormula` (§21) and a model generator (§22); the attestation must pin the
  Ward version so a consumer knows *which* axiomatized semantics the Ken lemma
  was discharged against. Design the pin's content (§12) and its granularity.
- **The residual-gap ledger.** Enumerate what faithfulness does **not** cover —
  the concrete-vs-abstract abstraction gap (§22), the depth bound (§33), the
  conformance assumption (§52) — so the honest boundary is a written artifact,
  not a gap a reader has to infer. This ledger is Ward's central discipline
  (`docs/PRINCIPLES.md`, honesty-about-the-boundary) made concrete.

## What is NOT open here

The bridge's *direction and soundness* are Ken's decision
(`OQ-classical-bridge`, DECIDED); Ward does not get to promote, re-import
classical strength into Ken, or weaken the one-way gate. Ward realizes the
discipline and designs the artifacts that record it. Resolver: `OQ-wardformula`
and `OQ-model-target` (the translations this argument is stated over), plus
`OQ-discharge-attestation` (where the version pin and gap ledger land).
