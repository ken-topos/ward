# 50 — L3: runtime verification, trace conformance, agentic envelope

> Status: DRAFT v0 — open design.

Three responsibilities:

- **Monitor synthesis** — monitors from the exported temporal obligations `T`
  (LTL → Büchi, MOP-style), over the `Σ` alphabet.
- **Trace conformance** — instrumented execution traces validated against the L1
  model: does the implementation **refine** the model? This closes the loop back
  to §30 and keeps the derived model honest against the running code.
- **The agentic safety envelope** — the LLM/agent as a **nondeterministic oracle
  inside a verifiable FSM**: Ward assures *safety* (the envelope holds), never
  *quality* (was the output "good"?). Metamorphic relations + RV watchdogs stand
  in where there is no propositional oracle.

**Ward design deliverables:** the monitor synthesiser and its `Σ`-event
instrumentation contract (coordinated with ken `73-conformance.md`); the
trace-conformance checker; the agentic-envelope FSM and its policy
(`OQ-agentic-oracle`, decided in Ken — realize it here); and how each feeds the
discharge attestation (§12).
