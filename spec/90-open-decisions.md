# Ward — open decisions

> Status: living. Ward's design forks and their resolution log, mirroring ken
> `spec/90-open-decisions.md`. Two OQs arrive **pre-assigned** to Ward from Ken
> (Ward-blocked there); the rest are Ward-internal, opened by the campaign.

## Inherited from Ken (Ward-blocked in ken)

| OQ | Question | Where |
|---|---|---|
| `OQ-discharge-attestation` | The discharge-attestation shape + the runtime CT validation. | §10 (`12`, `13`) |
| `OQ-sampling-policy` | The per-deployment test-sampling measure + its governance. | §40 |

## Ward-internal (opened by the campaign)

| OQ | Question | Where |
|---|---|---|
| `OQ-ward-stack` | Ward's implementation stack + top-level architecture. | §00 / campaign |
| `OQ-wardformula` | The `WardFormula` target + the `compile` image. | §20 / §21 |
| `OQ-model-target` | The model-translation target (Quint module / Apalache TLA+ / IR). | §22 / §30 |
| `OQ-export-wire` | Finalize the export field wire spellings, back-coordinated to ken. | §11 |

## Ken-decided, realized in Ward (NOT open — do not reopen)

These forks Ken already closed; Ward inherits the decision and **realizes** it in
the named sections. Listed for traceability so an elaborator does not mistake a
realization task for an open design fork. Any friction goes back to Ken (the seam
is fixed input), not into a Ward re-decision.

| OQ (ken) | Ken's decision (short) | Realized in |
|---|---|---|
| `OQ-classical-bridge` | Strictly one-way; never `proved`; sound by assume-guarantee; faithfulness Ken-checked once at compiler level + generated model + conformance + one honest assumption; trust edge = Ward version in the attestation. | §23 (+ §33/§52) |
| `OQ-temporal` | `Temporal` is inert data (no kernel modality); stated + exported as `T`/`delegated`. | §21 (source) |
| `OQ-conformance` | Ken emits a trace/instrumentation contract (observability in `Σ`); the checking mechanism is the downstream consumer's. | §51 / §52 |
| `OQ-agentic-oracle` | Assuring an embedded agent = the existing seam aimed at a maximally-nondeterministic `P`; four-row status partition; no new agentic mechanism. | §53 |

## Resolution log

Append `**RESOLVED (date, by):** …` entries as forks close; never delete a
decided OQ — keep it for the record, mirroring ken.
