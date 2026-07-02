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
| `OQ-ward-stack` | **RESOLVED** (§01, ADR 0002): two siblings (Ward + Keep), shared seam crate, Rust, orchestrator core. | §01 |
| `OQ-deployment-seam` | The `(Ken+Ward)↔deployment` seam: discharge predicate schema + reference admission policy (gate is Ken's). | §01 |
| `OQ-wardformula` | **DESIGN SET** (§21): ideal IR + lossless `compile` + classified projections; open = the first projection's fragment map. | §20 / §21 |
| `OQ-model-target` | **ENGINE CONFIRMED** (§31/§32/§33): Quint + Apalache (three modes) + TLC; open = the first worked `π` + fragment/abstraction map, mode-selection policy. | §22 / §30 |
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

**RESOLVED (2026-07-01, operator) — `OQ-ward-stack` (§01, ADR 0002).** **Two
sibling tools split by operational context:** **Ward** = offline/CI discharge
(L1 model-checking, L2 test-generation, regression corpus, offline CT, and the
attestation emit+sign); **Keep** = runtime/production (L3 monitors, trace
conformance, runtime timing, agentic envelope). A shared **seam crate** carries
the export consumer, `Σ`/ITF types, `τ`, the attestation library + obligation-id
key, and the metamorphic surface. **Substrate: Rust** (self-hosting in Ken is
the future north star; Rust is Ken's likely bootstrap regardless).
**Orchestrator posture:** a **minimal trusted core** (orchestration +
attestation + signing) over a polyglot toolbox run as **invoked, version-pinned
processes** (never linked — also keeps copyleft tools out of the MIT core);
orchestrated tools are **clean-room-replaceable**, not permanent deps. **Honest
version transparency:** the attestation records each tool + version +
reproducibility status and flags what cannot be reproduced. **Consequence:**
§50/§53 and §13's runtime face are **Keep's**; **migrated 2026-07-02** to the
`ken-topos/keep` repo (Keep ADR 0001) — Ward's §50 is now a pointer, §13's
runtime face is keep §54, and the offline CT verifier stays in Ward §13.

**DESIGN SET (2026-07-01, operator) — `OQ-wardformula` + `OQ-model-target`
(§21/§22), the translation `τ`.** Two layers on both the property and the system
side. **Ideal:** `WardFormula` (the full μ-calculus-over-`Σ`, Ward's
denotational semantics) and `WardModel` (the faithful transition system
generated from `Σ` + the space semantics). **Lossless in:**
`compile : Temporal Σ → WardFormula` is an isomorphic change of representation —
Ken proves `⟦φ⟧ = ⟦compile φ⟧` once (faithfulness lives here); the model is
*generated*, not authored (no drift). **Lossy out:** classified **projections**
`π` to a concrete, **replaceable** (§01) checker — fidelity ∈ {exact, sound
over-approx, sound under-approx/bounded, not projectable} — where the checker's
poorer language and finite-state abstraction live (fidelity lives here). The
**μ-calculus gap** is thus recorded routing, not loss: LTL-expressible → `exact`
(L1 fast path); true fixpoints → another checker/projection, or Keep's runtime
monitor, or flagged — never dropped. Projection soundness is a new faithfulness
component (§23, now five parts). This inherits §01's
minimal-core-over-replaceable-tools split (`WardFormula`/`WardModel` and the
lemma in the core; projections are the tool adapters). **Open (residue):** the
pinned `⟦·⟧` (finite/ω + fixpoint interpretation, ken `72 §6.2`); confirm
Quint+Apalache; the **first projection** (property + model) and its fragment/
abstraction map; the source-spelling coordination back to ken (`72 §3.1`).

**ENGINE CONFIRMED (2026-07-01, operator) — `OQ-model-target` (§30:
§31/§32/§33), the L1 engine.** **Quint + Apalache confirmed** (read for behavior
from `local/refs/{quint,apalache,tlaplus}`, Apache-2.0). The slot is **three
checking modes** behind one model projection (§31): **Apalache
bounded-symbolic** (SMT/Z3, `--max-steps`; green ⇒ `bounded`, data-rich),
**Apalache inductive-invariant** (two-query, unbounded; green ⇒ `discharged` —
the strong path, unlocked by an assumed `Q`, §32), and **TLC explicit-state**
(any-length, full temporal; green ⇒ decision on a *small* finite model — the
liveness escalation). **Fragment map (§33):** the **LTL fragment**
(`[]`/`<>`/`~>`/`WF`/`SF` over `Pred Σ`, Apalache ADR-017) projects `exact`;
alternating `μ`/`ν` with no LTL image is `not projectable` and routes onward
(§21). **Two honest axes kept distinct:** projection *fidelity* (expressiveness)
vs the *bound* (depth `k`) — in-fragment `exact` still only earns `bounded`
under symbolic depth, and only inductive / exhaustive-TLC convert it to
`discharged`. **Q/P lowering (§32):** `Q` → assumed state constraint + candidate
Apalache inductive invariant (never re-proved); `P` → `nondet … oneOf()` / free
vars (symbolic Apalache keeps large `P` domains tractable); consistency guard
flags a model that violates an assumed `Q`. **ITF (§31):** native from both
engines (`itf-rs`, §01); finite + **lasso** shapes, `{#bigint}` ints;
counterexamples route to §52 (conformance) and §40 (seed). **Invocation:**
invoked, version-pinned process (Quint + Apalache + Z3 + JVM, seed,
`--max-steps`) recorded in the attestation (§12) — the checker is classical
trust base. **Open (residue):** the first *worked* `π` pair (model + property)
with its concrete fragment/abstraction coverage, and the mode-selection policy
(§33 knobs). Note: §31/§32/§33 built against Apache-2.0 refs — no originality
scan required (that gate is copyleft-only, §00/`CLEAN-ROOM.md`).

**OPENED (2026-07-01) — `OQ-deployment-seam` (§01).** The third seam,
`(Ken+Ward)↔deployment`. Signature-verification + admission-control tooling is
mature (sigstore policy-controller, cosign `verify-attestation`, Kyverno,
OPA/Gatekeeper, Binary Authorization) and — because §12 chose in-toto/sigstore —
already carries Ward's attestation; what is missing is the
**behavioral-discharge policy vocabulary**. Ward brings it: publish the
discharge **predicate schema** + a **reference OPA/Rego (or Kyverno) policy** so
existing controllers enforce the §12 gate (which is Ken's). Not a new checker —
the research-to-industry gap is the semantics, not the mechanism.
