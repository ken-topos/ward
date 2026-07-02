# Ward design progress — resume protocol

> Status: living backbone. Survives context compaction. **On resume, read this
> first**, then `README.md` and `docs/program/`.

## Task

Design Ward — Ken's behavioral-assurance sibling — from the Ken interface (§10,
fixed) outward through the engines (§20–§50), producing a normative Ward spec
and the design decisions (`docs/adr/`) behind it. Analogous to ken's spec
campaign; run by the Ward design federation (`agent/`).

## Conventions

- Clean design (`CLEAN-ROOM.md`): behavior-not-expression for copyleft refs.
- Design latitude: Ward is a fresh design; prefer the correct / elegant /
  permanent choice over the expedient one. The Ken seam (§10) is fixed input;
  everything downstream of it is open.
- Wrap markdown at 80 columns; one section per commit; `> Status:` blockquotes.

## Outline & status

Every section is now scaffolded to the §10 level of detail — a `> Status:`
frame, the substance, the Ward design deliverables, and the resolver — enough
for the design enclave to elaborate against the rest of the spec and the
reference shelf (`local/refs/`). The subsection files are the elaboration units.

| Section | Subject | Status |
|---|---|---|
| `00-overview` | Thesis; two engines; guarantee levels; scope | DRAFT v0 (scaffold) |
| `01-architecture` | The Ward stack: two siblings (Ward + Keep) | **DECISION SET** (`OQ-ward-stack`; ADR 0002) |
| `10-seam/11-export-consumer` | Consuming the export IR | DRAFT v0 (anchored to ken B1) |
| `10-seam/12-discharge-attestation` | Discharge attestation | **RATIFIED** by ken (schema + Ken contract; §13 CT method open) |
| `10-seam/13-ct-runtime` | Runtime CT validation | **DESIGN SET** (3 mechanisms + assurance policy; `OQ-ct-assurance` residue) |
| `20-translation/21-property-translation` | `WardFormula` ideal + projections | **DESIGN SET** (`OQ-wardformula`) |
| `20-translation/22-model-translation` | `WardModel` ideal + projections | **DESIGN SET** (`OQ-model-target`) |
| `20-translation/23-faithfulness` | Bridge discipline + version pin | DRAFT v0 (realizes `OQ-classical-bridge`) |
| `30-model-checking/31-integration` | Checker integration (Quint/Apalache/TLC, 3 modes, ITF) | **DESIGN SET** — engine confirmed (`OQ-model-target`) |
| `30-model-checking/32-state-space` | `Q` assumed + inductive lever, `P` environment | **DESIGN SET** (`OQ-model-target`) |
| `30-model-checking/33-bounded-results` | Fragment map + mode/bound recording | **DESIGN SET** (`OQ-model-target`) |
| `40-test-generation/41-generators-oracles` | Generators + oracles from `G` | DRAFT v0 (open) |
| `40-test-generation/42-sampling-policy` | The measure Ken omits | DRAFT v0 (Ward-designed — open, `OQ-sampling-policy`) |
| `40-test-generation/43-metamorphic` | Model-based + metamorphic testing | DRAFT v0 (open) |
| `40-test-generation/44-regression-corpus` | Pinned witnesses of every defect | DRAFT v0 (open, `OQ-regression-corpus`) |
| `50-runtime-verification/51-monitor-synthesis` | `T` → monitor over `Σ` | DRAFT v0 (open) |
| `50-runtime-verification/52-trace-conformance` | Code refines model? | DRAFT v0 (realizes `OQ-conformance`) |
| `50-runtime-verification/53-agentic-envelope` | Agent as bounded `P` | DRAFT v0 (realizes `OQ-agentic-oracle`) |
| `90-open-decisions` | Fork log | living |

## Next action

Kick off the campaign: the design enclave grounds in §10 (the fixed Ken seam),
then opens `OQ-ward-stack` (§00) and `OQ-wardformula` (§20). The scaffolded
§20–§50 subsections are the elaboration backlog; each names its resolver OQ. See
`docs/program/DESIGN-PROGRESS.md` for the work-package view.
