# Model tiers

Ward runs a heterogeneous fleet to concentrate spend on the highest-judgment work
while using cheaper models for coordination and mechanical gates. Ward is at the
**design stage** — a design campaign, high-judgment work throughout — so the
fleet is two tiers, not three: there is **no T2 implementer tier yet** (no code
to write). The tiers:

| Tier | Model | Roles | Why |
|---|---|---|---|
| **T1** | **Opus 4.8 (`claude-opus-4-8[1m]`, high effort, extended thinking)** | Design-author, Design-reviewer, **Steward**, **Architect** | Highest judgment; the design enclave; design + workflow authority. These calls are worth the most. |
| **T3** | **DeepSeek V4 Pro (`deepseek-v4-pro`)** | Design-team Leader, Integrator, Librarian | Coordination, mechanical gates, doc observation. |

Design is high-judgment work — like Ken's spec enclave — so the design **authors**
and the design **reviewer** run on **T1 Opus**, together with the Steward and the
Architect. The design **leader**, the Integrator, and the Librarian coordinate and
gate, so they run on **T3 DeepSeek**.

The operator is the human product owner; the Steward is the primary proxy into
the federation.

## Knobs (tune by observed quality, not up front)

- **A T2 build tier appears only when Ward starts implementing** the L1/L2/L3
  engines. Until then every role is T1 or T3; do not provision a build model for
  the design campaign.
- **Integrator stays T3.** It enforces gates and merges; the deep correctness and
  design review is the **Architect's** (T1) job on the merge Decision, so the
  Integrator does not need a strong model.

## Grounding × models (load-bearing)

Ward is a **fresh MIT design with no AGPL prototype**, so there is **no
excluded-inspiration clean-room** to police. The model tiering instead
concentrates the two grounding sources that *do* need judgment on the T1 enclave:

- **The Ken interface is the authoritative grounding, and it is clean by
  construction.** Ward designs against Ken's **public, generated** seam — the
  assumption-boundary export (`Q`/`P`/`Σ`/`T`/`G` + ITF traces) and ADR 0006 —
  which is MIT-compatible and safe to send anywhere. The seam is **one-way**
  (Ken → Ward); Ward never feeds conclusions back.
- **Only the T1 Opus design enclave reads copyleft verification tools.** Apalache,
  Quint, MOP, and the RV/PBT frameworks are **⚠ copyleft** (GPL/AGPL/CeCILL in
  places). The enclave (Design-author / Design-reviewer, Anthropic-hosted) may
  read them for **behavior and approach only** — never structure, identifiers, or
  ordering — under the originality recheck (the reviewer's gate). **Never send
  copyleft source text to DeepSeek**, and **never vendor** copyleft into Ward's
  MIT tree. Permissive references may be read by the enclave but, like all refs,
  are not a source for anyone downstream — Ward's own design is authored in Ward's
  own words.
- **The DeepSeek coordination roles read only Ward's own artifacts** (the
  `design/` tree, clean by construction) and the Ken seam — never copyleft tool
  source. Coordinate the readers; don't become one.

## Portability

Playbooks and `COORDINATION.md` are written **model-agnostic** — no reliance on
Claude-specific behaviors — because the same coordination law must hold across
Anthropic and DeepSeek agents.

**Routing mechanism (hybrid).** Each agent is a normal Claude Code process
launched by `moot up`, which reads its `model` + `effort` per role from
`moot.toml` (`[agents.<role>]`) and passes `--model`/`--effort`. Provider routing
is **hybrid**:

- **Opus enclave** (4 roles: Design-author, Design-reviewer, Steward, Architect)
  runs **direct on the Anthropic subscription** (OAuth) — never through the proxy
  (the convo guardrail rejects subscription tokens at the proxy).
- **DeepSeek roles** (Design-leader, Integrator, Librarian) route through a
  **local LLM proxy** (`mootup_harness_sdk.llm_proxy`, `127.0.0.1:8090`) that
  dispatches by model prefix (`deepseek-*` → DeepSeek); the proxy holds the
  upstream key from `/home/node/.secrets/`.

Per-role selection is declared in `moot.toml`: each DeepSeek role's
`[agents.<role>].env` sets `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN`
(`${secret:llm-proxy-secret}`, resolved from `/home/node/.secrets/` at launch;
requires mootup ≥ 0.5.4), and moot injects it into the agent's launch env;
enclave roles set no `env`, so they use the default Anthropic endpoint + OAuth.
It must be **`ANTHROPIC_AUTH_TOKEN`** (a Bearer), not `ANTHROPIC_API_KEY`: the
proxy reads `Authorization: Bearer`, and AUTH_TOKEN also overrides the shared
claude.ai OAuth login (from the enclave's `/login`) that DeepSeek agents would
otherwise send — which the proxy rejects as a subscription token. The proxy is
started by `run-llm-proxy.sh`. Operator runbook:
`local/mootup-agent-backends-setup.md`.
</content>
