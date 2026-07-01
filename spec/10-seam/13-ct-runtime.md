# 13 — Runtime constant-time validation (the Ward runner)

> Status: **DESIGN SET (2026-07-01)** — the CT method's shape is fixed per
> operator direction: three measurement mechanisms under a user **assurance
> policy**, the leakage model as a recorded policy choice, CT-aware codegen, and
> strict outcome honesty. Residue (`OQ-ct-assurance`, the CT half spun out of
> `OQ-discharge-attestation`): each mechanism's tractability, the exact scope of
> the formal tier (recommended below, pending ratification), and the
> platform-pin mechanism. Ken lands the *static* `@ct` face and defers the
> *runtime* face to Ward (ken `spec/60-security/65-policy.md §2/§5a`, `61 §5a`,
> `64 §4.2`).

## The split

A constant-time requirement ("`KeyMaterial` is `@ct`") has **two faces**:

- **Static face — Ken's, landed.** A `@ct` value steering a branch / index /
  variable-time operation is a **compile error** (the `l_ct_sink` leakage-sink
  discipline, ken `61 §5a`). A *necessary precondition*, checked before anything
  runs.
- **Runtime face — Ward's.** The static face cannot certify a *timing*
  guarantee.

## Why timing needs a runtime face at all

Timing is **not a property of the program** — it is a property of
`program × compiler × microarchitecture × leakage-model`. Ken proved the
source-level necessary precondition by typing; but the actual timing depends on
codegen (`cmov`-vs-branch lowering), hardware (cache lines, ports, speculation),
and what the adversary can observe (ken `64 §4.2`, explicit). All of that lives
**below** Ken, which is why timing was delegated to "Ward + the toolchain." The
honest consequence, held throughout this chapter: **Ward validates constant-time
behavior empirically, under an explicit leakage model on a specific platform; it
does not certify "constant-time" unqualified** (except where the formal tier
gives a sound decision, still relative to a leakage model and platform).

## Three measurement mechanisms (support all, if tractable)

Selected per obligation by the **CT assurance policy** (below); a deployment may
demand a stronger tier for external endpoints than for internal ones.

| Mechanism | What it does | Cost | Outcome (§12) |
|---|---|---|---|
| **Statistical** (dudect-style) | two input classes, test for a timing-distribution difference | cheap, always available | `monitored` / `bounded` |
| **Hardware-assisted** | perf-counter / cycle-accurate harness | moderate; platform-specific | `monitored` / `bounded` |
| **Formal / binary-level** | sound CT verification of the **emitted binary** under a leakage model (Binsec/Rel, ct-verif class), and/or a CT-preserving codegen path | high | may reach `discharged` |

## Scope of the formal tier (recommended — pending ratification)

- **OUT of scope:** Ward building a **verified CT-preserving compiler** (Jasmin
  / CompCert-CT class). That is a research-scale compiler-verification effort,
  not Ward's mission.
- **IN scope:** (a) **CT-aware codegen** (the codegen constraint below), and (b)
  **consuming** an external CT-preserving backend **or** running a **sound
  binary-level CT verifier** as the high-assurance tier. The `discharged`
  outcome is reachable by verifying the *actual binary*, without Ward
  re-verifying a compiler.

## The CT assurance policy (user-facing, governed, recorded)

A **per-deployment policy** the operator authors — the analog, for CT, of the
sampling policy (§42) and Ken's security policy (`OQ-policy`): separately
authored, governed, and **recorded in the attestation** (§12). It selects, per
`@ct` obligation (or class):

- **The assurance tier** — which mechanism(s) must run, i.e. how strong a CT
  verdict this deployment requires.
- **The leakage model** — what the adversary is assumed to observe (wall-clock /
  cache state / branch predictor / …). This is a **policy choice** and it
  **bounds the strength of every CT claim** (ken `64 §4.2`); it is recorded so a
  verdict is always read as "CT *under this leakage model*," never absolutely.

The policy's *choices* are Ward-internal detail (they ride the `ct[]` / `policy`
fields of §12); the *outcome* they produce is the Ken-visible obligation
`outcome`.

## The codegen constraint (CT-preservation through compilation)

Ken's static face is **source-level**; the compiler between source and binary
can **destroy** constant-timeness (lower a `cmov` to a branch, introduce a
data-dependent access). So CT support imposes a **standing constraint on Ward's
codegen** (a requirement on `OQ-ward-stack`, not a validation-time
afterthought):

- Lower `@ct`-guarded operations to **constant-time primitives** — branchless
  select (`cmov`), no secret-dependent branch / index / memory access.
- **Preserve the IR features that guarantee CT across subsequent passes** —
  carry the `@ct` marking (directly or indirectly) so a later optimization pass
  cannot silently reintroduce a data-dependent operation the marker forbids.

This is the "CT-preservation through compilation" half ken `64 §4.2` names; the
three mechanisms above are how Ward *checks* it actually held on the emitted
binary.

## Platform binding and the gate (resolves loose thread — platform)

A CT verdict is valid only for the `(codegen, microarchitecture)` it was
measured on. **Recommended mechanism:** fold the platform into the
**build/provenance identity**, so an attestation's CT outcomes are
platform-specific *by construction* — deploying to an unvalidated platform means
the target's build (and thus its provenance/export match, §12 gate check 2) does
not correspond, so the CT obligations carry **no passing outcome** and the gate
**fails closed** using only the Ken-visible `outcome`. No new Ken-visible field,
no dependence on a Ward-internal field.

If a deployment reuses one build across platforms (so platform is *not* already
in the build identity), the fallback is a **gate-visible platform pin** — a
small ratification delta with ken, coordinated like the other Ken-visible fields
(`OQ-export-wire` discipline). **Recommendation: the provenance-fold**; it needs
no contract change. Either way the invariant is: *a CT verdict never applies to
a platform it was not measured on.* The validated platform is recorded in `ct[]`
for the audit record regardless.

## The `@ct` obligation's attestation tag (resolves loose thread — channel)

Two artifacts, reconciled: in the **export**, `@ct` information rides the `P`
channel as a **boundary label** (ken `71 §2.1`); in the **attestation**, a CT
discharge is tagged **`Q@ct`** — the ratified field value (§12) — marking it the
**runtime timing face of a static `@ct` guarantee**, distinct from a generic `P`
assumption or a `T` temporal obligation. The export channel (P boundary label)
and the attestation field tag (`Q@ct`) are different artifacts; both hold, and
the runner maps the former to the latter when it records the discharge.

## Outcome honesty (do not overstate)

- Statistical / hardware-assisted → **`monitored`** (or `bounded`), **never
  `discharged`**: the verdict means "no leak *detected* under distribution `D`,
  leakage model `L`, platform `P`," not a proof of constant-time.
- Formal / binary-level, **sound for `L`** → may be **`discharged`**, still
  **relative to `L` and `P`** — recorded as such.
- The attestation records **mechanism + leakage model + platform** with every CT
  verdict; a bare "CT: pass" is **forbidden**. As with every discharge, a CT
  verdict is classical evidence, **never promoted to a Ken `Q`** (§23, the
  one-way gate; §12).

## Ward's obligations here

- **Design + implement** the three mechanisms (tractability of each is the
  residue, `OQ-ct-assurance`); read the shelf's CT references (`local/refs`:
  `dudect`, `binsec`, `jasmin` — behavior/approach only per `CLEAN-ROOM.md`).
- **The CT assurance policy** — schema + governance, indexed over the exported
  `@ct` obligations, leakage model as a first-class recorded choice.
- **The CT-aware codegen discipline** — carried as a requirement into
  `OQ-ward-stack`.
- **The platform-pin mechanism** — provenance-fold (recommended) or the
  gate-visible pin (coordinate with ken if needed).
- **Emit into §12** — verdict + mechanism + leakage model + platform, tagged
  `Q@ct`, never promoted.
- Resolver: `OQ-ct-assurance` (the CT-method residue of
  `OQ-discharge-attestation`).
