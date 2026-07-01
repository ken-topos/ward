# 12 — The discharge attestation

> Status: **RATIFIED (2026-07-01).** Ken's steward ratified both coordination
> items (against ward `f33276b`); the Ken-visible **tokens are pinned** and Ken
> is formalizing them into `63 §5a` via its Sec6 spec WP. Ken decided the
> artifact (ken `spec/60-security/63-supply-chain.md §5a`,
> `OQ-discharge-attestation`) and deferred the *schema + gate semantics* to Ward
> ("needs Ward's runner"); this chapter is that design, now bilaterally locked:
> the concrete schema, the per-obligation outcome semantics, the Ken-side
> contract (including a **ratified cross-field invariant**, §Ken-side contract),
> and the lifecycle. Because the tokens are now **bound**, a rename is a
> breaking change (the `OQ-export-wire` stability rule). The **CT validation
> *method*** (§13) remains the sole Ward-internal open residue; the attestation
> *carries* its verdict.

## What it is

The **classical-discharge record**: when Ward discharges an exported obligation
(`T`) or assumption (`P`) by classical means — a model-check, a test campaign, a
monitor window, a regression replay — the fact "this was discharged, this way,
against this Ken export" is recorded here. In enterprise compliance "the tests
ran and passed" is already a required artifact; Ken/Ward replace text logs +
coverage XML with a **signed, runtime-checkable** attestation (ken `63 §5a`). It
is also the anchor of the seam's **translation-faithfulness** argument (§23):
Ken proves `⟦φ⟧ = ⟦compile φ⟧` *relative to an axiomatized Ward semantics*; that
**Ward implements** that semantics is the one explicit, version-bounded
assumption — pinned here, in the Ward-version signature.

## Ken-side constraints (fixed — Ward designs within them)

- **Binds 1:1 to the export hash.** A discharge is recorded against the
  content-hash of the `Q/P/Σ/T/G` contract it answered —
  `export_hash ↔ discharge` is reproducible, mirroring `export_hash ↔ build` in
  provenance.
- **Pins the Ward version.** The faithfulness proof is relative to an
  axiomatized Ward semantics; the attestation records *which* Ward produced the
  discharge — the load-bearing trust edge, not bureaucracy.
- **Never promotes.** A discharged obligation stays `delegated`/`tested` and
  re-enters the Ken side only as an attestation record — **never** a certificate
  the kernel re-checks (§00, the one-way gate; ken `71 §5.1`, I4).
- **Carries the runtime CT verdict.** The runtime constant-time validation (§13)
  is emitted **into** this attestation.

## The artifact — an in-toto predicate over the build

Ward reuses Ken's provenance substrate rather than inventing transport: the
attestation is an **in-toto Statement** whose `subject` is the built artifact
and whose `predicate` is the discharge record, **keyless-signed (sigstore) and
carried in provenance** on the same governance ladder as the policy attestation
(ken `63 §5`, `65 §5`; `OQ-provenance`). This is the load-bearing format
decision — it makes the attestation runtime-checkable by the *existing*
provenance verifier, so the deployment gate is a predicate check, not new
machinery.

```jsonc
// predicateType: "https://ward.dev/attestation/discharge/v1"  (pinned)
{
  "export":  { "hash": "<content-hash of Q/P/Σ/T/G>",           // Ken-visible
               "contractVersion": "<71 contract version>" },     // Ken-visible
  "ward":    { "version": "<semver+build>", "runner": "<hash>" },// Ken-visible
  "policy":  { "sampling": {"hash","version"},                  // Ward-internal
               "model": {...}, "monitor": {...} },
  "obligations": [                                               // per-entry:
    { "id":      "<obligation identity over Σ>",   // Ken-visible; = §44 key
      "field":   "T" | "P" | "Q@ct",               // Ken-visible export channel
      "outcome": "discharged" | "bounded"          // Ken-visible four-way
               | "monitored"  | "failed",
      "bound":   { ... },                          // Ward-internal (see below)
      "evidence":[ { "engine":"L1|L2|L3", ... } ] } // Ward-internal detail
  ],
  "ct":         [ { "id": "...", "verdict": "pass|fail|inconclusive",
                    "method": "..." } ],            // §13, rides here
  "regression": { "corpusHash": "...", "replayed": N, "recurred": 0 }, // §44
  "reproducibility": { "seeds": [...], "coverage": {...} }       // optional
}
```

The `obligations[].id` is the **obligation identity over `Σ`** — the same stable
key §44 uses for the regression corpus, so a discharge, a corpus case, and a
future re-check all name the same obligation across export-hash changes.

> **Ratified contract invariant (ken steward, 2026-07-01) — id stability.**
> `obligations[].id` MUST be a stable function of the obligation over `Σ`,
> **invariant across `export.hash` changes** — a discharge and a later re-check
> name the same obligation even when the export hash moves. Ken depends on this
> to match a discharge to its re-check, so it is a **contract obligation on
> Ward**, not merely an implementation convenience. It is exactly the stable key
> §44 already builds on.

## Per-obligation outcome — the four-way, made total

Ken fixed four outcomes (ken `63 §5a`:
`discharged / bounded-to-k / monitored / failed`). Ward gives each a precise
meaning so **every** engine verdict maps to exactly one, and records the *bound*
alongside so a partial result never reads as a total one:

| Outcome | Meaning | Produced by | `bound` records |
|---|---|---|---|
| `discharged` | **decided** — exhaustive/finite-state or decidable | L1 exhaustive (§33); a decidable check | — (none; it is total) |
| `bounded` | partial evidence, **honestly bounded** | L1 no-CEX to depth `k` (§33); L2 campaign under a measure (§42) | depth `k` **or** sampling coverage/seeds |
| `monitored` | runtime evidence over **observed** executions | L3 monitor window (§51); trace conformance (§52) | the window / observation scope |
| `failed` | a counterexample/violation/failed campaign was found | any engine | the witness (→ §44 corpus) |

- **`bounded` generalizes Ken's `bounded-to-k`** to also cover sampled coverage
  (L2) — same category (partial evidence under a stated bound), broader source.
  **RATIFIED (2026-07-01) as a single label**: the four-way classifies
  *epistemic status*, not the *mechanism* that produced it, so depth-`k` vs.
  sampled is a Ward-internal distinction — recorded in `bound`, **which Ken does
  not read**. Not two labels.
- **Rollup when several engines attack one obligation.** One `outcome` per
  obligation, by rule: any `failed` ⇒ `failed`; else `discharged` (decided)
  beats `bounded`/`monitored`; the individual engine results stay in
  `evidence[]`.
- **Nothing here is `proved`.** The four outcomes are all classical evidence;
  none promotes a `T` to a Ken `Q` (§23; I4). `discharged` means *decided by
  Ward*, not *proved by Ken*.

## The Ken-side contract (what the ken team implements against)

This is the hand-off. Everything else in the schema is Ward-internal.

- **Ken emits** (already landed, B1): the `Q/P/Σ/T/G` export, its content-hash,
  and each `T`'s `delegated` status. The attestation consumes these; Ward adds
  no requirement on the emitter beyond what §11 already coordinates.
- **Ken/the gate enforces**, at deployment, three checks and no more:
  1. **Signature valid** — keyless-verifiable, Ward-version present (ken
     `63 §5`).
  2. **`export.hash` matches** the export in the artifact's provenance — the
     attestation answers *this* build, not another (this is also the
     **revocation mechanism**: a stale attestation simply fails the hash match,
     fail-closed).
  3. **Each required obligation's `outcome` meets the target-environment
     requirement** — the requirement (e.g. "external endpoints: no `bounded` on
     a `@ct` obligation") is **governance policy** (ken `64`/`65`), not Ward's;
     the gate reads the outcomes, the policy decides sufficiency.
     - *CT obligations (`Q@ct`) carry an extra binding:* the verdict is valid
       only for the platform it was measured on (§13). Recommended handling
       folds platform into the build identity, so check (2) covers it and **no
       Ken-visible field is added**; a gate-visible platform pin is the fallback
       (a ratification delta, §13).
- **Ken must NOT**: treat any `outcome` as promoting a `T` to `proved` (I4);
  depend on any Ward-internal field (`policy`, `bound`, `evidence`, `ct.method`,
  `regression`) for a correctness judgment; or assume a fifth outcome. The
  Ken-visible surface is exactly `export.hash`, `export.contractVersion`,
  `ward.version`, each `obligations[].{id, field, outcome}`, and the `signature`
  — nothing more.
- **Ward MUST hold** the ratified **id-stability** invariant above: an
  `obligations[].id` names the same obligation over `Σ` across `export.hash`
  changes. This is the one contract obligation the ratification added *on Ward*.

## Lifecycle

- **Issuance.** One attestation per `(export-hash, Ward-version)` — i.e. one per
  build's discharge run — **aggregating all obligations** of that export into a
  single signed statement.
- **Revocation = hash mismatch.** There is no revocation list. A code change →
  new export hash → the old attestation no longer matches the gate's check (2)
  and is inert. New build, new attestation. Fail-closed by construction.
- **Re-issue on Ward upgrade.** Because the Ward version is the trust edge, a
  Ward upgrade **invalidates** prior discharges for gate purposes — the
  obligations are re-run under the new runner and a fresh attestation issued.
  `evidence[]` may cite the prior attestation for provenance, never as a
  substitute.

## What feeds it (the sink)

The attestation is the seam's terminus — nearly every engine chapter emits here:
§13 (CT verdict → `ct[]`); §33 (L1 `discharged`/`bounded`/`failed` + bound); §42
(sampling policy → `policy.sampling`, the measure a `bounded` L2 result is
relative to); §44 (regression replay → `regression`); §51/§52 (`monitored` +
window); §53 (an agent-bearing system's four-row composition, per obligation).

## Ratification record (ken steward, 2026-07-01)

Both coordination items are **ratified**; Ken is formalizing them into `63 §5a`
via its Sec6 spec WP (if its gate surfaces an edit, Ken sends an erratum — not a
hold). The outcome:

1. **Ken-visible field set — RATIFIED as-is.** Spellings confirmed (it is
   exactly the surface Ken already emits, B1 `71`); `predicateType`
   `ward.dev/attestation/discharge/v1` accepted (Ward namespace, Ward's to
   choose); `obligations[].field` value-set `T` / `P` / `Q@ct` confirmed against
   Ken's export channels. Ken pins **concept + value-set + cross-field
   invariants**; the literal tokens + `predicateType` are Ward's coordinated
   wire spelling, now **bound** (rename ⇒ breaking change). The added contract
   term is the **id-stability invariant** (above).
2. **`bounded` — RATIFIED as a single widened label**, bound recorded in a
   Ward-internal field Ken does not read (see the outcome table). Not two
   labels.
3. **The deployment gate is Ken's** (item 3): Ken builds the three checks on its
   existing provenance verifier; the check-(3) *requirement* is Ken governance
   (`64`/`65`). The **CT method (§13) stays Ward-side** — Ken carries the `ct[]`
   verdict but neither implements nor depends on it.

Ken also reaffirmed the one-way gate as its own enforcement obligation (its
conformance corpus carries a discriminating case that fails if any outcome is
wired to raise a `T` to `Q`). Nothing there for Ward to change.

## Residue (still open)

The **CT validation method** (§13) — how the runtime constant-time verdict in
`ct[]` is actually produced — is Ward-internal and unresolved; this chapter
finalizes only that the verdict *rides here*. Resolver for the residue:
`OQ-discharge-attestation` remains open **for §13's method only**; the
**attestation schema + Ken contract are ratified** (see `90-open-decisions.md`).
