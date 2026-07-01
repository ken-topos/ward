# 22 — Model translation: the generated transition system

> Status: DRAFT v0 — open design. Ken guarantees the model has **no authoring
> drift** because it is *generated*, not written (ken `71 §1`, `73 §1`); Ward
> designs the **target** the model generates into and the generator. Resolver:
> `OQ-model-target`.

## What it is

A model-checker checks properties against a **transition system**, not against
source code. The soundness of the whole L1 pillar (§30) rests on that transition
system actually *being* the program's behavior — the deepest way `τ` can lie is
a model that quietly disagrees with the code. Ward's defense is structural, and
it is inherited from Ken's design: the model is **generated** from verified
content, never hand-authored beside it, so the class of "the spec drifted from
the code" bugs is closed **by construction** rather than by review (ken `71 §1`,
`73 §1`).

Two pieces are already fixed by the export (§11), so Ward is not inventing a
model from nothing:

- **The alphabet is `Σ`** — the interaction-tree perform-node signatures (ken
  `72 §3`, `73 §2.1`). Ward watches exactly the events Ken's denotation can
  emit; there is no separate alphabet to define or keep in sync (§11, invariant
  I3).
- **The state machine derives from the space semantics** — the encapsulated,
  non-aliased `space` cells and shared-nothing message passing Ken settled
  (`OQ-Space`; ken `30-surface/36 §4`). The transitions are the `space` ops and
  the perform-nodes; `Q` (§32) prunes the reachable state.

What Ward designs is the **target** those two pieces generate *into* — a Quint
module, an Apalache TLA+ spec, or a Ward-internal IR that lowers to one — and
the generator that produces it deterministically from the export.

## Ward design deliverables

- **The model target** (`OQ-model-target`). Choose Quint module / Apalache TLA+
  / an intermediate IR that lowers to a checker. The overview names **Quint +
  Apalache** as the most Ken-congruent slot (typed, effect-aware, SMT-backed;
  §30); confirm or revise against the reference shelf (`local/refs/quint`,
  `apalache`, `tlaplus`) and record the trade (BMC vs symbolic reach, typedness,
  ITF-native output — §31).
- **The generator: `Σ` + space-semantics → model.** How a perform-node signature
  becomes a transition, how a `space` cell becomes state, how message passing
  becomes the concurrency/interleaving structure, and how `Q`/`P` (§32) enter as
  assumed invariants / environment nondeterminism. The generator is an
  **untrusted projection** of already-verified content (like the B1 emitter, ken
  `71 §6`; the B3 trace contract, ken `73`) — it adds nothing to the TCB and
  cannot assert more than Ken established.
- **Determinism + content-addressing.** Same export → same model (mirrors the
  export's own content-hash discipline, §11). The model's identity is a function
  of the export hash, so "this model corresponds to this code" is checkable, and
  the discharge attestation (§12) can bind a model-check verdict to the pair.
- **The abstraction boundary.** A finite-state model of an unbounded program is
  necessarily an **abstraction**; state exactly what is abstracted away (data
  domains collapsed, depth bounded — §33) so the residual concrete-vs-abstract
  gap is explicit and lands in §23/§50, not hidden in the generator.

## Why "generated" is load-bearing, not incidental

If the model were authored, faithfulness would be a per-program review burden
and a standing source of the worst bug class. Because it is generated from the
same `Σ`/space semantics the code denotes, authoring drift is **impossible**,
and faithfulness reduces to two checkable things: the generator is semantics-
preserving (§23) and the running code stays within the model (trace conformance,
§52). Resolver: `OQ-model-target` (§90), coordinated with §30's engine choice.
