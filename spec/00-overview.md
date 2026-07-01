# Ward — overview

> Status: DRAFT v0. The thesis and section map are stable; every downstream
> section is design-stage.

## Thesis

Ken proves a **closed, deterministic, total, static** world propositionally. The
behaviors that escape that net — time and ordering, concurrency and
distribution, the assumed environment, nondeterminism, and agentic outputs with
no propositional oracle — are not foreign to Ken: the world of
behaviors-over-time is itself a topos (Schultz–Spivak, *Temporal Type Theory* —
behavior types are sheaves over time). Ward occupies the **temporal/modal**
fragment of the same topos-internal logic that Ken occupies the
**static/propositional** fragment of.

**One logic, two engines** (Ken ADR 0006). A property means the same thing on
both sides; it is written once, in Ken, and Ward discharges the half Ken
delegates — by **model → test → monitor**, with classical machinery that stays
**outside** Ken's kernel TCB.

## The seam (one-way)

Ward's only coupling to Ken is the **assumption-boundary export** (§10): a
generated, content-addressed assume-guarantee contract. Ward discharges the
obligations it carries by classical means; **results never re-enter Ken as proof
terms** — a depth-k model-check is not a proof for all N, and a green monitor is
not a proof. A discharged obligation stays `delegated`/`tested`, recorded in the
**discharge attestation**, never promoted to `proved`. This one-way flow is a
soundness *and* a legibility commitment.

## Guarantee levels

- **Ken** certifies `Q` **given** `P` — intuitionistic, kernel-checked: the
  static half.
- **Ward** discharges `P` and `T` **classically** — model-checked / tested /
  monitored: a **lower-trust, version-pinned** artifact bound to the Ken export
  it answered (the discharge attestation, §10). Where an obligation is decidable
  / finite-state, classical and intuitionistic truth coincide; where unbounded,
  it is an honest assumption.

## The engines (§30–§50)

- **L1 model checking** — check exported obligations `T` against guarantees `Q`
  (assumed, not re-proved) and assumptions `P` (the environment).
- **L2 test generation** — Ken's refinement types are generators *and* oracles
  (`G`); Ward adds the per-deployment **sampling policy** (the measure Ken never
  supplies) and metamorphic testing for oracle-free outputs.
- **L3 runtime verification** — monitors synthesised from `T`, **trace
  conformance** (does the implementation refine the model?), and the **agentic
  safety envelope**.

## Scope / non-goals

- **In scope:** consuming the Ken export; the `τ` translation and its
  faithfulness; the L1/L2/L3 engines; the discharge attestation and runtime CT
  validation Ken defers to Ward; the sampling policy.
- **Not in scope:** changing Ken's kernel or re-importing classical strength
  into it; specifying Ken's *half* of the seam (that is ken
  `spec/70-behavioral/`). Ward's implementation stack was a **design output**,
  now decided in §01 (`OQ-ward-stack`): **two siblings** — Ward (offline
  discharge: L1+L2) and **Keep** (runtime: L3) — over a shared seam crate, on a
  Rust substrate.
