# Ward design progress — resume protocol

> Status: living backbone. Survives context compaction. **On resume, read this
> first**, then `README.md` and `docs/program/`.

## Task

Design Ward — Ken's behavioral-assurance sibling — from the Ken interface (§10,
fixed) outward through the engines (§20–§50), producing a normative Ward spec and
the design decisions (`docs/adr/`) behind it. Analogous to ken's spec campaign;
run by the Ward design federation (`agent/`).

## Conventions

- Clean design (`CLEAN-ROOM.md`): behavior-not-expression for copyleft refs.
- Design latitude: Ward is a fresh design; prefer the correct / elegant /
  permanent choice over the expedient one. The Ken seam (§10) is fixed input;
  everything downstream of it is open.
- Wrap markdown at 80 columns; one section per commit; `> Status:` blockquotes.

## Outline & status

| Section | Subject | Status |
|---|---|---|
| `00-overview` | Thesis; two engines; guarantee levels; scope | DRAFT v0 (scaffold) |
| `10-seam/11-export-consumer` | Consuming the export IR | DRAFT v0 (anchored to ken B1) |
| `10-seam/12-discharge-attestation` | Discharge attestation | DRAFT v0 (Ward-designed — open) |
| `10-seam/13-ct-runtime` | Runtime CT validation | DRAFT v0 (Ward-designed — open) |
| `20-translation` | `τ` + faithfulness | DRAFT v0 (open) |
| `30-model-checking` | L1 | DRAFT v0 (open) |
| `40-test-generation` | L2 + sampling policy | DRAFT v0 (open) |
| `50-runtime-verification` | L3 + agentic envelope | DRAFT v0 (open) |
| `90-open-decisions` | Fork log | living |

## Next action

Kick off the campaign: the design enclave grounds in §10 (the fixed Ken seam),
then opens `OQ-ward-stack` (§00) and `OQ-wardformula` (§20). See
`docs/program/DESIGN-PROGRESS.md` for the work-package view.
