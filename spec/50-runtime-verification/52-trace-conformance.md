# 52 — Trace conformance: does the code refine the model?

> Status: DRAFT v0 — open design. This is the runtime half of the faithfulness
> argument (§23, part 4): Ken guarantees the model has no authoring drift; L3
> checks the running code stays within it. Consumes ken `73`'s trace contract
> and the L1 model (§22/§30). Resolver: `OQ-conformance` (DECIDED in ken —
> realized here).

## What it is

Faithfulness (§23) reduces "the model means the code" to two checkable claims:
the model is generated so it cannot drift from the code's *denotation* (§22),
and the *running* code stays within the model — **trace conformance**. This
chapter owns the second. The instrumented execution emits a trace in `Σ`'s
vocabulary (ken `73 §2`); Ward checks that trace **refines** the L1 model's
allowed behaviors: every observed transition is one the model permits, and
observed ordering respects the model's structure.

This is the load-bearing runtime check for the whole L1 pillar. A model check
(§30) is only meaningful about the real system if the real system actually
behaves like the model; trace conformance is what turns "checked the model" into
"checked something true about the code," continuously, in production. When it
holds, L1's verdicts transfer; when it fails, the model and the code have
diverged and *that divergence is the finding* — surfaced, not smoothed over.

## Where it sits in the loop

- **From §30** — the L1 model (§22) is the reference: the set of allowed
  behaviors the live trace is checked against.
- **From ken `73`** — the trace itself: one event per `Vis` firing, with the
  fields and correlation keys Ken's contract fixes (ken `73 §2.1`/`§2.2`).
- **To §30** — a conformance violation feeds back: either the model is too
  abstract (refine §22) or the code has a bug the model rules out (a real
  defect). Both are results.
- **To §12** — a conformance verdict is a discharge: it substantiates the
  faithfulness assumption (§23 part 5) *for the observed execution*, version-
  and export-hash-bound, never promoted.

## Ward design deliverables

- **The refinement/conformance checker.** The precise refinement relation (trace
  inclusion, simulation, or a stronger notion) between the live `Σ`-trace and
  the L1 model, and the algorithm that decides it online or offline. Reuse the
  L1 engine's trace machinery (ITF, §31) where possible — the model checker and
  the conformance checker speak the same trace format by design (§11).
- **The correlation/reconstruction logic.** How per-space events (ken `73 §2.2`
  correlation keys) reassemble into the global or per-space trace the model is
  stated over — the piece offline checking glossed and a live monitor cannot
  (ken `73 §2.2`).
- **The violation response contract.** What Ward records and routes on a
  divergence: the diverging trace prefix (→ §40 as a seed; → §22 as a model-
  refinement signal), and the honest attestation entry (this execution did
  **not** refine the model). Ken leaves the *response policy* (gate / monitor /
  both) to the downstream consumer (ken `73 §4`); Ward supplies the mechanism
  and the recording, not the policy.
- **The honesty statement.** Conformance holds **for observed executions**, not
  for all executions — it is evidence the code refines the model on the traces
  seen, closing §23's residual gap only as far as coverage reaches. Record that
  bound (§12), like L1's `N ≤ k` (§33).

Resolver: `OQ-conformance` (§90) — Ken's observability contract, checking
mechanism realized here.
