# 33 — Bounded results and honest recording

> Status: DRAFT v0 — open design. This is where L1 meets the one-way gate (§23)
> and the discharge attestation (§12). Resolver: `OQ-discharge-attestation`
> (shared with §12), `OQ-model-target`.

## What it is

A model check is almost never "proof for all `N`." It is exhaustive **up to a
bound** — depth `k`, a data abstraction, a finite instance count. The result is
genuine, strong evidence, but it is *bounded* evidence, and the single most
important thing L1 does is record that bound **honestly**: a depth-`k` green
result must never be filed as if it decided the obligation for all `N`. This
chapter fixes the bound policy and the recording contract.

## The two verdicts and their honest form

- **Counterexample found (red).** A definite disproof within the model: the
  obligation `T` does **not** hold, and the ITF trace is a concrete witness.
  This is unconditional — a bug in the code or the assumptions, routed to §52
  (does the running system reproduce it?) and §40 (a seed test case).
- **No counterexample within the bound (green-up-to-`k`).** Evidence, **not**
  proof. Recorded with its bound explicit: depth, abstraction, instance count,
  and — where the property is unbounded liveness — the standing note that
  coverage is `N ≤ k`, never all `N` (ken `72 §8`, the revisit trigger). Where
  the model is genuinely finite-state and the check is exhaustive, the result is
  a **decision** and may be recorded as such (§23, decidable⇒coincidence).

## The one-way gate applies here

Neither verdict re-enters Ken as a proof term. A green-up-to-`k` result is
evidence for a `delegated` `T`; it stays `delegated` (§11 I4; ken `71 §5.1`).
The discharge attestation records *that* the check ran, *which* Ward/checker
version ran it, *what* bound it reached, and *what* it found — never a promotion
to `Q` (§12; ken `OQ-discharge-attestation`). This is the honesty discipline of
§23's residual-gap ledger, made specific to L1.

## Ward design deliverables

- **The bound/depth policy.** How `k`, abstractions, and instance counts are
  chosen per obligation class; when to escalate depth; when to fall back to a
  different engine (§31, symbolic vs BMC). State the default and the knobs.
- **The recording schema (into §12).** What a model-check discharge contributes
  to the attestation: verdict, bound, checker + Ward version, model hash (bound
  to the export hash, §22), and the honest coverage statement. This is one of
  the "what counts as a discharge per engine" pieces §12 explicitly defers to
  the engines.
- **Counterexample routing.** The ITF trace → §52 (trace-conformance oracle:
  does the running system exhibit it?) and → §40 (a concrete seed for test
  generation). A counterexample is reused, never discarded.
- **The unbounded-liveness escalation.** For properties where `N ≤ k` is
  load-bearing, the principled response is **not** a stronger classical claim;
  it is the contained reflective model in Ken (ken `72 §8`) or a documented
  standing assumption. Record which, per obligation — never silently upgrade.

Resolver: `OQ-discharge-attestation` (the recording half, coordinated with §12)
and `OQ-model-target` (the bound/engine half). §90.
