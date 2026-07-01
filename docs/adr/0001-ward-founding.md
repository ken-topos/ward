# ADR 0001 — Ward: founding and relationship to Ken

- **Status:** Accepted
- **Date:** 2026-07-01
- **Deciders:** the operator

## Context

Ken proves the static/propositional fragment of a topos-internal logic. What
escapes — time/ordering, concurrency/distribution, the assumed environment,
nondeterminism, and agentic outputs with no propositional oracle — is delegated
to a sibling (Ken ADR 0006: "one logic, two engines"). That sibling is **Ward**.
This ADR founds Ward's repository and fixes its relationship to Ken.

## Decision

1. **Ward is a separate MIT project**, coupled to Ken **only** by the generated
   assumption-boundary export. No shared build, no shared kernel; the coupling is
   one artifact, one-way.
2. **The Ken seam is fixed input.** Ward consumes ken `spec/70-behavioral`'s
   contract (the five-field export, its invariants, content-hashing) as given;
   changes are **coordination requests back to Ken**, never local redesigns.
3. **Ward owns what Ken defers to it:** the discharge attestation
   (`OQ-discharge-attestation`) and the runtime CT validation, plus the sampling
   policy (`OQ-sampling-policy`), its own engines (L1/L2/L3), the translation, and
   the implementation stack.
4. **One-way flow is inviolable.** No Ward result is ever promoted to a Ken
   `proved`; a discharge is a lower-trust, version-pinned artifact bound to the
   export hash.
5. **Fresh clean design, not a port.** Ward derives from Ken's public interface
   and first principles; it reads external verification tools (Quint/Apalache/
   MOP) for behavior-not-expression under `CLEAN-ROOM.md`, integrating them as
   licensed dependencies, never copied source.
6. **Design campaign now.** Ward begins at the design stage, run by an agent
   federation mirroring ken's (`agent/`), producing a normative spec + ADRs
   before any implementation.

## Consequences

- Ken stays small and static; the classical machinery lives in Ward, outside the
  kernel TCB.
- The two-artifact drift risk is bounded: Ward's model is *derived from* Ken's
  export and kept honest by trace conformance.
- Ward's implementation stack is deliberately **undecided** here
  (`OQ-ward-stack`) — a design output, not a founding premise.

## References

Ken ADR 0006 (the sibling decision); ken `spec/70-behavioral/` (Ken's half of
the seam); ken `spec/60-security/{63,65}` (discharge attestation + CT).
