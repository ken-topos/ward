# 22 — Model translation: `WardModel` and its projections

> Status: **DESIGN SET (2026-07-01, operator).** The system side, treated
> exactly like the property side (§21): an internal **ideal** `WardModel` — the
> faithful transition system **generated** from `Σ` + the space semantics — and
> **lossy, classified projections** `π : WardModel → CheckerModel` to a concrete
> (replaceable, §01) checker. Ken guarantees no authoring drift because the
> model is generated, not written (ken `71 §1`, `73 §1`). Resolver:
> `OQ-model-target`.

## The two layers (mirroring §21)

A model-checker checks properties against a **transition system**, not source
code, so the deepest way `τ` can lie is a model that quietly disagrees with the
code. Ward answers it the same way as on the property side — hold a faithful
ideal, project *down*:

- **Generation: `Σ` + space semantics → `WardModel` — faithful (no drift).**
  `WardModel` is the **ideal**: the transition system *generated* from
  already-verified content, not hand-authored beside it, so the "spec drifted
  from code" bug class is closed **by construction** (ken `71 §1`, `73 §1`). Two
  pieces are already fixed, so Ward is not inventing a model from nothing:
  - **the alphabet is `Σ`** — the interaction-tree perform-node signatures (ken
    `72 §3`, `73 §2.1`); Ward's model steps exactly the events Ken's denotation
    can emit (§11, I3);
  - **the state machine derives from the space semantics** — the encapsulated,
    non-aliased `space` cells and shared-nothing message passing (`OQ-Space`;
    ken `36 §4`); transitions are `space` ops + perform-nodes.

  `WardModel` may be **infinite-state** (faithful to the denotation); it is the
  generator's output and an *untrusted projection of verified content* (like the
  B1 emitter, ken `71 §6`) — it adds nothing to the TCB and asserts no more than
  Ken established.
- **Projection: `WardModel → CheckerModel` — lossy (abstraction lives here).**
  The projection to a concrete checker (a Quint module, an Apalache TLA+ spec)
  is where **finite-state abstraction** happens — data domains collapsed,
  instance counts and depth bounded. Engine-specific and **replaceable**: a new
  checker is a new `π`, leaving `WardModel` untouched.

This locates the abstraction gap precisely: it is **not** in the generation
(faithful) but in the **model projection**, where it is a declared, classified
property — not a silent loss buried in a generator.

## Projection fidelity (same classification as §21)

The model projection carries the same fidelity tags, governing how a checker
verdict transfers:

| Fidelity | When | Effect on the verdict |
|---|---|---|
| **exact** | `WardModel` is already finite-state in this checker's terms | verdict transfers directly |
| **sound over-approx** | `π` adds behaviors (widens reachable state) | green ⇒ property holds of the code; a counterexample may be **spurious** (§33 re-check → §52) |
| **sound under-approx / bounded** | `π` drops behaviors / bounds depth `k` | a counterexample is **real**; green is only `bounded` (§33) |
| **not projectable** | no sound finite image for this checker | route onward (another checker, or Keep runtime, §21) |

Over-approximation is the common, safe default (a green safety result over a
widened model transfers to the code); the bound/abstraction is **recorded**, so
§33's `bounded` outcome and §52's conformance check inherit an explicit account
of what was abstracted, never a hidden one.

## `Q`/`P` shape the ideal, the projection preserves them

`Q` (assumed invariants) and `P` (the nondeterministic environment) enter at the
**`WardModel`** level (§32): `Q` prunes reachable state, `P` is free/adversarial
input. The projection must preserve their roles into `CheckerModel` — a `Q`
stays a hard invariant, a `P` stays free nondeterminism — or declare the
fidelity cost if it cannot. (Assume-guarantee decomposition, §32, is likewise a
`WardModel` operation the projection carries down.)

## Ward design deliverables

- **`WardModel` — the ideal transition-system IR.** How a perform-node signature
  becomes a transition, a `space` cell becomes state, message-passing becomes
  the interleaving structure, and `Q`/`P` enter (§32) — as a faithful, possibly
  infinite-state model, **generated deterministically** from the export.
- **Determinism + content-addressing.** Same export → same `WardModel` (mirrors
  §11's content-hash discipline); the model's identity is a function of the
  export hash, so a model-check verdict binds to the (code, model) pair in the
  attestation (§12).
- **The projection interface + fidelity.** Per checker, the
  `π : WardModel → CheckerModel`, its abstraction (what it collapses/bounds),
  and its declared fidelity + soundness obligation — the same typed interface as
  §21's property projections, so L1 pairs a model projection with a property
  projection of compatible fidelity.
- **The first model target** (`OQ-model-target`). Confirm **Quint + Apalache**
  (typed, effect-aware, SMT-symbolic, ITF-native — the most Ken-congruent slot,
  §30/§31) against `local/refs/{quint,apalache,tlaplus}` (approach only), or
  record the alternative; supply the first `π` and its fidelity.

## Faithfulness, reduced

With the two layers, model faithfulness reduces to checkable parts that compose
into §23: **generation** is drift-free (structural, §21-symmetric), the **model
projection** is sound-by-its-declared-fidelity, and the **running code refines
`WardModel`** (trace conformance, §52). Resolver: `OQ-model-target` (§90),
coordinated with §21 (`WardFormula`) and §30/§31 (the engine).
