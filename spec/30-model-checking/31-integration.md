# 31 — Checker integration

> Status: DRAFT v0 — open design. Depends on the model target (§22,
> `OQ-model-target`) and the property target (§21, `OQ-wardformula`). Resolver:
> `OQ-model-target` (shared with §22).

## What it is

The mechanical coupling between Ward and an external model checker: how a
generated model (§22) plus compiled properties (§21) become a checker run, and
how the checker's output — verdicts and counterexample traces — comes back into
Ward as ITF (§11, the trace layer). This chapter fixes the *engine* and the
*plumbing*; §32 fixes what the checker is asked to prove, §33 fixes how its
answer is recorded.

## The candidate slot

The overview and §22 name **Quint + Apalache** as the most Ken-congruent choice,
and the reason is structural, not incidental:

- **Typed and effect-aware** — Quint's type/effect discipline is the closest
  external analog to Ken's, so the `Σ`/space model (§22) lands with the least
  impedance.
- **SMT-backed symbolic checking** — Apalache checks TLA+/Quint via SMT, which
  handles large (even unbounded-data, bounded-depth) state better than explicit
  enumeration.
- **ITF-native** — Apalache/Quint emit the *Informal Trace Format* Ward already
  speaks (§11); counterexamples arrive in the interop currency with no second
  parser. This is why §11 fixed ITF as the trace layer.

Confirm or revise against `local/refs/{quint,apalache,tlaplus}` (read for
behavior, not expression — `CLEAN-ROOM.md`); if revised, record the trade in
`OQ-model-target`.

## Ward design deliverables

- **Engine selection + the BMC-vs-symbolic trade.** Bounded model checking
  (fast, shallow, definite up to depth `k`) vs symbolic/SMT (deeper reach,
  richer data, costlier). Likely both, selected per obligation class; state the
  policy and how it interacts with the bound ledger (§33).
- **Invocation + toolchain contract.** How Ward drives the checker (process,
  version pin, determinism), what it pins for reproducibility, and how the
  checker's version enters the discharge attestation (§12) — the checker is part
  of the classical trust base Ward records.
- **ITF ingest.** Parse counterexample and witness traces into Ward's internal
  trace type; confirm the `Σ` alphabet of the trace matches the export's `Σ`
  (§11 I3); route counterexamples to §33 (recording) and §52 (conformance
  oracle).
- **Failure/timeout handling.** What Ward records when the checker times out,
  runs out of memory, or returns "unknown" — these are **not** green results and
  must be recorded as bounded/inconclusive (§33), never silently dropped.

## Relationship to the rest

The checker is a **classical, untrusted** oracle: its green verdict is evidence
for a `delegated` `T`, never a promotion (§23; ken `71 §5.1`). Its identity and
version are part of what §12 pins. Resolver: `OQ-model-target` (§90).
