# 30 — L1: model checking

> Status: DRAFT v0 — open design.

Check exported obligations `T` against `Q` (assumed, not re-proved → smaller
state space) and `P` (the environment). The most Ken-congruent slot is **Quint +
Apalache** (typed, effect-aware, SMT-backed); ITF is the shared trace format.

**Ward design deliverables:** the model-checker integration (Quint/Apalache
invocation, ITF ingest); how `Q`/`P` shrink and shape the state space; the
depth/bound policy and how a bounded result is recorded honestly in the discharge
attestation (§12) without claiming proof-for-all-N; counterexample-trace handling
into §50 trace conformance.
