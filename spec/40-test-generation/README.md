# 40 — L2: spec-driven test generation

> Status: DRAFT v0 — open design. The second engine (§30–§50). It consumes the
> `G` field of the export (§11) and one L1 output (counterexamples, §33), and it
> hosts the **sampling policy** — the one piece of the seam Ken deliberately
> leaves unmeasured (`OQ-sampling-policy`, Ward-blocked in ken). Reference:
> `local/refs/quickchick` (read for behavior, not expression).

L2 answers the **unbounded, sampled** question L1 cannot: over the real code
(not a model), on inputs drawn from the valid domain, does the system behave?
Ken hands Ward everything needed to *generate and check* — the
refinement/dependent types are simultaneously **generators** (they define the
valid input domain) and **oracles** (they define the postcondition) — but with
**no measure** on the domain (§11 I5). Ward's L2 contribution is fourfold:
derive the generators and oracles from `G`, supply the missing measure as a
governed **sampling policy**, cover the oracle-free (agentic) outputs by
**metamorphic** relations, and retain every defect found as a pinned
**regression corpus** — the deterministic backstop under the sampled measure.

- [`41-generators-oracles.md`](41-generators-oracles.md) — deriving generators
  and oracles from `G`'s support (partition, boundaries, cases),
  QuickChick-style.
- [`42-sampling-policy.md`](42-sampling-policy.md) — the per-deployment measure
  Ken never supplies: its schema, its governance, and its vocabulary (the
  exported partition). `OQ-sampling-policy`.
- [`43-metamorphic.md`](43-metamorphic.md) — model-based generation from L1 and
  metamorphic testing where no propositional oracle exists (feeds §53).
- [`44-regression-corpus.md`](44-regression-corpus.md) — the pinned,
  auto-populated witnesses of every defect found: the deterministic
  (weight-∞) counterpart to §42's measure. `OQ-regression-corpus`.
