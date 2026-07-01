# Ward — open decisions

> Status: living. Ward's design forks and their resolution log, mirroring ken
> `spec/90-open-decisions.md`. Two OQs arrive **pre-assigned** to Ward from Ken
> (Ward-blocked there); the rest are Ward-internal, opened by the campaign.

## Inherited from Ken (Ward-blocked in ken)

| OQ | Question | Where |
|---|---|---|
| `OQ-discharge-attestation` | Schema + Ken contract **RESOLVED** (§12, see log); CT validation *method* still open. | §10 (`12`, `13`) |
| `OQ-sampling-policy` | The per-deployment test-sampling measure + its governance. | §40 |

## Ward-internal (opened by the campaign)

| OQ | Question | Where |
|---|---|---|
| `OQ-ward-stack` | Ward's implementation stack + top-level architecture. | §00 / campaign |
| `OQ-wardformula` | The `WardFormula` target + the `compile` image. | §20 / §21 |
| `OQ-model-target` | The model-translation target (Quint module / Apalache TLA+ / IR). | §22 / §30 |
| `OQ-export-wire` | Finalize the export field wire spellings, back-coordinated to ken. | §11 |
| `OQ-regression-corpus` | The pinned-witness corpus: identity keying, replay, staleness, governance. | §44 |

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
**Pending:** ken ratification of the Ken-visible spellings + the `bounded`
widening of `bounded-to-k` (coordination items, §12). **Still open under this
OQ:** the CT validation *method* (§13) — the attestation carries its verdict,
but how the verdict is produced is Ward-internal and unresolved.
