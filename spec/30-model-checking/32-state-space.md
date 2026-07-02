# 32 — State-space shaping: `Q` assumed, `P` as environment

> Status: **DESIGN SET (2026-07-01, operator).** The `Q`/`P`/`T` lowering, now
> concrete against the confirmed engine (§31, Quint + Apalache). Rests on the
> export's field semantics (§11) and its invariants I1–I5. Resolver:
> `OQ-model-target`.

## What it is

The export's five fields are not just data to check *against* — three of them
**shape the search itself**. Using them correctly is what makes L1 tractable and
what keeps it honest. This chapter fixes how `Q`, `P`, and `T` enter the
checker.

| Field | Role in L1 | Lowering to Quint/Apalache (§31) |
|---|---|---|
| `Q` guarantees | **assumed** invariants — Ken already proved them | a state constraint on `init`/`step`, and — the strong path — an **inductive invariant** supplied to Apalache; **never** a checked obligation |
| `P` assumptions | the **nondeterministic environment** | free/adversarial input: `nondet x = S.oneOf()`, unconstrained vars, `any { … }` branch choice |
| `T` obligations | the **properties to check** | a state invariant (`val`) or a temporal formula (via §21), decided or bounded by mode |

## `Q` is assumed, never re-proved — and is the inductiveness lever

Every `Q` entry traces to a `proved`, kernel-checked verdict (§11 I1). L1 takes
each `Q` as a **standing invariant of the model** — a constraint that prunes the
state space, not a proof obligation. This is a soundness *and* a performance
lever: re-deriving `Q` classically would be redundant work **and** would risk L1
"disagreeing" with the kernel on something the kernel already settled. Ward
**assumes `Q`, shrinks by it, and moves on** (§00, guarantee levels).

Concretely against §31, `Q` is also what unlocks the **inductive-invariant
mode** — the only L1 path to an unbounded *decision*. An assumed `Q` (or a
`Q`-strengthened conjunction) is exactly the shape Apalache needs for its
two-query inductive check (`init ⇒ Inv`; `Inv ∧ step ⇒ Inv'`): because Ken
already proved `Q`, Ward asserts it rather than re-establishing it, and can lean
on it to make a `T` inductive and thus `discharged` rather than merely
`bounded`. If the model's reachable states appear to violate a `Q`, that is a
**model-generation bug** (§22) or an export bug (§11) — surfaced by a
consistency guard (below), never papered over.

## `P` is the environment — including the adversarial one

`P` carries `trusted_base_delta ∪ assume/test ∪ boundary labels` as `tested`/
`unknown` entries (§11). L1 realizes each as **nondeterministic environment
choice** — `nondet … oneOf()`, free variables, `any`-branch selection — so the
checker explores *all* values `P` ranges over. This is the same move that makes
the agentic envelope work: an embedded agent is just `P` at its most adversarial
(ken `74 §2`), and L1 deciding `T` "for all `P`" decides it for every value the
environment (or agent) can supply. The `Σ` alphabet bounds what `P` can do; the
space semantics bound how it interleaves. Where `P` ranges over a large or
infinite data domain, **symbolic Apalache** (not explicit TLC) is what keeps
this tractable — Z3 reasons over the domain instead of enumerating it (§31).

## Assume-guarantee decomposition

`Q ⊣ P` is the seam's soundness shape (ken `OQ-classical-bridge`), and it is
also L1's **compositional lever**: a large system's obligation can be discharged
by checking each component against its neighbors' `Q` (assumed) under its own
`P` (environment), rather than the whole product at once. How far to push this —
per-`space` decomposition, symmetry reduction over identical spaces — is a
design question with direct tractability payoff, and it is what keeps the
per-`space` state within reach of the finite-state modes.

## Ward design deliverables

- **The `Q`→invariant and `P`→environment lowering.** Precisely how each field
  entry becomes a Quint constraint / free input, preserving the entry's
  epistemic status (a `Q` is a hard, assumed invariant — candidate inductive
  strengthening; a `P` is free nondeterminism). Fix the encoding of assumed-`Q`
  as an Apalache inductive-invariant input (§31).
- **The decomposition strategy.** Whether/how to check per-`space` under
  assume-guarantee, symmetry reduction over identical spaces, and how partial
  (component-level) results compose in the attestation (§12/§33).
- **State-space controls.** Which `Q`-driven prunings, data abstractions, and
  bounds keep the search tractable — and, for each, what it costs in coverage,
  fed to the bound ledger (§33). This is where the model projection's fidelity
  (§22) is chosen: over-approx by widening, under-approx by bounding.
- **Consistency guards.** Detect and report a model whose reachable states
  violate an assumed `Q` (a generation/export bug), rather than returning a
  misleading verdict — the `Q`-as-assumption discipline made operational.

Resolver: `OQ-model-target` (§90); coupled to §22 (the model these constraints
shape) and §33 (how their coverage cost is recorded).
