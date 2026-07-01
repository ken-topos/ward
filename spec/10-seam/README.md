# 10 — The Ken interface (the seam)

> Status: DRAFT v0. This section is **anchored**, not open: it restates the
> Ken-side contract Ward is built against (the fixed input) and marks the pieces
> Ken explicitly defers to Ward to design. Authoritative source: ken
> `spec/70-behavioral/71-assumption-boundary.md` (the export IR) and ken
> `spec/60-security/{63-supply-chain.md §5a, 65-policy.md §2/§5a}` (the discharge
> attestation + CT). Where this section and those disagree, **ken wins** — flag
> it, do not silently diverge.

Ward couples to Ken through **one artifact**: the assumption-boundary export. The
coupling is deliberately narrow (a broadcast contract, not an API) and **one-way**
(§00). This section pins what Ward *consumes* and what Ward *owes back*.

- [`11-export-consumer.md`](11-export-consumer.md) — the five-field export IR
  `{Q, P, Σ, T, G}` + ITF traces, content-addressing, and the cross-field
  invariants Ward relies on. **Consumed.**
- [`12-discharge-attestation.md`](12-discharge-attestation.md) — the record that
  binds a classical discharge 1:1 to the export it answered, and pins the Ward
  version. **Schema + Ken contract FINALIZED** (`OQ-discharge-attestation`,
  pending ken ratification of the Ken-visible fields).
- [`13-ct-runtime.md`](13-ct-runtime.md) — the runtime constant-time validation
  Ken's static `@ct` face defers to the "Ward runner." **Ward-designed.**
