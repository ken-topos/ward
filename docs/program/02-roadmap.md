# Ward design roadmap

> Status: DRAFT v0. Phases, each with an objective exit gate.

- **Phase 0 — Anchor.** Ratify the fixed seam (spec/10-seam) against ken
  spec/70-behavioral; open `OQ-ward-stack`. **Exit:** seam section reviewed +
  agreed; no divergence from ken left unflagged.
- **Phase 1 — Translation.** Define `WardFormula` + the `compile` image + the
  model target. **Exit:** the `compile` faithfulness lemma is statable;
  `OQ-wardformula` + `OQ-model-target` resolved.
- **Phase 2 — Engines.** Specify L1/L2/L3 and their discharge contributions.
  **Exit:** each engine section moves DRAFT → normative; §12 shapes each engine's
  evidence.
- **Phase 3 — Deferred artifacts.** Design the discharge attestation + runtime
  CT; coordinate Ken-visible fields back through ken `63 §5a`. **Exit:**
  `OQ-discharge-attestation` resolved (Ward side).
- **Phase 4 — Stack + freeze.** Resolve `OQ-ward-stack`; design freeze; hand to
  an implementation campaign. **Exit:** stack decided; spec normative;
  implementation DAG drawn.
