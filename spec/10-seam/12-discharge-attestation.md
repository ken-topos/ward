# 12 — The discharge attestation

> Status: DRAFT v0 — **Ward-designed.** Ken pins the *constraints* below and
> defers the *shape* to Ward (`OQ-discharge-attestation`, Ward-blocked; ken
> `spec/60-security/63-supply-chain.md §5a`, referenced from `65-policy.md §5a`
> and `71 §3.3/§5`). This chapter is the **primary open design deliverable** of
> the seam section.

## What it is

The **classical-discharge record**: when Ward discharges an exported obligation
(`T`) or assumption (`P`) by classical means — a model-check result, a passing
test campaign, a clean monitor window — the fact "this was discharged, this way,
against this Ken export" is recorded here. It is the anchor of the seam's
**translation-faithfulness** argument (§20): Ken proves `⟦φ⟧ = ⟦compile φ⟧`
*relative to an axiomatized Ward semantics*; that **Ward implements** that
semantics is the one explicit, version-bounded assumption — pinned here.

## Ken-side constraints (fixed — Ward designs within them)

- **Binds 1:1 to the export hash.** A discharge is recorded against the
  content-hash of the `Q/P/Σ/T/G` contract it answered — `export_hash ↔
  discharge` is reproducible, mirroring `export_hash ↔ build` in provenance.
- **Pins the Ward version.** The faithfulness proof is relative to an axiomatized
  Ward semantics; the attestation records *which* Ward (version-bounded) produced
  the discharge — the load-bearing trust edge, not bureaucracy.
- **Never promotes.** A discharged obligation stays `delegated`/`tested` in Ken's
  four-way status and re-enters the Ken side only as a `trusted_base_delta` /
  attestation record — **never** a certificate the kernel re-checks (§00, the
  one-way gate; ken `71 §5.1`).
- **Carries the runtime CT verdict.** The runtime constant-time validation (§13)
  is emitted **into** this attestation.

## Open design questions (Ward)

- **Shape + schema** of the attestation record: fields, serialization, and how it
  composes with Ken provenance / the supply-chain attestation of ken `63`.
- **What counts as a discharge** per engine — the evidence a model-check (§30), a
  test campaign (§40), and a monitor window (§50) each contribute, and how
  bounded/partial results (depth-k, sampled, finite window) are recorded honestly
  without over-claiming.
- **Attestation lifecycle** — issuance, revocation on export-hash change,
  aggregation across the obligations of one export.
- Resolver: `OQ-discharge-attestation` (this section). Coordinate the record's
  Ken-visible fields back through ken `63 §5a`; own the rest.
