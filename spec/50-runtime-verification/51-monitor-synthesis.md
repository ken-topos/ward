# 51 — Monitor synthesis and the instrumentation contract

> Status: DRAFT v0 — open design. Consumes `T` (§11) via the monitor projection
> `compile : Temporal Σ → Monitor` (distinct from §21's L1 projection; ken
> `72 §6.3`, `73 §2.4`) and Ken's trace/instrumentation contract (ken `73 §2`).
> References: `local/refs/rv-monitor` (MOP), `local/refs/spot` (⚠ GPL, approach
> only). Resolver: `OQ-wardformula` (the monitor target) + `OQ-conformance`
> (DECIDED in ken — realized here).

## What it is

A **monitor** is an automaton that consumes a live event stream and reports
whether the temporal obligation `T` holds so far. L3's first job is to
synthesize these monitors from the exported `T` and run them against the
instrumented system. Two things are already fixed and Ward must not re-invent
them:

- **The formula is `T`** — the exported temporal obligations, compiled by the
  **monitor projection** `compile : Temporal Σ → Monitor` (LTL → Büchi / MOP).
  This is a *different function* from §21's `compile : Temporal Σ → WardFormula`
  (the L1 property target); they share the `Temporal Σ` source and the `Σ`
  alphabet, never an implementation (ken `72 §6.3`). Keep them distinct.
- **The vocabulary is `Σ`, and Ken emits the events** — Ken's trace/
  instrumentation contract (ken `73 §2`) concretizes `Σ` for a live system: one
  event per interaction-tree `Vis` firing, carrying effect symbol, op, argument,
  response, correlation keys, and sequence position (ken `73 §2.1`). Ward's
  monitor consumes *that* schema; it does not define its own event source.

Ken owns its half (observability in the model's vocabulary) and explicitly
leaves the checking mechanism to the downstream consumer — that is Ward (ken
`73` status; `OQ-conformance`, DECIDED: Ken provides the contract, "nothing
about the checking mechanism").

## Ward design deliverables

- **The monitor projection `Temporal Σ → Monitor`.** LTL → Büchi (read
  `local/refs/spot` for the construction's *approach* only — ⚠ GPL,
  `CLEAN-ROOM.md`) and/or MOP-style parametric monitors
  (`local/refs/rv-monitor`, MIT). Handle the `mu`/`nu` fixpoints and the
  finite-vs-ω semantics chosen for `WardFormula` (§21) — the monitor's
  acceptance must match that semantics.
- **The instrumentation-consumption contract.** Precisely how Ward ingests ken
  `73 §2`'s event schema: mapping the event's effect/op fields to `Σ` members
  (ken `73 §2.1`, invariant TC1 alphabet closure), using correlation keys to
  reconstruct per-space / global traces (ken `73 §2.2`), and ordering by the
  sequence index. Coordinate the wire spellings back to Ken (ken `73` leaves
  them `(oracle)`-tagged — Ward finalizes the token, like §11).
- **The sidecar/topology question.** Ken notes the runtime monitor is likely a
  **distinct sidecar** (ken `73` / `OQ-conformance`). Design the monitor's
  deployment shape, its coupling to the running system, and its overhead budget
  (instrumentation is bounded to the single `Vis` site — ken `73 §2`).
- **Verdict semantics + recording.** What a monitor reports (satisfied /
  violated / inconclusive-so-far over a finite window), and how a **clean
  monitor window** is recorded in the discharge attestation (§12) — a green
  window is evidence for a `delegated` `T` over *that window*, never a
  promotion, and never a claim about windows unseen (§23; ken `73 §2.6` TC5).

Resolver: `OQ-conformance` (Ken's contract, realized here) + `OQ-wardformula`
(the monitor target). §90.
