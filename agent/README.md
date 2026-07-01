# Ward agent workflow

Ward is designed by a federation of agent teams coordinating through
[mootup](https://mootup.io) spaces and GitHub PRs. This directory holds the
behavioral discipline those agents follow, in three tiers — the structure exists
because the moot tooling provisions skills as **per-team copies with no
inheritance**, so cross-team sharing has to be deliberate.

Ward is Ken's behavioral-assurance sibling (Ken ADR 0006, *one logic, two
engines*). Ken proves the static/propositional/intuitionistic fragment of a
topos-internal logic; Ward discharges the temporal/modal fragment of the **same**
logic — liveness, fairness, concurrency/distribution, the environment a proof
assumed, nondeterminism, and agentic outputs with no propositional oracle — by
**model → test → monitor**. Ward is at the **design stage**: a design campaign,
the analog of Ken's spec campaign.

## The three tiers

1. **`COORDINATION.md`** — the federation-wide law every agent reads, regardless
   of team or model. The shared substrate. **Read it first.**
2. **`playbooks/`** — **archetype sources**, one skill per role per archetype.
   This is the shared-innovation layer; the Steward promotes validated lessons
   into it.
   - `design/` — `leader`, `author`, `reviewer` (the design enclave; grounds
     Ward's design in the **Ken interface** — the assumption-boundary export and
     ADR 0006 — and first principles).
   - `federation/` — singletons: `steward`, `architect`, `integrator`,
     `librarian`.

   There is **no `build/` tier yet.** Ward is at the design stage; when
   implementation begins (the L1/L2/L3 engines), a `build/` archetype
   (`leader`, `implementer`, `qa`) is added the way Ken's was.
3. **`teams/<team>/<role>.md`** — **per-team overlays** (created when a team is
   instantiated). Thin deltas that reference the archetype and add only that
   team's specifics. Where local refinement and retros land first.

`MODELS.md` records the model tier for every role (Opus 4.8 1M / DeepSeek V4 Pro)
and the provider-routing rules. There is no build-model tier during the design
campaign.

## The Ken interface (Ward's grounding, not a prototype)

Ward is a **fresh, clean MIT design** — it is **not** derived from any AGPL
prototype, so there is **no excluded-inspiration clean-room**. What replaces it
is a positive grounding: Ward designs from **Ken's public seam** — the
generated, content-addressed assumption-boundary export (the five-field
`Q`/`P`/`Σ`/`T`/`G` assume-guarantee contract plus ITF traces, Ken
`spec/70-behavioral/71-assumption-boundary.md`) and ADR 0006 — and from first
principles. The seam is **strictly one-way** (Ken → Ward; conclusions never feed
back). Ward reads copyleft **verification tools** (Apalache, Quint, MOP, RV
frameworks) for **behavior, not expression** — never copying their source into
Ward's own MIT tree. That originality discipline is the design enclave's, and is
described in the enclave playbooks.

## Promotion ladder (how the tiers stay coherent)

Lessons flow **up**, curated by the Steward: team overlay → archetype source →
`COORDINATION.md`, promoted only when validated across ≥2 teams (or ≥3 runs),
model-/operator-agnostic, and normative. This is the inheritance the tooling
lacks. See `playbooks/federation/steward.md` and `COORDINATION.md §10`.

## Provenance

These patterns are lifted and adapted from **Ken's federation law**, which was
itself lifted from the workflow skills the mootup team developed by dogfooding
mootup on a different (SaaS) codebase — the coordination nuance transfers;
Ken-build-specific and SaaS/tooling specifics were dropped or re-grounded on
Ward's design campaign. The git model will live at
[`04-git-and-integration.md`](../docs/program/04-git-and-integration.md); roles
map to workstreams in
[`03-program-of-work.md`](../docs/program/03-program-of-work.md).
</content>
</invoke>
