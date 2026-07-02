# 31 — Checker integration

> Status: **DESIGN SET (2026-07-01, operator).** Engine **confirmed: Quint +
> Apalache** (with TLC as the explicit-state escalation), read for behavior from
> `local/refs/{quint,apalache,tlaplus}` (Apache-2.0, read-to-understand).
> Depends on the model target (§22) and the property target (§21). Resolver:
> `OQ-model-target` (shared with §22).

## What it is

The mechanical coupling between Ward and an external model checker: how a
generated model (§22) plus compiled properties (§21) become a checker run, and
how the checker's output — verdicts and counterexample traces — comes back into
Ward as ITF (§11, the trace layer). This chapter fixes the *engine* and the
*plumbing*; §32 fixes what the checker is asked to prove, §33 fixes how its
answer is recorded.

## The confirmed engine

**Quint + Apalache**, confirmed for the structural reasons §22 anticipated:

- **Typed and effect-aware (Quint)** — Quint's type/effect discipline is the
  closest external analog to Ken's, so the `Σ`/space model (§22) lands with the
  least impedance. A `WardModel` projects to a Quint module: state variables,
  `init`/`step` actions, `val` invariants.
- **Symbolic, SMT-backed (Apalache)** — `quint verify` transpiles to Apalache,
  which encodes the model + property into SMT and discharges with Z3. This
  reaches large (unbounded-*data*, bounded-*depth*) state that explicit
  enumeration cannot, which matters because `Σ`/`space` state is data-rich.
- **ITF-native** — both Apalache and the Quint simulator emit the *Informal
  Trace Format* (Apalache ADR-015) Ward already fixed as its trace currency
  (§11). Counterexamples arrive in the interop format with no second parser.

## The engine trade — three modes, one slot

The "Quint + Apalache" slot is really **three checking modes** behind one model
projection, selected per obligation class (§33 owns the policy):

| Mode | Reach | Verdict strength | Use when |
|---|---|---|---|
| **Apalache — bounded symbolic** | depth `k` (`--max-steps`, default 10); rich data via Z3 | green = **bounded** (green-up-to-`k`); counterexample real | the default; data-rich state, shallow-to-medium depth |
| **Apalache — inductive invariant** | unbounded (2-query: init ⇒ `Inv`, `Inv ∧ step ⇒ Inv'`) | green = **decision** (`discharged`), no depth bound | a `Q`-style standing invariant is available (§32) |
| **TLC — explicit-state** | any length; full temporal | green = **decision** on that finite model | state-space small; genuine liveness needs deciding |

The **inductive-invariant** mode is Ward's most valuable path: it turns a green
into an actual decision — not merely bounded evidence — even over infinite-data
state, and it composes directly with §32's "`Q` is an assumed standing
invariant." **TLC** is the explicit-state escalation when a genuine liveness
obligation over a small finite model needs deciding rather than bounding
(Apalache's temporal support is bounded — §33). Bounded-symbolic Apalache is the
common default.

## Ward design deliverables

- **The mode-selection policy.** Per obligation class (safety invariant /
  bounded temporal / liveness / inductive-invariant-available), which mode runs,
  when to escalate depth `k`, and when to fall back from bounded-symbolic to
  inductive or to TLC. State the default and the knobs; feed the coverage cost
  to the bound ledger (§33).
- **Invocation + toolchain contract.** Ward drives the checker as an **invoked,
  version-pinned process** (§01 — never linked; Apalache is Apache-2.0 but the
  posture is uniform). Apalache is normally auto-downloaded by `quint verify`;
  Ward instead **pins** the Quint, Apalache, and Z3 versions (and the JVM),
  fixes the seed / `--max-steps`, and records all of them into the discharge
  attestation (§12) — the checker is part of the classical trust base Ward
  records, and Z3's version can move a verdict.
- **ITF ingest.** Parse counterexample and witness traces into Ward's internal
  trace type via a pinned ITF reader (`itf-rs`, matching §01's Rust substrate);
  handle the two ITF shapes — **finite execution** and **lasso** (a finite
  prefix followed by a repeated loop, i.e. the ω-behavior of a finite-state
  system, §21's pinned `⟦·⟧`) — and the `{#bigint}` integer encoding. Confirm
  the trace's `Σ` alphabet matches the export's `Σ` (§11 I3); route
  counterexamples to §33 (recording) and §52 (conformance oracle).
- **Failure/timeout handling.** A checker timeout, memory-out, Z3 `unknown`, or
  `--max-steps` exhaustion is **not** a green result: record it as
  bounded/inconclusive (§33), never silently drop it. Distinguish "no
  counterexample within `k`" (evidence) from "the solver gave up" (no evidence).

## Relationship to the rest

The checker is a **classical, untrusted** oracle: a green verdict is evidence
for a `delegated` `T`, never a promotion (§23; ken `71 §5.1`). Its identity and
version — Quint, Apalache, Z3 — are part of what §12 pins. The mode chosen
determines whether the outcome can be `discharged` (inductive / exhaustive-TLC)
or only `bounded` (symbolic depth-`k`), which §33 records. Resolver:
`OQ-model-target` (§90).
