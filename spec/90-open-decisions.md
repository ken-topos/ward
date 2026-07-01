# Ward — open decisions

> Status: living. Ward's design forks and their resolution log, mirroring ken
> `spec/90-open-decisions.md`. Two OQs arrive **pre-assigned** to Ward from Ken
> (Ward-blocked there); the rest are Ward-internal, opened by the campaign.

## Inherited from Ken (Ward-blocked in ken)

| OQ | Question | Where |
|---|---|---|
| `OQ-discharge-attestation` | Schema + Ken contract **RATIFIED** by ken (§12); CT-method residue spun to `OQ-ct-assurance`. | §10 (`12`) |
| `OQ-sampling-policy` | The per-deployment test-sampling measure + its governance. | §40 |

## Ward-internal (opened by the campaign)

| OQ | Question | Where |
|---|---|---|
| `OQ-ward-stack` | Ward's implementation stack + top-level architecture. | §00 / campaign |
| `OQ-wardformula` | The `WardFormula` target + the `compile` image. | §20 / §21 |
| `OQ-model-target` | The model-translation target (Quint module / Apalache TLA+ / IR). | §22 / §30 |
| `OQ-export-wire` | Finalize the export field wire spellings, back-coordinated to ken. | §11 |
| `OQ-regression-corpus` | The pinned-witness corpus: identity keying, replay, staleness, governance. | §44 |
| `OQ-ct-assurance` | The runtime-CT validation method: mechanism tractability, platform-pin, the CT assurance policy (codegen is Ken's — coordinate back). | §13 |

## Ken-decided, realized in Ward (NOT open — do not reopen)

These forks Ken already closed; Ward inherits the decision and **realizes** it
in the named sections. Listed for traceability so an elaborator does not mistake
a realization task for an open design fork. Any friction goes back to Ken (the
seam is fixed input), not into a Ward re-decision.

| OQ (ken) | Ken's decision (short) | Realized in |
|---|---|---|
| `OQ-classical-bridge` | Strictly one-way; never `proved`; sound by assume-guarantee; faithfulness Ken-checked once at compiler level + generated model + conformance + one honest assumption; trust edge = Ward version in the attestation. | §23 (+ §33/§52) |
| `OQ-temporal` | `Temporal` is inert data (no kernel modality); stated + exported as `T`/`delegated`. | §21 (source) |
| `OQ-conformance` | Ken emits a trace/instrumentation contract (observability in `Σ`); the checking mechanism is the downstream consumer's. | §51 / §52 |
| `OQ-agentic-oracle` | Assuring an embedded agent = the existing seam aimed at a maximally-nondeterministic `P`; four-row status partition; no new agentic mechanism. | §53 |

## Resolution log

Append `**RESOLVED (date, by):** …` entries as forks close; never delete a
decided OQ — keep it for the record, mirroring ken.

**RESOLVED (2026-07-01, design) — `OQ-discharge-attestation`, schema + Ken
contract (§12).** The attestation is an **in-toto predicate** over the built
artifact (`predicateType: ward.dev/attestation/discharge/v1`), keyless-signed
and carried in provenance on the policy-attestation ladder (ken `63 §5`,
`65 §5`). Per-obligation outcome is Ken's four-way made total — `discharged`
(decided) / `bounded` (depth-`k` **or** sampled coverage) / `monitored`
(observed windows) / `failed` — with the bound recorded alongside and a rollup
rule when engines overlap. The **Ken-visible surface** is fixed to
`export.hash`, `export.contractVersion`, `ward.version`, each
`obligations[].{id,field,outcome}`, and the `signature`; the gate enforces
signature + export-hash match (= revocation) + per-obligation sufficiency
against a **governance** requirement, and must never promote to `proved`.

**RATIFIED (2026-07-01, ken steward).** Both coordination items ratified against
ward `f33276b`; tokens **pinned**, Ken formalizing into `63 §5a` via its Sec6 WP
(erratum, not a hold, if its gate surfaces an edit). Item 1: Ken-visible field
set accepted as-is (it is exactly Ken's B1 export surface); `predicateType`
`ward.dev/attestation/discharge/v1` accepted as Ward-namespace. Item 2:
`bounded` accepted as a **single** widened label, the depth-vs-sample mechanism
kept in a Ward-internal `bound` Ken does not read. **New ratified contract
invariant (on Ward):** `obligations[].id` is stable over `Σ` across
`export.hash` changes — Ken relies on it to match a discharge to its re-check;
same key §44's regression corpus uses. Tokens now bound ⇒ rename is a breaking
change (`OQ-export-wire` stability rule). The CT-method residue is now spun into
`OQ-ct-assurance` (below); `OQ-discharge-attestation` is **fully resolved at the
seam**.

**DESIGN SET (2026-07-01, operator) — `OQ-ct-assurance` (§13), the CT-method
residue, spun out of `OQ-discharge-attestation`.** Runtime CT validation
supports **three mechanisms** — statistical (dudect-style), hardware-assisted,
and formal/binary-level (Binsec/Rel, ct-verif class) — selected per obligation
by a user-authored **CT assurance policy** (governed like §42's sampling policy;
the **leakage model** is a recorded policy choice that bounds every claim's
strength). **Formal-tier scope (operator-concurred):** no verified CT-preserving
compiler on **either** side. **Codegen is Ken's** (its backend, likely LLVM):
Ken preserves CT **best-effort using LLVM's existing features** (branchless
lowering that survives passes, no secret-dependent branch/index/memory, ARM DIT
/ x86 DOITM where available) — ken `45` / `61 §5a` / `OQ-backend-target`,
communicated back to ken's steward. Ward's high-assurance tier is a **sound
binary-level CT verifier** over Ken's *emitted binary*, so neither side needs a
verified compiler — Ward's binary check catches any violation the backend
introduced. **Platform binding:** a CT verdict is platform-relative; recommended
handling folds platform into the build/provenance identity (no Ken-visible
field), gate-visible pin as fallback. **Honesty:** statistical/hw →
`monitored`/`bounded` (never `discharged`); formal sound-for-`L` → possibly
`discharged`, always relative to leakage model `L` + platform `P`; a bare "CT:
pass" is forbidden; never promoted to `Q`. **Open (residue):** each mechanism's
tractability, the platform-pin choice, and the ken-side codegen coordination.
