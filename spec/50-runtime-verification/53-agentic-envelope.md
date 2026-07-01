# 53 — The agentic safety envelope

> Status: DRAFT v0 — open design. `OQ-agentic-oracle` is **DECIDED in Ken**
> (scoped out of the kernel: assuring an embedded agent is *nothing new* — it is
> the existing seam aimed at a maximally-nondeterministic component; ken `74`,
> ADR 0006). This chapter **realizes** the runtime faces of that reduction; it
> does not add an agentic mechanism. Resolver: `OQ-agentic-oracle`.

## What it is

A Ken-built system may **embed an agent** at runtime — an LLM proposes a plan, a
query, a tool call, a config. Such outputs have **no propositional oracle**: you
cannot write a spec that says "this generated summary is correct." The reframe
that dissolves the problem (ken `74 §1`): you do not trust the agent, you verify
the **boundary**. An embedded agent is just a **maximally-nondeterministic
input** — the strongest case of an assumption `P` (§11; ken `74 §2`) — and Ken
already proves systems safe *for all* values of `P`. So agentic assurance
**reduces to the existing seam** pointed at the most adversarial environment,
and Ward's L3 supplies the runtime machinery for that reduction's faces.

The discipline, stated once: **Ward assures *safety* (the envelope holds), never
*quality* (was the output good?).** Quality is oracle-free and honestly
declined; safety is a proved invariant over all agent outputs, watched at
runtime.

## The four-row partition (Ken fixes it; Ward realizes the runtime faces)

Ken's status partition (ken `74 §2.1`) is **total** — every assurance question
about an agent output is one of four rows — and it does **not leak** (the
one-way gate forbids promotion). Ward does not invent a fifth kind; it realizes
the runtime faces of the rows that are classical:

| Assurance | Status | Field | Ward's role (this chapter) |
|---|---|---|---|
| **Safety envelope** — invariant proved for *all* outputs | `proved` | `Q` | **none to prove** — Ken's kernel already did (caps + obligation → `Q`); Ward *watches* it holds live (§51) |
| **Metamorphic relation** — oracle-free relational check | `tested` | `P` | realize the relation runtime (§43 machinery, aimed at the agent) |
| **RV watchdog** — temporal obligation over agent actions | `delegated` | `T` | synthesize + run the monitor (§51) over the agent's `Σ`-events |
| **Output quality** — "the summary is faithful" | `unknown` | *(hole in `P`)* | **not dischargeable** — honestly declined (§ below) |

The envelope's `proved` guarantee is Ken's — the agent holds only `propose` (no
ambient authority; a `view` with no `Cap E` is inert by its type), a **verified
validator** holds `act`, and the safety invariant is proved quantified over the
agent output without inspecting it (ken `74 §2.2`/`§3`). Ward's L3 does not
re-prove that; it **instruments and watches** the envelope so a violation of the
runtime assumptions (the validator's gate, the capability confinement) is caught
as it happens.

## Ward design deliverables

- **The envelope's runtime instrumentation.** How Ward observes that the agent
  stayed confined to `propose` and the validator held `act` — the `Σ`-events
  (ken `73`) that witness the capability boundary, and the RV watchdog (§51)
  over the agent's action stream (the `delegated`/`T` row).
- **The metamorphic runtime for agent outputs.** Point §43's relation machinery
  at the agent: the `tested`/`P` row — related prompts must yield related
  outputs, sampled under a policy (§42). Reuse §43's surface; do not fork it.
- **The honesty guard (the fourth row).** A mechanism that makes "quality is
  `unknown`, not dischargeable" **explicit and non-bypassable** — Ward must
  never let a green metamorphic run or a clean monitor window read as a
  *quality* verdict (ken `74 §6`, the honesty line; §23 one-way gate). This is
  Ward's central discipline (`docs/PRINCIPLES.md`) at its sharpest: the envelope
  is assured, the output's goodness is declined, and the two are never
  conflated.
- **The attestation shape for an agent-bearing system.** How the three
  dischargeable rows (proved-watched / tested / delegated) plus the honest
  `unknown` compose into one discharge attestation (§12) for a deployment — so a
  reader sees exactly which assurances hold and which are honestly absent.

## What is NOT open here

That the agent reduces to `P`, that the four rows are the whole story, and that
nothing promotes are **Ken's** decisions (`OQ-agentic-oracle`, DECIDED; ADR
0006). Ward builds no new agentic primitive — if a design finds itself inventing
one, it has mis-scoped (ken `74` banner). Ward realizes the runtime faces and
the honesty guard. Resolver: `OQ-agentic-oracle` (§90).
