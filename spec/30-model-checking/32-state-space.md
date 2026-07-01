# 32 — State-space shaping: `Q` assumed, `P` as environment

> Status: DRAFT v0 — open design. Rests on the export's field semantics (§11)
> and its invariants I1–I5. Resolver: `OQ-model-target`.

## What it is

The export's five fields are not just data to check *against* — three of them
**shape the search itself**. Using them correctly is what makes L1 tractable and
what keeps it honest. This chapter fixes how `Q`, `P`, and `T` enter the
checker.

| Field | Role in L1 | Consequence |
|---|---|---|
| `Q` guarantees | **assumed** invariants — Ken already proved them | prune unreachable/impossible states; **do not re-prove** |
| `P` assumptions | the **nondeterministic environment** | the checker's free/adversarial inputs; the interleaving source |
| `T` obligations | the **properties to check** | the temporal formulas (via §21) L1 decides up to bound |

## `Q` is assumed, never re-proved

Every `Q` entry traces to a `proved`, kernel-checked verdict (§11 I1). L1 takes
each `Q` as a **standing invariant of the model** — a constraint that prunes the
state space, not a proof obligation. This is a soundness *and* a performance
lever: re-deriving `Q` classically would be redundant work **and** would risk L1
"disagreeing" with the kernel on something the kernel already settled. Ward
**assumes `Q`, shrinks by it, and moves on** (§00, guarantee levels). If the
model's reachable states appear to violate a `Q`, that is a **model-generation
bug** (§22) or an export bug (§11) — surfaced, never papered over.

## `P` is the environment — including the adversarial one

`P` carries `trusted_base_delta ∪ assume/test ∪ boundary labels` as `tested`/
`unknown` entries (§11). L1 realizes each as **nondeterministic environment
choice**: the checker explores *all* values `P` ranges over. This is the same
move that makes the agentic envelope work — an embedded agent is just `P` at its
most adversarial (ken `74 §2`), and L1 proving `T` "for all `P`" is proving it
for every value the environment (or agent) can supply. The `Σ` alphabet bounds
what `P` can do; the space semantics bound how it interleaves.

## Assume-guarantee decomposition

`Q ⊣ P` is the seam's soundness shape (ken `OQ-classical-bridge`), and it is
also L1's **compositional lever**: a large system's obligation can be discharged
by checking each component against its neighbors' `Q` (assumed) under its own
`P` (environment), rather than the whole product at once. How far to push this —
per-`space` decomposition, symmetry reduction over identical spaces — is a
design question with direct tractability payoff.

## Ward design deliverables

- **The `Q`→invariant and `P`→environment lowering.** Precisely how each field
  entry becomes a checker constraint / free input, preserving the entry's
  epistemic status (a `Q` is a hard invariant; a `P` is free nondeterminism).
- **The decomposition strategy.** Whether/how to check per-`space` under
  assume-guarantee, symmetry reduction over identical spaces, and how partial
  (component-level) results compose in the attestation (§12/§33).
- **State-space controls.** Which `Q`-driven prunings, data abstractions, and
  bounds keep the search tractable — and, for each, what it costs in coverage,
  fed to the bound ledger (§33).
- **Consistency guards.** Detect and report a model that violates an assumed `Q`
  (a generation/export bug), rather than returning a misleading verdict.

Resolver: `OQ-model-target` (§90); coupled to §22 (the model these constraints
shape) and §33 (how their coverage cost is recorded).
