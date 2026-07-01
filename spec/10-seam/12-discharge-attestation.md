# 12 — The discharge attestation

> Status: **FINALIZED (design), pending ken ratification of the Ken-visible
> fields.** Ken decided the artifact (ken
> `spec/60-security/63-supply-chain.md §5a`, `OQ-discharge-attestation`) and
> deferred the *schema + gate semantics* to Ward ("needs Ward's runner"). This
> chapter locks Ward's side: the concrete schema, the per-obligation outcome
> semantics, the Ken-side contract, and the lifecycle. The **CT validation
> *method*** (§13) remains Ward-internal open; the attestation *carries* its
> verdict, finalized here. Where a field is marked `(ratify)` the ken team
> confirms the Ken-visible spelling, exactly as §11 handles the export wire
> (`OQ-export-wire`).

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
// predicateType: "https://ward.dev/attestation/discharge/v1"   (ratify)
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
  `(ratify)` — the one place Ward's label is *wider* than ken's current
  spelling.
- **Rollup when several engines attack one obligation.** One `outcome` per
  obligation, by rule: any `failed` ⇒ `failed`; else `discharged` (decided)
  beats `bounded`/`monitored`; the individual engine results stay in
  `evidence[]`.
- **Nothing here is `proved`.** The four outcomes are all classical evidence;
  none promotes a `T` to a Ken `Q` (§23; I4). `discharged` means *decided by
  Ward*, not *proved by Ken*.

## The Ken-side contract (what the ken team implements against)

This is the hand-off. Everything else in the schema is Ward-internal.

- **Ken emits** (already landed, B1): the `Q/P/Σ/T/G` export + its content-hash
  + each `T`'s `delegated` status. The attestation consumes these; Ward adds no
  requirement on the emitter beyond what §11 already coordinates.
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
- **Ken must NOT**: treat any `outcome` as promoting a `T` to `proved` (I4);
  depend on any Ward-internal field (`policy`, `bound`, `evidence`, `ct.method`,
  `regression`) for a correctness judgment; or assume a fifth outcome. The
  Ken-visible surface is exactly `export.hash`, `export.contractVersion`,
  `ward.version`, each `obligations[].{id, field, outcome}`, and the `signature`
  — nothing more.

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

## Coordination items for the ken team

1. **Ratify the Ken-visible field spellings** (marked `(ratify)`): the
   `predicateType` URI, and `export.hash` / `export.contractVersion` /
   `ward.version` / `obligations[].{id,field,outcome}` — the contract surface,
   into ken `63 §5a` conformance.
2. **Ratify `bounded` generalizing `bounded-to-k`** to subsume sampled coverage,
   or split into two labels — the one substantive widening of ken's `§5a` text.
3. **Own the gate *requirement* policy** (ken `64`/`65`): what each target
   environment demands of the outcomes. Ward specifies the gate's *check*; the
   *requirement* is governance.

## Residue (still open)

The **CT validation method** (§13) — how the runtime constant-time verdict in
`ct[]` is actually produced — is Ward-internal and unresolved; this chapter
finalizes only that the verdict *rides here*. Resolver for the residue:
`OQ-discharge-attestation` remains open **for §13's method**; the **attestation
schema + Ken contract are resolved** (see `90-open-decisions.md`).
