# 30 — L1: model checking

> Status: DRAFT v0 — open design. This is the first of Ward's three engines
> (§30–§50). It consumes the seam (§10) through the translation (§20) and feeds
> the discharge attestation (§12). The most Ken-congruent slot is **Quint +
> Apalache** (typed, effect-aware, SMT-backed; `local/refs/quint`, `apalache`,
> `tlaplus`); ITF is the shared trace currency.

L1 answers the **bounded, exhaustive** question: over the generated model (§22),
do the exported obligations `T` hold, given the guarantees `Q` and against the
environment `P`? Where the model is finite-state, the answer is a decision;
where it is not, it is a decision **up to a bound**, recorded honestly. L1 is
Ward's strongest engine — it explores *all* interleavings within its bound,
which is exactly the liveness/fairness/ordering territory interactive proof
cannot reach (ken `72 §1`) — and its counterexamples are the seed corpus for L2
(§40) and the oracle for trace conformance (§52).

- [`31-integration.md`](31-integration.md) — the checker integration: engine
  choice, invocation, ITF ingest, and the BMC-vs-symbolic reach trade.
- [`32-state-space.md`](32-state-space.md) — how `Q` (assumed, not re-proved)
  and `P` (the environment) shape and shrink the state space; the
  assume-guarantee decomposition.
- [`33-bounded-results.md`](33-bounded-results.md) — the depth/bound policy, how
  a bounded result is recorded in the discharge attestation (§12) without
  claiming proof-for-all-`N`, and how counterexamples flow to §40/§52.
