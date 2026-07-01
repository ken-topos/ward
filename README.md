# Ward

**Ward** is Ken's behavioral-assurance sibling: the engine for what proof cannot
reach. Ken (`ken-topos/ken`) proves the **static, propositional, intuitionistic**
fragment of a topos-internal logic; Ward discharges the **temporal/modal**
fragment of the *same* logic — liveness, fairness, concurrency and distribution,
the environment a proof assumed, nondeterminism, and agentic outputs with no
propositional oracle — by **model → test → monitor**.

> One logic, two engines. A property means the same thing whether Ken proves it
> or Ward monitors it. The contract is written once, in Ken; Ward discharges the
> half Ken delegates. (Ken ADR 0006.)

## Status

**Design campaign.** Ward is at the design stage — this repo holds the design
scaffolding, the Ken-side interface Ward is built against, and the campaign
plan. There is no implementation yet: the implementation stack is a design
*output*, not a premise.

## The seam

Ward couples to Ken through **one artifact** — Ken's *assumption-boundary
export*, a generated, content-addressed assume-guarantee contract with five
fields:

| Field | Carries | Ward uses it as |
|---|---|---|
| `Q` guarantees | proved postconditions / space invariants | facts the model may **assume**, not re-prove |
| `P` assumptions | the assumption boundary (`trusted_base_delta`, `assume`s) | the nondeterministic **environment** |
| `Σ` alphabet | interaction-tree perform-node signatures | the **event alphabet** of the model + monitor |
| `T` obligations | `Temporal` (LTL/μ-calculus) values in Ken source | the **properties** to model-check and monitor |
| `G` generators | refinement-type support (partition/boundaries) | **test generators + oracles** (never a measure) |

The seam is **one-way**: Ward discharges obligations by classical means, and
results never re-enter Ken as proof terms (a green model-check or monitor is not
a proof). Ken stays small and static; Ward carries the classical machinery
outside the kernel TCB.

## The engines

- **L1 — Model checking.** Quint + Apalache (typed, effect-aware, SMT-backed):
  check `T` against `Q`/`P`.
- **L2 — Spec-driven test generation.** Ken's refinement/dependent types are
  generators *and* oracles (`G`); plus a per-deployment **sampling policy** (the
  measure Ken never supplies) and metamorphic testing for oracle-free outputs.
- **L3 — Runtime verification.** Monitors synthesised from the exported temporal
  obligations (LTL → Büchi, MOP-style), the **agentic safety envelope** (the
  agent as a nondeterministic oracle inside a verifiable FSM), and **trace
  conformance** (does the implementation refine the model?).

Ward also owns the **discharge attestation** — it binds a classical discharge
1:1 to the Ken export content-hash and pins the Ward version — and the **runtime
constant-time (CT) validation** Ken defers to it.

## Map

- [`spec/`](spec/) — Ward's design/spec sections. `10-seam` is the Ken
  interface; `20`–`50` are the engines. [`spec/00-overview.md`](spec/00-overview.md)
  is the thesis; [`spec/90-open-decisions.md`](spec/90-open-decisions.md) logs
  the forks.
- [`docs/`](docs/) — `adr/` (decisions), `design/` (exploration), `program/`
  (the design campaign: strategy, roadmap, work packages).
- [`agent/`](agent/) — the agent federation: `COORDINATION.md` (law),
  `MODELS.md` (tiers), `playbooks/` (roles).
- [`conformance/`](conformance/) — Ward's own conformance corpus (later).

## Relationship to Ken

Ward is a **separate project**, joined to Ken only by the generated export
artifact — so it cannot silently drift: its model is *derived from* Ken's export,
and trace conformance keeps it honest continuously. Ken specifies **its half** of
the seam in ken `spec/70-behavioral/`; Ward specifies **its own internals** here.

Named for "to keep watch / guard": Ward watches what proof cannot reach — what
lies beyond Ken's ken.

## License

MIT — see [LICENSE](LICENSE). Ward is a fresh clean design; see
[CLEAN-ROOM.md](CLEAN-ROOM.md) for its reference-tools discipline.
