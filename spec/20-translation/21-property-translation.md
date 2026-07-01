# 21 — Property translation: `compile : Temporal Σ → WardFormula`

> Status: DRAFT v0 — open design. Ken pins the **source** (`Temporal Σ`, an
> inert LTL/μ-calculus datatype over `Σ`, ken `72 §3`) and the **lemma**
> (semantics preservation, proved once at the compiler level, ken `72 §6.3`,
> `71 §5`); Ward designs the **target** `WardFormula` and the `compile` image.
> Resolver: `OQ-wardformula`.

## What it is

The exported obligations `T` are `Temporal Σ` values — a deeply-embedded
LTL/μ-calculus over the event alphabet `Σ` (ken `72 §3`:
`atom`/`not`/`and`/`or`/ `next`/`until` + `mu`/`nu`, with `◇`/`□`/`leadsto`
derived from `until`). Ward cannot model-check or monitor that datatype
directly; it must land in a concrete formula language its engines accept — LTL
for a Büchi monitor (§51), a Quint/TLA+ temporal property for L1 (§30).
`compile : Temporal Σ → WardFormula` is that map, and `WardFormula` is the
target Ward **defines** so the map is statable.

Ken proves `compile` **semantics-preserving once, at the compiler level** —
`⟦φ⟧ = ⟦compile φ⟧` over `Σ`-behaviors, a structural induction, the exact analog
of the prover's Kripke-adequacy lemma (ken `72 §6.3`, `20-verification/23 §4`).
The lemma is amortized to zero per obligation: every delegated property reuses
the one proof. But the lemma is only **statable** once Ward fixes `WardFormula`
and gives `⟦·⟧` on it — which is why the target is a Ward deliverable that Ken's
proof *waits on* (ken `72 §6.3`, `(oracle)`-tagged there).

## The two `compile` projections do not share a function

There are **two** structural maps out of `Temporal Σ`, and Ward must keep them
distinct (ken `72 §6.3`):

- **This one — the L1/faithfulness projection:**
  `compile : Temporal Σ → WardFormula`, the model-checker's property language,
  over which Ken states the faithfulness lemma.
- **The monitor projection (§51):** `compile : Temporal Σ → Monitor`, the
  runtime synthesis (LTL→Büchi/MOP). Same source, same `Σ`, **different
  codomain**.

They share the `Temporal Σ` source and the `Σ` alphabet, not an implementation.
Conflating them is the trap ken flags; §20 owns the first, §51 owns the second,
and §23 argues they cohere.

## Ward design deliverables

- **`WardFormula` — the target syntax + its semantics.** A concrete formula
  language faithful to the pinned `Temporal Σ` value-set (the LTL core, the
  `mu`/`nu` fixpoints, `◇`/`□`/`leadsto`). It must denote **these operators with
  these meanings** (ken `72 §3.1`, the conformance-checkable core), give `⟦·⟧`
  over `Σ`-behaviors so the lemma is statable, and settle the semantic questions
  ken `(oracle)`-tagged: **finite vs ω-behaviors**, and the **μ/ν fixpoint
  interpretation** (ken `72 §6.2`). One `WardFormula` may serve L1 and the
  monitor, or they may diverge — decide and record.
- **The `compile` image + the atom language.** How `Pred Σ` atoms (state/event
  predicates over `Σ`, spelling deferred by ken `72 §3.1`) render into
  `WardFormula` atoms, and how first-order `mu`/`nu` binding (load-bearing for
  strict positivity, ken `72 §3.1`) survives translation.
- **Coordinate the source spelling back to Ken.** The `Temporal` constructor
  spelling, the `Pred Σ` atom language, the fixpoint-variable representation,
  and the ITF-facing serialization are the **joint Ward encoding pass** ken
  defers (`72 §3.1`, `§6.3`); finalize them against Ward's parser and feed them
  back into ken conformance (mirrors §11's wire-spelling obligation).
- **State the lemma's shape for Ken to discharge.** Ward does not prove the
  faithfulness lemma (Ken does), but it fixes everything the lemma quantifies
  over. Deliver the precise statement of `⟦φ⟧ = ⟦compile φ⟧` — the behavior
  model, the equality, the induction's structure — so Ken can carry it.

## Relationship to the rest

The compiled `WardFormula` is what L1 (§30) checks and the monitor projection
(§51) synthesizes from; the version of `compile`/`WardFormula` used is pinned in
the discharge attestation (§12), because faithfulness is *relative to* this
translation. Resolver: `OQ-wardformula` (§90); coordinate the source-spelling
half through ken `72`.
