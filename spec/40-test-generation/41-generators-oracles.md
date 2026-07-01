# 41 — Generators and oracles from `G`

> Status: DRAFT v0 — open design. Consumes the `G` field (§11): refinement-type
> support — partition, boundaries, cases — carrying support **only, never a
> measure** (I5). Reference: `local/refs/quickchick`. Resolver:
> `OQ-sampling-policy` (the measure half is §42; this chapter is the support
> half).

## What it is

Ken's refinement and dependent types already say what a valid input **is** and
what a correct output **satisfies** — so they are, at once, the two halves of a
property test: the **generator** (produce a value in the refinement) and the
**oracle** (check the postcondition holds). L2's first job is to make that
latent structure executable: derive from `G` a generator that produces
well-distributed valid inputs and an oracle that decides the postcondition,
without re-authoring either — they are Ken's, projected.

`G` carries the **support structure** (§11): the equivalence-class partition of
the valid domain, the boundary values, and the case decomposition. It carries
**no weight** (I5, enforced type-level in Ken's emitter, so a measure is not
even representable). That division is deliberate: the *shape* of the valid
domain is a verified fact about the program (Ken's), but *which* valid inputs
matter is a per-deployment judgment (the sampling policy's, §42). This chapter
owns the shape; §42 owns the weight.

## Generators and oracles are the same types, two directions

- **Generator direction.** A refinement `{ x : T | φ x }` is a producer of `T`s
  satisfying `φ`. `G`'s partition/boundaries/cases tell the generator *where the
  interesting regions are* (equivalence classes to cover, boundaries to hit, the
  case split to exercise) — the classic QuickChick move, but sourced from Ken's
  verified support rather than a hand-written generator.
- **Oracle direction.** The same (or the postcondition's) refinement, read as a
  decision procedure over an output, is the **oracle**: run the code, check the
  result against the type. Where the postcondition is decidable, the oracle is
  exact; where it is not, the property is oracle-free and falls to §43
  (metamorphic) — this chapter marks that boundary, §43 handles it.

## Ward design deliverables

- **The derivation `G → (generator, oracle)`.** How the partition becomes
  coverage targets, how boundaries become boundary cases, how the case
  decomposition drives the generator's structure — QuickChick-style, sourced
  from `G` (read `local/refs/quickchick` for *approach*, integrate under its
  license, never vendor — `CLEAN-ROOM.md`).
- **The generator/oracle IR and its host.** What language/runtime the generated
  tests target (Ward's stack, `OQ-ward-stack`), how they invoke the real Ken
  code under test, and how shrinking works against the refinement structure.
- **The oracle/oracle-free boundary.** A precise rule for when a postcondition
  yields an exact oracle vs when the output is oracle-free and must be handed to
  §43 — mirroring ken `74 §2.1`'s status partition (decidable postcondition ⇒
  oracle; no oracle ⇒ metamorphic/`unknown`).
- **The measure seam.** The generator must accept the sampling policy's weights
  (§42) as a *separate* input over its partition vocabulary — the support and
  the measure stay decoupled, so the same generators serve any deployment's
  policy.

Resolver: `OQ-sampling-policy` (§90); the support/measure split is the reason
this OQ is Ward's to close.
