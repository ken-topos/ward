# 13 — Runtime constant-time validation (the Ward runner)

> Status: DRAFT v0 — **Ward-designed.** Ken lands the *static* `@ct` face and
> defers the *runtime* face to Ward (ken `spec/60-security/65-policy.md §2/§5a`,
> `61 §5a`; resolver `OQ-discharge-attestation`).

## The split

A constant-time requirement ("`KeyMaterial` is `@ct`") has **two faces**:

- **Static face — Ken's, landed.** A `@ct` value steering a branch / index /
  variable-time operation is a **compile error** (the `l_ct_sink` leakage-sink
  discipline). A *necessary precondition*, checked before anything runs.
- **Runtime face — Ward's.** The static face cannot certify a *timing* guarantee:
  that is hardware- and codegen-relative and lives **below** Ken. Ward's runner
  **validates constant-time behavior at runtime** and emits the verdict into the
  discharge attestation (§12).

## Ward's obligations here

- Design the **CT validation method** (statistical timing analysis / dudect-style
  tests / hardware-assisted checks) and its trust story — what it can and cannot
  certify, and against which threat model (ken `64 §4.2`).
- Consume the `@ct` **boundary obligations** Ken exports (they ride the `P`
  channel, ken `71 §2.1`) so the runner knows *what* must be constant-time.
- **Emit into §12**: the CT verdict is a discharge — version-pinned, bound to the
  export hash, never promoted to a Ken `Q`.
- Resolver: `OQ-discharge-attestation`.
