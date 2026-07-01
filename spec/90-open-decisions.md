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
| `OQ-wardformula` | The `WardFormula` target + the `compile` image. | §20 |
| `OQ-model-target` | The model-translation target (Quint module / Apalache TLA+ / IR). | §20 / §30 |
| `OQ-export-wire` | Finalize the export field wire spellings, back-coordinated to ken. | §11 |

## Resolution log

Append `**RESOLVED (date, by):** …` entries as forks close; never delete a
decided OQ — keep it for the record, mirroring ken.
