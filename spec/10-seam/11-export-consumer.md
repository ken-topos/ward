# 11 — Consuming the assumption-boundary export

> Status: DRAFT v0. Restates ken `spec/70-behavioral/71-assumption-boundary.md`
> (impl-ready, B1) from the consumer side. Ken locks the concept, value-set,
> per-entry status, cross-field invariants, and content-hash discipline; the
> literal wire spellings of the five fields are `(oracle)`-tagged and finalized
> **with Ward** — so field naming is a Ward deliverable, coordinated back to Ken.

## What Ward receives

A generated, content-addressed **assume-guarantee contract**, emitted by
`ken-elaborator` when Ken finishes checking a program. Two layers:

- **Contract layer — Ken-native.** The five fields below, semantically faithful
  to Ken's terms (a lossy translation here would break the single-logic
  guarantee).
- **Trace layer — ITF** (Apalache/Quint *Informal Trace Format*). Execution and
  counterexample witnesses; interop currency Ward's tools already speak. Traces
  carry **no epistemic status** — a green trace is evidence for a `delegated`
  `T`, never a promotion of it.

## The five fields

| Field | Carries | Entry status | Ward consumes it as |
|---|---|---|---|
| `Q` guarantees | proved postconditions / space invariants | `proved` | invariants the model **assumes**, shrinking state space |
| `P` assumptions | `trusted_base_delta` ∪ `assume`/`test` ∪ boundary labels | `tested` / `unknown` | the nondeterministic **environment** / generator input domain |
| `Σ` alphabet | interaction-tree perform-node signatures | — | the **event alphabet** of the model + monitor |
| `T` obligations | `Temporal` (LTL/μ-calculus) values in source | `delegated` | the **properties** to model-check (§30) and monitor (§50) |
| `G` generators | refinement-type support: partition, boundaries, cases | derived | **generators + oracles** (§40) |

`Σ` is **reuse, not reinvention**: Ward watches exactly the events Ken's
denotation can emit — there is no separate alphabet to define or keep in sync.
`G` carries **support only, never a measure** (weights are the sampling policy's
job, §40) — enforced type-level in Ken's emitter, so a measure in `G` is not
representable.

## Invariants Ward relies on (Ken locks; Ward must not assume more)

- **I1 no over-claim** — every `Q` entry traces to a `proved` verdict whose goal
  is absent from `trusted_base()`; nothing in `Q` is `tested`/`unknown`/
  `delegated`. Ward may **assume** `Q` without re-proving it.
- **I2 assumption visibility** — every postulate in `trusted_base_delta` appears
  in `P`; shrinking the delta removes the `P` entry (and changes the hash).
- **I3 alphabet closure** — every symbol used by `T`, `G`, or a monitor is in
  `Σ`; `Σ` is exactly the denotation's perform-nodes (no orphan, no missing).
- **I4 delegated never proved** — every `T` is `delegated`; nothing promotes it.
- **I5 no measure** — no `G` entry carries a weight/likelihood.

## Content-addressing

The export is **versioned and content-addressed**: a canonical-form hash,
deterministic in the checked program (same program → same hash). The hash travels
in Ken's provenance (build ↔ export) and is what the **discharge attestation**
(§12) binds to (export ↔ discharge) — so "this Ward model corresponds to this
code" is checkable, not asserted. A field rename after the spelling binds is a
**breaking change** (new hash, new contract version).

## Ward's obligations here

1. **Parse + validate** the contract layer against I1–I5; reject an export that
   violates them (that is a Ken bug, not something Ward papers over).
2. **Finalize the wire spellings** of the five fields + the per-entry status tag
   against Ward's parsers, and coordinate them back into ken conformance
   (`conformance-assert-at-locked-granularity`). Ken fixed the concept; Ward
   fixes the token.
3. **Treat the hash as the identity** of a model/attestation throughout §12–§50.
