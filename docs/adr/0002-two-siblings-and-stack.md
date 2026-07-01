# ADR 0002 — Two siblings (Ward + Keep), and the Ward stack

- **Status:** Accepted
- **Date:** 2026-07-01
- **Deciders:** the operator
- **Resolves:** `OQ-ward-stack`. **Opens:** `OQ-deployment-seam`.
- **Normative detail:** `spec/01-architecture.md`.

## Context

`OQ-ward-stack` (ADR 0001 §consequences) left Ward's implementation stack and
top-level architecture as a design output. The behavioral-assurance work spans
two very different operational contexts — offline/CI discharge and
runtime/production monitoring — with different constraints and lifetimes.

## Decision

1. **Two sibling tools, not one.** **Ward** owns the offline/build-time
   discharge (L1 model-checking, L2 test-generation, the regression corpus,
   offline CT verification) and **emits + signs the attestation**. **Keep** owns
   the runtime/production side (L3 monitors, trace conformance, runtime timing,
   the agentic envelope). Split by operational context.
2. **A shared *seam* crate** carries what both need — the export consumer, `Σ`/
   ITF types, the `τ` translation, the attestation library + obligation-identity
   key, and the metamorphic-relation surface — so neither re-implements the
   seam.
3. **Rust substrate**, with self-hosting in Ken as the (future) north star; Rust
   is likely Ken's bootstrap regardless, so it is durable, not a crutch.
4. **Orchestrator with a minimal trusted core.** Ward integrates a polyglot
   best-of-breed toolbox as **invoked, version-pinned processes**, never linked;
   the small orchestration+attestation+signing core is what `ward.version`
   trusts. Invoking (not linking) also keeps copyleft tools out of the MIT core.
5. **Orchestrated tools are clean-room-replaceable**, not permanent dependencies
   (the ~36-hour kernel precedent); copyleft refs are approach-sources.
6. **Honest version transparency.** The attestation records each orchestrated
   component + version + reproducibility status and **flags what cannot be
   reproduced** — it says what is assured and what is not, rather than
   laundering it. Ward-internal; no change to the ratified Ken-visible contract.
7. **The deployment seam is a reference policy, not a new checker.** Ward
   publishes the discharge predicate schema + a reference OPA/Rego (or Kyverno)
   policy so existing admission controllers (policy-controller, Gatekeeper,
   Binary Authorization) enforce the §12 gate. Tracked as `OQ-deployment-seam`.

## Consequences

- The Ward spec's §50, §53, and the runtime face of §13 are **Keep's**
  territory, to be migrated; Keep may later split to its own repository.
- The attestation's credibility is bounded by an explicit, minimal trusted core,
  with the large evidence-producing toolbox held at arm's length.
- A new seam (`OQ-deployment-seam`) is opened; the gate itself stays Ken's
  (§12).

## References

`spec/01-architecture.md` (normative); ADR 0001 (founding); §12 (attestation);
ken `73` (the sidecar hint).
