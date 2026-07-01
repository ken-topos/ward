# 42 — The sampling policy: the measure Ken never supplies

> Status: DRAFT v0 — **Ward-designed, Ward-blocked in Ken.** Ken decided the
> measure lives **outside Ken source, durably**, per-deployment, governed like
> the security policy, with Ken's `G` partition as its vocabulary; the policy
> **language** is deferred *because it needs Ward's sampler* (ken
> `OQ-sampling-policy`, `71 §4`). This chapter is the **primary open design
> deliverable** of §40. Resolver: `OQ-sampling-policy`.

## What it is

`G` gives the **support** of the valid input domain but no **measure** — no
statement of which valid inputs are likely, important, or risky (§41; §11 I5).
That omission is correct: the measure is not a property of the *program*, it is
a property of the *deployment*. A payments service and a batch analytics job
share a type but weight its inhabitants completely differently. So Ken refuses
to bake a measure into source, and Ward supplies it as a **separately-authored,
per-deployment sampling policy** — the analog, on the behavioral side, of Ken's
security policy (`OQ-policy`): mandatory, separately governed, not silently
weakenable.

The policy answers: **given the exported partition, how should test effort be
distributed?** It is what turns L2 from "generate arbitrary valid inputs" into
"generate the inputs this deployment actually needs covered."

## Why it lives on Ward's side, durably

- **It is per-deployment, not per-program.** It changes with the operational
  environment (traffic shape, risk profile, SLA), not with the code — so it
  belongs with deployment configuration (`Dockerfile`/Terraform class, ken
  `OQ-sampling-policy`), durably versioned, not in Ken source.
- **Its vocabulary is Ken's, its weights are Ward's.** The policy indexes over
  the **exported partition** (§41): it names equivalence classes, boundaries,
  and cases Ken defined, and attaches weight/priority to them. It cannot invent
  a region Ken did not export (that would be an ungrounded test target) — the
  partition is the shared, checkable vocabulary.
- **It is governed.** Like the security policy, it is separately authored,
  reviewed, and attested; a discharge attestation (§12) records *which* sampling
  policy a test campaign ran under, so "tested" is meaningful — tested **under
  this measure**, not tested in the abstract.

## Ward design deliverables

- **The policy language** (the deferred piece, `OQ-sampling-policy`). A surface
  for expressing weights/priorities over the exported partition: how to name a
  class/boundary/case, how to weight it, how to compose policies, and how to
  keep it non-weakenable where a class is safety-critical. This is what Ken's
  deferral was **waiting on Ward's sampler to define**.
- **The sampler.** How weights become an actual generation distribution over
  §41's generators — coverage guarantees (every class sampled), risk-weighting,
  boundary emphasis, and the seed budget per campaign.
- **Governance + attestation binding.** How the policy is authored, reviewed,
  and pinned into the discharge attestation (§12) alongside the export hash and
  Ward version, so a `tested` verdict carries its measure. Coordinate the
  Ken-visible fields back through ken `63 §5a` (the attestation), the rest is
  Ward's.
- **The default policy.** A safe, coverage-complete default for a deployment
  that authors no policy — every partition class sampled, boundaries hit, no
  region silently skipped (an unweighted region is under-tested, not untested).

Resolver: `OQ-sampling-policy` (§90) — Ward closes it, coordinating the
attestation-visible surface with ken `63`.
