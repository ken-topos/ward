# 50 — L3: runtime verification, trace conformance, agentic envelope

> Status: DRAFT v0 — open design. The third engine (§30–§50), and the one that
> touches the **running** system. It consumes the seam's `T` and `Σ` (§10), the
> trace/instrumentation contract Ken emits (ken `73`), and the L1 model (§30);
> it feeds the discharge attestation (§12). References:
> `local/refs/{rv-monitor, spot}` (spot is ⚠ GPL — read for approach only,
> `CLEAN-ROOM.md`).

L1 checks a model and L2 samples the code offline; L3 watches the **deployed
system** and answers the questions only runtime can: does the live execution
stay within the verified model, and does an embedded agent stay inside its
verified envelope? Ward is careful about what "watch" buys — a green monitor
window is evidence for a `delegated` obligation, never a proof (§23, the one-way
gate) — but within that honesty it is what makes liveness, ordering, and agentic
safety *observable in production*, in the model's own vocabulary.

- [`51-monitor-synthesis.md`](51-monitor-synthesis.md) — monitors from `T` (LTL
  → Büchi, MOP-style) over `Σ`, and the instrumentation contract that couples to
  ken `73`.
- [`52-trace-conformance.md`](52-trace-conformance.md) — does the running
  implementation **refine** the L1 model? Closes the loop to §30 and keeps the
  generated model honest against reality.
- [`53-agentic-envelope.md`](53-agentic-envelope.md) — the embedded agent as a
  nondeterministic oracle inside a verified FSM; safety-not-quality; realizes
  ken's four-row status partition. `OQ-agentic-oracle`.
