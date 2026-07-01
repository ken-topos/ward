# 40 — L2: spec-driven test generation

> Status: DRAFT v0 — open design.

Ken's refinement/dependent types are **generators and oracles**; `G` (§11)
carries the **support** — the equivalence-class partition, the boundaries, the
case decomposition — with **no measure**. Ward supplies the measure as a
separately-authored, per-deployment **sampling policy** (which valid states are
likely / important / risky), governed like Ken's security policy and hosted on
Ward's side.

**Ward design deliverables:** the generator/oracle derivation from `G`
(QuickChick-style); the **sampling policy** schema and governance
(`OQ-sampling-policy`, Ward-blocked in Ken) — indexed over Ken's exported
partition as its vocabulary; model-based test-case generation from L1; and
**metamorphic** testing for oracle-free (agentic) outputs (§50).
