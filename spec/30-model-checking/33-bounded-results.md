# 33 — Bounded results and honest recording

> Status: **DESIGN SET (2026-07-01, operator).** The fragment map and bound
> recording, concrete against §31's three modes. This is where L1 meets the
> one-way gate (§23) and the discharge attestation (§12). Resolver:
> `OQ-discharge-attestation` (shared with §12), `OQ-model-target`.

## What it is

A model check is almost never "proof for all `N`." It is exhaustive **up to a
bound** — depth `k`, a data abstraction, a finite instance count — *unless* the
mode used is genuinely unbounded (an inductive invariant, or exhaustive TLC on a
small finite model). The result is genuine, strong evidence, but its **strength
is the mode's strength**, and the single most important thing L1 does is record
that honestly: a depth-`k` green must never be filed as if it decided the
obligation for all `N`. This chapter fixes the fragment map, the bound policy,
and the recording contract.

## The fragment map — what projects, and how strongly

Which `WardFormula` (§21) projects to the confirmed engine, and the outcome
(§12) it can earn, follows Apalache's expressible fragment (its BMC LTL
encoding: `[]`, `<>`, `~>`, `WF`, `SF` over state predicates — ADR-017; it
ignores the advanced operators `\cdot`, `\EE`, `\AA`, `-+->`):

| `WardFormula` shape | Projection fidelity (§21) | Mode (§31) | Best outcome (§12) |
|---|---|---|---|
| **State-predicate safety** (`Pred Σ`, no temporal) | exact | Apalache inductive | **`discharged`** (unbounded decision) |
| same, no inductive invariant available | exact | Apalache bounded | **`bounded`** (green-up-to-`k`) |
| **LTL temporal** (`[]`/`<>`/`~>`/fairness over `Pred Σ`) | exact (in-fragment) | Apalache bounded | **`bounded`** |
| same, small finite-state, liveness must be decided | exact | TLC explicit | **`discharged`** (exhaustive on that model) |
| **Alternating `μ`/`ν`** with no LTL image | **not projectable** | — | route: another checker / Keep monitor / flagged (§21) |

Two honest consequences fall out of this map:

- **"In-fragment" is not "decided."** An LTL formula projects `exact` (the
  *formula* is faithfully expressed), but bounded-symbolic Apalache still only
  decides it **up to `k`** — fidelity is about expressiveness, the bound is a
  separate axis recorded here. Only the inductive and exhaustive-TLC modes
  convert `exact` into `discharged`.
- **The `μ`-calculus gap is routing, not silence.** A `not projectable`
  obligation is never a green and never a drop; it is routed per §21 and
  recorded as routed in the attestation.

## The two verdicts and their honest form

- **Counterexample found (red).** A definite disproof within the model: the
  obligation `T` does **not** hold, and the ITF trace is a concrete witness
  (finite, or a **lasso** for a liveness violation — §31). This is unconditional
  regardless of mode — a bug in the code or the assumptions — routed to §52
  (does the running system reproduce it?) and §40 (a seed test case).
- **No counterexample (green).** Its strength is the mode's: an **inductive** or
  **exhaustive-TLC** green is a *decision* and may be recorded as `discharged`
  (§23, decidable ⇒ classical/intuitionistic coincidence); a
  **bounded-symbolic** green is *evidence, not proof*, recorded `bounded` with
  the bound explicit — depth `k`, abstraction, instance count, and, for
  unbounded liveness, the standing note that coverage is `N ≤ k`, never all `N`
  (ken `72 §8`, the revisit trigger).

## The one-way gate applies here

Neither verdict re-enters Ken as a proof term — not even an inductive
`discharged`. A decision is stronger *evidence*, but it remains a classical
result about the generated model; the obligation stays `delegated` (§11 I4; ken
`71 §5.1`). The discharge attestation records *that* the check ran, *which*
Quint/Apalache/Z3 versions ran it (§31), *which mode* and *what* bound it
reached, and *what* it found — never a promotion to `Q` (§12; ken
`OQ-discharge-attestation`). This is §23's residual-gap ledger made specific to
L1.

## Ward design deliverables

- **The bound/depth + mode policy.** How `k`, abstractions, and instance counts
  are chosen per obligation class; when to escalate depth; when to switch mode
  (bounded → inductive when a `Q` strengthening is available per §32; bounded →
  TLC when a small-model liveness needs deciding). State the default and the
  knobs.
- **The recording schema (into §12).** What a model-check discharge contributes
  to the attestation: verdict, **mode**, bound, checker + Ward versions, model
  hash (bound to the export hash, §22), and the honest coverage statement. This
  is one of the "what counts as a discharge per engine" pieces §12 explicitly
  defers to the engines — and the `mode` field is what lets a consumer tell a
  bounded outcome from a decided one.
- **Counterexample routing.** The ITF trace (finite or lasso) → §52
  (trace-conformance oracle: does the running system exhibit it?) and → §40 (a
  concrete seed for test generation). A counterexample is reused, never
  discarded.
- **The unbounded-liveness escalation.** For properties where `N ≤ k` is
  load-bearing and no inductive/exhaustive decision is reachable, the principled
  response is **not** a stronger classical claim; it is the contained reflective
  model in Ken (ken `72 §8`), Keep's runtime monitor (§21), or a documented
  standing assumption. Record which, per obligation — never silently upgrade.

Resolver: `OQ-discharge-attestation` (the recording half, coordinated with §12)
and `OQ-model-target` (the fragment/mode/bound half). §90.
