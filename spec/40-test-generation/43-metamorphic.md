# 43 — Model-based and metamorphic testing

> Status: DRAFT v0 — open design. Consumes L1 output (counterexamples/models,
> §33) and covers the oracle-free outputs §41 marks. Feeds the agentic envelope
> (§53). Resolver: `OQ-agentic-oracle` (DECIDED in ken — realized here) +
> `OQ-sampling-policy`.

## What it is

Two techniques that extend L2 past the reach of a direct oracle:

- **Model-based generation** — turn L1's model (§22) and its counterexamples
  (§33) into test cases against the *real code*. A counterexample is a concrete,
  high-value seed (it found a bug in the abstraction; does the implementation
  reproduce it?); the model's reachable transitions are a coverage target. This
  closes L1→L2: the model checker proposes, the test runner disposes against
  reality, and disagreement between them is itself a finding (model too
  abstract, or code diverges — §52).
- **Metamorphic testing** — the answer to outputs with **no propositional
  oracle** (§41's boundary): where you cannot say "this output is *correct*,"
  you can often say "these two *related* runs must have *related* outputs." A
  metamorphic relation checks that invariant across runs without ever deciding
  any single output — the oracle-free discipline Ken scopes out of its kernel
  and hands to the classical side (ken `74 §2.1`, the `tested`/`P` row;
  `OQ-agentic-oracle`, DECIDED scoped-out).

## Metamorphic relations are the oracle-free `tested` channel

Ken's status partition (ken `74 §2.1`) fixes exactly where this lands: an
oracle-free relational check across runs is **`tested`**, rides **`P`**, and is
**never `proved`** (the agent/output is `P`, not a pure function). Ward's L2
realizes that row — it does not invent a new assurance kind, it supplies the
classical machinery for the row Ken already partitioned. The relations are the
same shape whether the oracle-free output is a numeric routine or an embedded
agent's proposal; §53 points the identical mechanism at the agent.

## Ward design deliverables

- **The L1→L2 model-based path.** How a §33 counterexample becomes a concrete
  test against real code; how model coverage becomes a generation target; and
  how a model/code disagreement is recorded (it is a conformance signal, §52,
  not just a failed test).
- **The metamorphic-relation surface.** How relations are expressed over the `Σ`
  vocabulary and the refinement structure, how they are sourced (from Ken's
  relational/2-safety exports where present — ken `OQ-relational`; authored
  where not), and how they compose with the sampling policy (§42).
- **The oracle-free recording contract.** A metamorphic pass is `tested` under a
  measure, never `proved`; record it into the discharge attestation (§12) with
  its relation set, sampling policy, and Ward version — honestly bounded, like
  L1's green-up-to-`k` (§33).
- **The handoff to §53.** The agentic envelope is this same metamorphic +
  RV-watchdog machinery aimed at a maximally-nondeterministic component; keep
  the relation surface general enough that §53 reuses it rather than forking.

Resolver: `OQ-agentic-oracle` (§90; the relation/oracle-free semantics, DECIDED
in ken, realized here) and `OQ-sampling-policy` (the measure these relations
sample under).
