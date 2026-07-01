# 21 — Property translation: `WardFormula` and its projections

> Status: **DESIGN SET (2026-07-01, operator).** `WardFormula` is the internal
> **ideal** language (full `Temporal Σ` expressiveness, Ward's own denotational
> semantics); `compile : Temporal Σ → WardFormula` is a **lossless** change of
> representation Ken proves faithful once; **projections**
> `π : WardFormula → CheckerLang` express it in the simplified terms a concrete
> (replaceable, §01) checker accepts. Resolver: `OQ-wardformula`. Ken pins the
> source (ken `72 §3`) and proves the lemma (ken `72 §6.3`, `71 §5`).

## The two layers

Ken hands Ward a **richer** logic than any orchestrated checker's input language
can represent — the full modal μ-calculus over `Σ` (ken `72 §3`:
`atom/not/and/or/next/until` **+** `mu`/`nu` fixpoints). And §01 commits Ward to
**no** permanent checker. Both facts point the same way: hold the ideal
internally, and project *down* to whatever checker is mounted.

- **`compile : Temporal Σ → WardFormula` — lossless (faithfulness lives here).**
  `WardFormula` is the **ideal**: the same logic Ken exports, in Ward's syntax
  with Ward's denotational semantics `⟦·⟧` over `Σ`-behaviors. Because both
  sides are the same logic, `compile` is essentially an **isomorphism** — which
  is exactly why Ken can prove `⟦φ⟧ = ⟦compile φ⟧` cleanly, **once**, at the
  compiler level (ken `72 §6.3`, the analog of Kripke adequacy; amortized to
  zero per obligation). This is the trust-edge lemma (§23), and it is small and
  stable.
- **`π : WardFormula → CheckerLang` — lossy (fidelity lives here).** The
  projection expresses `WardFormula` in a concrete checker's poorer language
  (Quint/Apalache LTL-ish temporal, an LTL→Büchi monitor, …). It is
  engine-specific and **replaceable**: a new checker is a new `π`, leaving
  `WardFormula` and Ken's lemma untouched.

The payoff of the split: the hard-to-prove part (faithfulness) is lossless and
proved once by Ken; the engineering part (fitting a weaker checker) is isolated
in swappable adapters. This is §01's minimal-core-over-replaceable-tools split
inherited by the translation layer — `WardFormula` + the lemma sit in the
trusted core; the projections are the invoked-tool adapters.

## Projection fidelity — a first-class, recorded property

A projection is lossy, so each one **declares its fidelity**, which determines
how the checker's verdict maps to a discharge outcome (§12) and keeps it honest:

| Fidelity | When | Checker verdict → `outcome` (§12) |
|---|---|---|
| **exact** | the formula is in the checker's expressible fragment | transfers directly (`discharged` / `bounded` / `failed`) |
| **sound over-approx** | `π` strengthens the property (or widens the model) | green ⇒ the ideal holds; a counterexample may be **spurious** (re-check before `failed`) |
| **sound under-approx / bounded** | `π` checks a weaker or depth-bounded image | a counterexample is **real** (`failed`); green is only `bounded` |
| **not projectable** | the fixpoint structure has no honest image in this checker | this checker **cannot** discharge it — route onward (below) |

A projection whose fidelity is not one of the sound classes is **rejected** —
Ward never ships a `π` that can silently turn "green" into a false discharge.

## The μ-calculus gap, handled — not tolerated

The general case Ken can hand us is a genuine alternating `mu`/`nu` property; a
typical L1 checker (Apalache/TLA+ temporal, bounded, LTL-ish) has **no** exact
image for it. Under the two layers this is not a loss but a **recorded
routing**: the property is `WardFormula` in full; the *projection* is
`not projectable` for that checker, and the obligation is routed —

1. to a **different projection/checker** that can express it (a
   μ-calculus-capable engine mounted later — cheap under §01), or
2. to **Keep's runtime monitor** — a monitor can *watch* a temporal property at
   runtime even when offline L1 cannot *decide* it (→ `monitored`, §51/§53), or
3. **flagged undischarged** — honestly, never dropped (§33, §23).

The **LTL-expressible fragment** projects `exact` and is L1's common, fast path;
the fixpoint remainder is classified and routed. Which formulas fall where is a
property of the projection, recorded per obligation in the attestation.

## Keep the two *downward* projections distinct

There are two projections out of `WardFormula`, and they must not be conflated
(ken `72 §6.3`): **`π_L1 : WardFormula → CheckerLang`** (this section, model
checking) and **`π_mon : WardFormula → Monitor`** (§51, runtime synthesis,
LTL→Büchi/MOP — Keep's). Same ideal source, different codomains and fidelities.
(The old "two `compile` functions" framing is subsumed: there is one `compile`
into the ideal, then two projections out of it.)

## Ward design deliverables

- **`WardFormula` — the ideal IR + its `⟦·⟧`.** The full μ-calculus-over-`Σ`
  value-set (ken `72 §3.1`), with the `(oracle)`-tagged semantics **pinned**:
  **finite vs ω-behaviors** and the **μ/ν fixpoint interpretation** (ken
  `72 §6.2`). Denotational, engine-independent — so the lemma is statable and
  every projection is checked against *it*, not against a checker's quirks.
- **`compile` (lossless) + its lemma shape.** Fix the isomorphic map and state
  `⟦φ⟧ = ⟦compile φ⟧` — the behavior model, the equality, the induction — so Ken
  can carry it. Handle the `Pred Σ` atoms and first-order `mu`/`nu` binding (ken
  `72 §3.1`) as structure-preserving.
- **The projection interface + fidelity classification.** A typed interface
  every `π` implements, carrying its fidelity and its fragment coverage; the
  soundness obligation each `π` must discharge (verdict-over-`π` ⇒ honest claim
  about `WardFormula`); and the routing for `not projectable`.
- **The first projection** (to the initial checker, Quint/Apalache; see
  §22/§31): classify which `Temporal Σ` fragments it covers `exact` vs routes
  onward.
- **Coordinate the source spelling back to Ken.** The `Temporal` constructor
  spelling, the `Pred Σ` atom language, the fixpoint-variable representation,
  and the ITF serialization are the **joint Ward encoding pass** ken defers
  (`72 §3.1`/`§6.3`); finalizing `WardFormula` finalizes what it compiles *from*
  — feed it back into ken conformance (mirrors §11's wire spellings).

## Relationship to the rest

`WardFormula` is the property side of the ideal; §22 is the system side, treated
the same way (an ideal `WardModel` + classified projections). Projection
soundness is a distinct faithfulness component that composes into §23. The
`WardFormula`/`compile`/`π` versions are pinned in the discharge attestation
(§12), since faithfulness is relative to them. Resolver: `OQ-wardformula` (§90).
