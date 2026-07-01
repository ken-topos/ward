# 44 — The regression corpus: pinned witnesses of every defect found

> Status: DRAFT v0 — open design. The deterministic counterpart to the sampling
> measure (§42): where §42 distributes finite effort over a measure, this
> chapter pins the *specific* points a past defect proved must always be
> checked. Fed by all three engines (§33, §41, §52/§53); recorded trans-export
> in the attestation (§12). Resolver: `OQ-regression-corpus`.

## What it is

Standard engineering and compliance practice: for every bug found, retain a test
that guards against its reintroduction. Ward makes that practice **automatic and
signed.** Every defect Ward finds already yields a concrete, typed witness — it
only needs to be *retained* and *always replayed*:

| Source engine | The witness retained |
|---|---|
| L1 counterexample (§33) | the ITF trace refuting a `T`, keyed to that obligation |
| L2 oracle failure (§41) | the generated input that violated a refinement oracle |
| L3 monitor breach (§52) | the trace prefix that diverged from the model |
| Agentic breach (§53) | the action sequence that escaped the envelope |

The corpus is the **durable, auto-populated memory** of those witnesses. A human
never has to remember to write the test; Ward retains everything it ever found,
content-addressed and deduplicated. This is *more* automatic than the manual
control, not less — the whole project's discipline applied to its own findings.

## Pinned obligations are the anti-sampling backstop

A regression case is **not** just another sample. The sampling policy (§42) is a
*measure* — probabilistic, effort-distributing; a future policy may legitimately
stop covering a region. A regression case is the opposite: a **specific point,
checked every run, weight-∞, decoupled from the measure.** Folding known-bad
points into the sampler would let the exact defects regression exists to prevent
slip back below the sampling threshold.

So the corpus and the sampler are **complementary and mutually reinforcing**:
the sampler explores the unknown space; the corpus nails the known-bad points
deterministically; and a found defect **both** pins a permanent witness here
*and* raises the risk-weight of its region in §42. The measure covers what might
break; the corpus covers what already did.

## Keyed by obligation identity, not export hash — so it does not rot

Manual suites decay into tests that no longer test anything because the code
moved underneath them. Ward avoids this **because the seam is
content-addressed** — but only if the corpus is keyed correctly. Keying a case
to the *export hash* (§11) would orphan the whole corpus on every code change.
Instead, key each case to the **obligation identity** — the `T`/`Q` entry over
the stable `Σ` alphabet — which survives most code changes. That buys two things
a manual suite cannot:

- the witness replays against the **new** code (ordinary regression); and
- an orphaned case is **machine-detectable** — when the obligation it guards is
  gone from the export, Ward flags the case stale instead of letting it silently
  pass. Test-rot becomes a reported condition, not a slow accretion.

Architecturally the corpus therefore lives **above** any single discharge
attestation: §12 binds one discharge to one export hash; the corpus is the
**trans-export memory** each new attestation consults and replays.

## Ward design deliverables

- **The corpus schema + identity keying.** The stored case (witness value/trace,
  the obligation it refutes over `Σ`, and provenance: which engine and which
  build first found it), content-addressed and deduplicated; the
  obligation-identity key and how it is matched against a new export.
- **Population hooks from each engine.** The retain path from §33 / §41 / §52 /
  §53 — automatic on every red verdict, with a deduplication rule so the same
  defect found twice is one case.
- **The replay mechanism.** Primarily replay against **real code** via §43 (an
  L1 counterexample may be a model-abstraction artifact — the real-code replay
  is the authoritative regression, and a model/code disagreement is itself a
  finding); plus model re-check (§30) and monitor replay (§51) where those are
  the natural home. Always-run, independent of §42's measure.
- **Orphan / staleness detection.** Detect when a case's obligation has left the
  export and report it (retire it, or surface it for human review) — the
  anti-rot mechanism the content-addressed key makes possible.
- **Governance + attestation binding.** The retention/expiry policy, and whether
  a case needs human confirmation to become **load-bearing** (block a build on
  recurrence) versus advisory; the corpus's replay result recorded in the
  discharge attestation (§12) as a signed, version-pinned "no known defect
  recurred as of this build." Coordinate any Ken-visible field back through ken
  `63 §5a`, like §42.

## The honesty boundary

A green corpus stays **`tested`** and means exactly *these specific past defects
do not recur* — never `proved`, never "no bugs" (§23 one-way gate; §53 honesty
guard). Its compliance value is precisely its honesty: a machine-checkable,
signed record that every historically-found defect's witness was replayed and
did not recur — stronger evidence than a manual "a test exists per bug"
checklist, and never more than it is.

Resolver: `OQ-regression-corpus` (§90); coupled to §42 (the measure it
backstops) and §12 (the attestation it feeds).
