# Ward spec

Ward's design/spec, in decade-numbered sections. Each section opens with a
`> Status:` blockquote declaring its normativity. This tree is the campaign's
normative target; exploratory work lives in `../docs/design/`, decisions in
`../docs/adr/`, and the plan + status in `../docs/program/`.

| Section | Subject |
|---|---|
| [`00-overview.md`](00-overview.md) | Thesis; one logic, two engines; guarantee levels; scope. |
| [`10-seam/`](10-seam/) | **The Ken interface** — the assumption-boundary export Ward consumes, the discharge attestation, the runtime CT validation. The fixed input. |
| [`20-translation/`](20-translation/) | `τ`: `compile : Temporal Σ → WardFormula`, model translation, faithfulness. |
| [`30-model-checking/`](30-model-checking/) | **L1** — model checking (Quint / Apalache): check `T` against `Q`/`P`. |
| [`40-test-generation/`](40-test-generation/) | **L2** — spec-driven test generation from `G`; the sampling policy; metamorphic testing. |
| [`50-runtime-verification/`](50-runtime-verification/) | **L3** — monitor synthesis, trace conformance, the agentic safety envelope. |
| [`90-open-decisions.md`](90-open-decisions.md) | The design forks + resolution log. |

Living status: [`SPEC-PROGRESS.md`](SPEC-PROGRESS.md). Non-normative working
notes: [`_notes/`](_notes/).
