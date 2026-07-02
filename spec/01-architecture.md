# 01 — Architecture (the Ward stack)

> Status: **DECISION SET (2026-07-01, operator).** Resolves `OQ-ward-stack`.
> Fixes the top-level shape: **two sibling tools** (Ward + Keep) over a shared
> seam crate, a **Rust** substrate, an **orchestrator** posture with a minimal
> trusted core, and honest version transparency over the orchestrated toolbox.
> Opens `OQ-deployment-seam` (below). §00 deferred the stack here; this is that
> design.

## Two siblings, split by operational context

The behavioral-assurance work divides cleanly along **operational context**, and
the two halves have different responsibilities, constraints, and lifetimes — so
they are **two tools, not one wearing two hats** (the same instinct that made
the runtime monitor "a distinct sidecar" in ken `73`):

| Tool | Context | Owns | Engines |
|---|---|---|---|
| **Ward** | offline / CI / build-time | consume the export, discharge obligations, **emit + sign the attestation** | **L1** model-checking (§30), **L2** test-generation (§40), the regression corpus (§44), offline CT verification (§13) |
| **Keep** | runtime / production / sidecar | watch the running system, hold it inside the verified envelope, contribute runtime verdicts | **L3** monitors + trace conformance (§50–§52), runtime timing monitoring (§13), the agentic envelope (§53) |

The family: **Ken** proves (static, in-kernel), **Ward** model-checks (offline,
outside the kernel TCB), **Keep** holds the line (runtime). Each is a separate
tool; L2's *offline* context puts it with Ward (it invokes the real code in CI
and contributes to the build-time attestation — it has no production existence),
while only L3 is genuinely production-resident.

### The shared crate — the seam

Both siblings consume the same Ken export and contribute to the same
attestation, so the shared functionality is exactly the **seam**, factored into
one crate both depend on and neither re-implements:

- the export consumer + its invariants (§11), the `Σ` / ITF trace types,
- the `τ` translation and `WardFormula` (§20),
- the discharge-attestation library + the obligation-identity key (§12, §44),
- the metamorphic-relation surface (§43) that Keep's envelope (§53) reuses.

Ward and Keep are thin tools over this crate. (Keep **has now split to its own
repository**, `ken-topos/keep` — Keep ADR 0001, 2026-07-02: §50, §53, and the
runtime face of §13 (as keep §54) migrated there; Ward's §50 is a pointer. The
seam and translation stay normative here, referenced by Keep.)

## Substrate — Rust now, Ken later

Ward and Keep are **Rust**: it matches Ken (`crates/ken-*`) for export/ITF
interop, carries the sigstore/in-toto tooling the attestation needs (§12), and
gives Keep a low-overhead sidecar. The **north star** is self-hosting — ideally
Ken and Ward are eventually written *in Ken* — but that is solidly future, and
Rust is likely to remain **Ken's bootstrap** regardless, so it is the pragmatic
*and* durable substrate, not a temporary crutch.

## Orchestrator posture and the trusted core

Ward is an **integrator, not a reimplementer** (`CLEAN-ROOM.md`: integrate under
licence, never vendor). It orchestrates a **polyglot best-of-breed toolbox** —
Quint/Apalache (JVM), a PBT engine, dudect (C harness), Binsec/Rel (OCaml), spot
(GPL) — as **invoked, version-pinned processes**, never linked into the core.
This has a load-bearing consequence for trust:

> The credibility of `ward.version` rests on a **small trusted core** — the
> orchestration + attestation + signing layer — sharply separated from the
> **large invoked toolbox** that produces evidence. The sharper that line, the
> more the attestation means.

Invoking (not linking) also keeps copyleft tools (spot, Binsec) out of the MIT
core by construction (they run as separate processes), satisfying clean-room.

### Orchestrated tools are clean-room-replaceable, not permanent

Today's polyglot integration is a **starting posture, not a commitment**. Where
a tool becomes a trust or licensing liability, it is a **replaceable
component**: the precedent stands that Ken's kernel was clean-roomed by an agent
team in ~36 hours, so a Ward-native equivalent of a problematic dependency is a
tractable future move, not a research program. The copyleft references (spot,
Binsec) are **approach-sources**, never load-bearing dependencies.

## Version identity and honest transparency

`ward.version` is *the* load-bearing trust edge in the attestation (§12, §23),
so the stack must define what a version **is** and record it **honestly**:

- **A Ward version** = the runner build **+** the pinned versions of every
  orchestrated tool **+** the axiomatized-semantics version the faithfulness
  argument is relative to (§23).
- **Full transparency includes the orchestrated components** — and some (third-
  party tools) will **not** have reproducible builds. Ward does **not** solve
  that; instead, like Ken, the attestation **says what is assured and what is
  not (or cannot be)**: it enumerates each orchestrated component + version +
  **reproducibility status**, and flags the non-reproducible ones rather than
  laundering them into a false guarantee. Honesty over the tool supply chain is
  the same discipline as honesty over bounds (§33) and CT (§13).

This is Ward-internal detail in the attestation (rides the `policy` / a
`toolchain` field of §12); it changes **nothing** in the ratified Ken-visible
contract.

## The deployment seam (`OQ-deployment-seam`, opened here)

The attested artifact must be checked *by the deployment environment* — a
**third seam**, `(Ken+Ward) ↔ deployment`, shaped like the Ken–Ward seam. The
landscape:

- **Transport + signature verification + admission control already exist and are
  mature** — sigstore **policy-controller**, cosign **`verify-attestation`**,
  **Kyverno**, **OPA/Gatekeeper**, Google **Binary Authorization**. Because §12
  chose **in-toto/sigstore**, Ward's discharge attestation is already a
  first-class citizen of this ecosystem.
- **What does not exist is the *semantics*** — no off-the-shelf checker
  understands "were these obligations discharged, at what assurance level, is
  that sufficient for this environment." That policy vocabulary is the
  research-to-industry gap.

So Ward does **not** build a deployment checker. It **brings the vocabulary**:
publish the discharge **predicate schema** + a **reference OPA/Rego (and/or
Kyverno) policy**, so existing admission controllers enforce the §12 gate (which
is Ken's). `OQ-deployment-seam` owns the predicate schema + the reference
policy; the enforcement mechanism is off-the-shelf.

## What this section fixes vs leaves open

- **Fixed:** the two-sibling split (Ward offline / Keep runtime) over a shared
  seam crate; Rust substrate with a Ken self-hosting horizon; the orchestrator +
  minimal-trusted-core posture; clean-room-replaceable tools; honest toolchain
  transparency in the attestation; the deployment-seam approach.
- **Open:** the component decomposition inside each sibling; the exact toolbox
  selection (couples to `OQ-model-target`, `OQ-ct-assurance`); the
  `OQ-deployment-seam` predicate schema + reference policy; the eventual Keep
  repository split.
