# Ward design principles — the reasoning charter

> **Audience: every agent working on Ward — especially Steward, design authors,
> and reviewers.** These are the *implicit* values of the project, made explicit.
> Ward inherits them from Ken — they are one project in two engines. **When the
> spec dictates an answer, follow it. When it does not, reason from these.** They
> are priors for judgment, not a substitute for it.

Each principle is stated, justified in a line, and shown *in practice*. Ward is
the **second engine** (Ken ADR 0006): most of Ken's charter carries over intact;
the few kernel-specific principles are reframed for Ward's role — the classical
discharge engine that lives *outside* Ken's trusted core.

---

## I. What Ward is — the mission

### 1. Ward is the engine for what proof cannot reach

Ken proves the static/propositional fragment; Ward discharges the temporal,
concurrent, empirical, and agentic fragment — by **model → test → monitor**. Its
job is to **discharge honestly what Ken delegated**, legibly for a
sufficiently-educated human, and to **say exactly how much it discharged** —
never more. Every other principle is downstream of this one.

*In practice:* a discharge is a **lower-trust, version-pinned** artifact bound to
the Ken export it answered (§10, `12`), carrying its real epistemic status; a
depth-k model-check or a green monitor is **evidence for a `delegated`
obligation, never a promotion to `proved`** (§00, the one-way gate).

---

## II. How to decide — the decision calculus

### 2. The agent-writes / human-reads asymmetry is a design axis

Writing is cheap (agents do it); reading and verification are dear (humans do
them, and they are the bottleneck). **Optimize the canonical, permanent form for
reading and checking**, and push cost onto the writing side.

*In practice:* Ward's models, monitors, and attestations are **read-optimized** —
the human's scarce attention goes to reviewing *what was discharged and how
strongly*, not re-deriving the model (which is generated from the export, §20).

### 3. Decide on intrinsic merits, not effort (feasibility is the only hedge)

Person-time and "amount of work to build" are the **wrong axis** — a team of
agents collapses effort that human priors treat as decisive. Weigh what is
permanent: **soundness of the seam, faithfulness of translation, legibility,
fitness for purpose**. The only risk that justifies a hedge is
feasibility/correctness, never effort.

*In practice:* pick the model-checking / monitor-synthesis approach that is
*correct and permanent* (a faithful `τ`, honest bounds), and hedge only genuine
feasibility doubt — not "that integration is more work."

### 4. Choose the correct / natural / elegant / permanent over the expedient

"Time to market" and "less to implement" are not Ward values. Prefer the choice
that will still be right in ten years and reads as inevitable.

*In practice:* a faithful, amortized-once `compile` lemma over a per-obligation
hack (§20); a derived-from-code model over a hand-authored parallel spec that can
silently drift (Ken ADR 0006).

---

## III. Design invariants — the recurring commitments

### 5. Keep the trust edges small, explicit, and version-pinned

Ward lives **outside** Ken's kernel TCB — so its discipline is not "small kernel"
but **honest, minimal, legible trust edges**. The faithfulness argument rests on
*one* explicit assumption — that Ward implements its axiomatized semantics —
pinned as the **Ward version in the discharge attestation** (§12). Name every
such edge; add none silently.

*In practice:* the attestation records *which* Ward produced a discharge (§12);
translation faithfulness is a Ken-checked lemma *relative to* an axiomatized Ward
semantics, and that gap is the one pinned edge (§20) — not buried, not assumed
away.

### 6. Derive, don't re-author

When tempted to hand-author a model, monitor, or alphabet, the Ward move is to
**generate it from Ken's export** instead. The model is a *function of the code*;
authoring drift is then impossible by construction.

*In practice:* `Σ` *is* the interaction-tree perform-node signatures — Ward
watches exactly the events Ken can emit, no second alphabet (§11); the transition
system is generated from the space semantics (§20); generators/oracles fall out
of `G`'s refinement support (§40). If your instinct is "write a parallel spec,"
first ask: *can this be projected from the export?*

### 7. Subsume, don't proliferate

Before adding a mechanism, check whether **already-decided machinery composes to
give it**. Concerns that are not Ward's belong at the right layer.

*In practice:* the agentic safety envelope = an FSM + metamorphic relations + RV,
no new logic (§50); the test *measure* lives in a deployment-adjacent sampling
policy, not in Ward's core (§40); the timing guarantee is the runtime CT runner
over Ken's exported `@ct` obligations, not a re-derivation (§13).

### 8. Be honest about the boundary — over-claiming is itself a failure

A behavioral engine that pretends to guarantees it does not have is a **security
risk**. State bounds, models, and assumptions plainly; tag every result with its
real epistemic status; **never promote `tested`/`delegated` to `proved`**. Prefer
**loud refusal over silent degradation**. This is Ward's central discipline.

*In practice:* a bounded model-check reports its bound, not "verified" (§30); a
sampled test campaign reports its coverage, not "exhaustive" (§40); a monitor
window is evidence, not a proof (§50); Ward assures agent-output *safety* (the
envelope holds), never *quality* — quality is `unknown`, explicitly outside the
boundary (§50). Every discharge carries its bound into the attestation (§12).

### 9. One logic, two engines — Ward is the second, the seam is one-way

Ken proves the static/propositional fragment; Ward discharges the
temporal/behavioral/runtime fragment. One assertion language, two engines, two
trust domains. Ward consumes a **generated, broadcast export** flowing **one way**
(Ken → Ward); results never re-enter Ken as proof terms — for legibility as much
as soundness.

*In practice:* Ward redesigns nothing on Ken's side of the seam — a needed change
is a **coordination request back to Ken**, not a local edit
(`docs/program/01-strategy.md`); the one-way gate is inviolable (§00). When a
concern is temporal, concurrent, empirical, or quality-judgmental, it is Ward's —
and the handoff is designed deliberately.

### 10. Predictability is a feature

The human reasoning about a discharge needs a **predictable substrate**. Favor
the option that makes behavior, coverage, and cost easy to predict over the one
that is cleverer or marginally faster.

*In practice:* a stated, legible bound/coverage model over an opaque heuristic; a
deterministic model translation over an ad-hoc one; ITF as the one shared trace
currency over a bespoke format (§11).

### 11. Safety is tier-1 and intrinsic

Ward owns security-relevant assurance Ken defers to it — the runtime
constant-time validation (§13) and the agentic safety envelope (§50). These are
first-class, built on the export Ward already consumes, not bolted on.

*In practice:* the `@ct` runtime face consumes Ken's exported `@ct` boundary
obligations (§13); the agentic envelope is the FSM the agent is a
nondeterministic oracle *inside*, assuring safety while quality stays honestly
`unknown` (§50).

---

## Working constraints (process, not philosophy)

- **Clean design, skeptical of inherited artifacts.** Design from the Ken
  interface + first principles; external verification tools (Quint/Apalache/MOP)
  are **understood and integrated under their license, never copied**
  (`CLEAN-ROOM.md`). Ward has no excluded prototype — but treat any inherited
  design candidate skeptically; it may reflect uncritical wandering, not a
  considered choice.
- **Record decisions where they live.** Genuine forks →
  `spec/90-open-decisions.md`; architecturally significant ones → an ADR under
  `docs/adr/`; the deciding authority is the operator. Wrap markdown at 80
  columns, and **use Mermaid for diagrams/charts** (fenced ` ```mermaid `), never
  ASCII art.

## How to use this document

When you face a choice the spec does not settle: identify which principles bear on
it, reason from them explicitly, and — if the choice is significant — record the
decision and cite the principle. If two principles seem to conflict, say so and
surface it; that tension is usually where a real fork lives, and it belongs to the
operator. Do **not** optimize for effort, speed-to-build, or writer convenience at
the expense of soundness, the honesty of the boundary, or the human reader.
