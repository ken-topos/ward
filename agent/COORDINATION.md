# Ward coordination law (read by every agent)

Cross-cutting rules for every Ward agent, regardless of role, team, or model.
Role-specific discipline is in `playbooks/`; model tiers are in `MODELS.md`; the
git model is in `../docs/program/04-git-and-integration.md`. These rules are
adapted from **Ken's federation law**, itself hard-won from mootup team lessons;
each exists because skipping it caused a real stall or a real bug. They must hold
identically across Opus and DeepSeek agents.

**What Ward is (the mission every agent shares).** Ward is Ken's
**behavioral-assurance sibling** (Ken ADR 0006). *One logic, two engines:* Ken
proves the **static / propositional / intuitionistic** fragment of a
topos-internal logic; Ward discharges the **temporal / modal** fragment of the
**same** logic — liveness, fairness, concurrency and distribution, the environment
a proof assumed, nondeterminism, and agentic outputs with no propositional oracle
— by **model → test → monitor**. Ward's engines are **L1** model-checking
(Quint/Apalache), **L2** spec-driven test generation + a sampling policy, and
**L3** runtime verification / trace conformance / the agentic safety envelope.
Ward consumes Ken's **assumption-boundary export** — a generated, content-addressed
assume-guarantee contract (`Q` guarantees / `P` assumptions / `Σ` event alphabet /
`T` delegated obligations / `G` generator support, plus ITF traces) — and **never
feeds conclusions back**: the seam is **strictly one-way** (Ken → Ward). Ward also
owns the **discharge attestation** and the runtime **constant-time (CT)
validation** Ken defers to it. Ward is **MIT and a fresh, clean design** — not
derived from any AGPL prototype (no excluded-inspiration clean-room; it reads
copyleft verification tools for *behavior, not expression*). Ward is at the
**design stage** — a design campaign, the analog of Ken's spec campaign.

## 0. The shape: a ring of rings

- **Within a team — a sequential token-ring.** Generally one agent is active at a
  time; the others are in a supporting role, called on when the active agent needs
  them (e.g. a design-author asks the reviewer for a clarification). Keeping the
  whole team on one task maximizes coherence and effectiveness. Do not fan a team
  onto several tasks to chase parallelism — coherence beats it. This includes
  waiting on CI: once a WP is published, the team **waits idle** for its CI run
  rather than pipelining or stacking work. Idle is cheap and load-friendly;
  throughput comes from other teams' rings, not from this one multitasking.
- **Across teams — parallel.** The teams are independent rings spinning at once;
  that parallelism is the entire reason the work is articulated into teams. During
  the design campaign the primary ring is the **design enclave**; more rings appear
  as Ward grows. The rings couple at only three points: merges to `main` (via the
  Integrator), the roadmap gate dependencies, and the **sanctioned cross-team query
  edges** (§9, §11). Keep that coupling thin — it is what serializes the federation
  if abused.

## 1. Event-driven, never poll

After you finish a unit of work or hand off, **post, set status, and stop.** Do not
`/loop`, self-wake, or poll for replies. The notification system delivers what you
need; polling burns tokens for zero value. A missing notification is a *stall* —
catching stalls is the team leader's watchdog job, not yours. Only team leaders, the
Integrator, and the Steward run schedulers.

## 2. Mention discipline

**Mention an agent iff the next move in the workflow is theirs.** Every mention
costs the recipient tokens and fires a notification.

**A mootup mention is the *only* way you reach a teammate.** Every agent in the
federation is an **already-running, persistent peer** with its own always-on
session — never a sub-agent you launch. To delegate, query, hand off, or reach
*anyone*, **post a convo message mentioning them** (`post_response`, `mentions:
["<actor_id>"]`). **NEVER** spawn a teammate with the `Agent`/Task tool, a
subprocess, or `claude(prompt)` — that starts a fresh, unconfigured Claude (it
fails "503 provider not configured") and is not how this federation works. This has
bitten DeepSeek leaders mis-reading "hand the WP to your author" as a launch instead
of a mention. Assignment, query, handoff — all are mentions; local git only.

- Handoff that passes work to B → mention **B only**.
- "Your request is done," nothing pending → mention **nobody**.
- **The escalation triangle:** when you answer X's question but the *next move*
  belongs to Y, mention **Y**, not X. Naming Y in prose without a real mention is
  the classic silent stall.
- **Route an action-expecting mention only to a *live* agent — never a `moot init`
  template placeholder.** The space carries dead template participants (`Design`,
  `Leader`, `Reviewer`, …) left by scaffolding; a review/handoff/kickoff mention
  routed to one is a **silent no-op** — it wakes no one. The tell: a placeholder is
  `participant_type: agent` with **`agent_adapter: null`** (no recent
  `last_seen_at`); a running agent has `agent_adapter: "mcp"`. Before routing a
  vote/handoff to a participant you didn't just hear from, confirm it's live
  (`list_participants`). Ken's federation traced a whole merge-gate breach to a
  soundness-review mention routed to a dead placeholder, so the gate never ran.
- **Thread your reply — don't scatter to the space root.** Every event you receive
  carries a `thread_id`. When you respond to a message that belongs to a thread — a
  kickoff, assignment, query, handoff, review request, or retro call — **reply
  *into that thread*** (`post_response` with `thread_id` set to that message's
  thread; or `parent_event_id` on the first reply, which opens the thread). A bare
  `post_response` with no thread drops your message at the space root, **scattering
  one WP's conversation across the space** so the next reader — and the Steward
  harvesting retros — can't follow the exchange. **One WP = one thread:** kickoff →
  ack → queries → handoff → merge Decision → retros all live under it. **`reply_to`
  needs an *existing* thread** — it 404s ("Thread not found") on a **top-level**
  event that has no thread yet (e.g. a kickoff, which is itself a root post). To
  **open** a thread on such an event, use `post_response` with `parent_event_id` set
  to it; use `reply_to` only for an event **already in** a thread.

### 2a. Convo call cheat-sheet (form valid parameters — recurring failure)

Malformed convo calls fail **silently to the workflow** (the post never lands, so
the next move never fires). The two parameters that get rejected:

- **`message_type` MUST be one of the backend enum values** — guessing a
  natural-language type is a 400 reject. **Valid:** `question`, `code_share`,
  `git_request`, `review_request`, `retro`, `status_update`, `bug`, `feature`,
  `decision_propagated`, `pause_issued`, `connection_status`. **REJECTED (do not
  use):** `message`, `kickoff`, `assignment`, `merge_ready`, `handoff`, `nudge`,
  `ack`. Map your *intent* to a valid type:
  | Intent | `message_type` |
  |---|---|
  | kickoff / assign a WP / hand work to a teammate / deliver an artifact | `code_share` |
  | question / query / nudge / ack / general note | `question` |
  | ask the Integrator to publish+merge a branch (the old "merge_ready") | `git_request` |
  | reviewer→leader merge-Decision request; "request review" | `review_request` |
  | a retro bullet-set | `retro` |
  | a status line | `status_update` |
  | a defect note to another team | `bug` |
  When unsure, **`question`** is always accepted. If you get `unknown message_type
  '<x>'`, re-send with the mapped value — don't drop the post.
- **`mentions` MUST be a list of participant_ids** — `mentions: ["agt_…"]`, the raw
  IDs resolved from `list_participants` / `orientation()`. **Not** display names
  (`["design-leader"]` ✗), **not** `@name` in the `text` (that fires no
  notification — §2), **not** a bare string. One id per actor whose move is next.
- **`propose_decision`**: give the decision `text`/title + the WP/branch; mention
  the required reviewers (Architect always; the design **reviewer** only on
  `design/` paths — §13/diff-scope). **`thread_id`/`parent_event_id`** take an
  event/thread id string (§2), never a name. **`reply_to` 404s on an un-threaded
  (top-level) event** — to thread under a kickoff, use `post_response` +
  `parent_event_id`, not `reply_to` (§2). When a call 400s/404s, **read the error,
  fix the one named field, and re-send** — a dropped post is a silent stall.

## 3. Status = what you're doing, in your own words

Three liveness signals exist; only the third is yours to post:
1. connection (automatic — never post "I'm online");
2. activity (file/transcript mtime — automatic);
3. **semantic status** — "drafting the `Q`/`P` projection", "blocked-on-architect:
   one-way-gate". Agent-composed, never auto-classified. Update it on: receiving a
   handoff, completing one, changing focus, and becoming idle or blocked.

## 4. Threads are the spine

One mootup thread per work item; the kickoff message *is* the spine. All handoffs,
questions, status, and retros for that item are **replies in that thread** — a
top-level post fragments the work. After any context reset/compaction, resolve the
live thread from fresh context; do **not** reuse a thread/event ID from a
summarized memory (it may be stale).

**Post at both boundaries — on receiving work and on handing off.** When you **pick
up** a task, post a brief *taking-this* ack in its WP thread and set your semantic
status to it (§3); when you **finish or hand off**, post what you did + the mention
of whoever moves next (§2) and update your status again. **Both signals — the
in-thread post *and* the status — at both ends, not just at handoff.** The pickup
ack is what keeps the flow legible in the web view and preserves the full
interaction history; without it there is a silent gap between assignment and
completion that no one can audit or replay.

**Title convention (the single-space articulation tag).** The whole federation runs
in one space (`ward-topos`), so a thread's **title is its team tag** — there is no
separate per-team channel. Begin every kickoff title with a tag, then a terse
subject:

- **Work-package threads:** the WP ID, whose letter encodes the owning
  workstream — `S*` Seam (Ken-interface consumption / the one-way gate) · `M*` L1
  model-checking (Quint/Apalache) · `G*` L2 test-generation + sampling · `R*` L3
  runtime verification / trace conformance / agentic · `A*` discharge attestation ·
  `C*` constant-time validation · `F*` foundation · `T*` tooling · `D*` a
  design-general section. E.g. `S1: assume-guarantee export projection`.
- **Non-WP threads** (a cross-team query, a process/retro note): prefix with the
  originating role/team and an arrow if it targets one — `steward: cadence pass`,
  `author→architect: one-way-gate soundness`.

This makes the one space scannable and `search_spaces`-filterable by workstream
without the cost of per-team spaces, and pairs with the `message_type` taxonomy
(§8): the title says *who/what*, the type says *what kind*, and `list_threads`
filters on type with per-actor unread counts. Keep both accurate.

## 5. Decisions are for judgment, not deduction

Open a mootup Decision (`propose_decision`) for choices with tradeoffs where a
reasonable peer might differ — the seam projection, an engine's translation
faithfulness, a sampling-policy boundary, an API shape. Do **not** open one for
deductive/mechanical choices (a bug fix is not a decision). Decisions are how future
agents query *why* Ward is the way it is. **Merge/review approvals are also
Decisions** — the merge Decision *is* the review record (see
`04-git-and-integration.md`).

## 6. Resolve when structurally determined; escalate only real forks

Before escalating or querying another team, ask: *is there a strategic choice
between materially different futures?* If **no** — the published `design/` + the Ken
interface + the seam invariants already determine the answer; resolve it yourself
and record the resolution with a cited rationale (`file:line`, a `design/` §, or a
Ken `71`/ADR-0006 pointer). If **yes** — escalate. For grounding questions, "the
published design" means `design/` + the Ken interface, never a copyleft tool's
source. This filter is the volume control on the cross-team query edges (§11):
without it, the design enclave and the Architect become bottlenecks.

## 7. Ground every premise before locking

Before locking a design claim, an ADR, or an acceptance criterion, verify each
premise against reality: "the Ken export carries X" → cite `71` and the projection;
"this tool behaves like Y" → read the tool's *behavior* end-to-end. For a language
whose whole job is *assurance*, a design claim about the seam must be checked
against the Ken interface, not assumed. The disciplines below are **inherited from
Ken's federation** (each promoted there after repeated, cross-team validation) and
re-ground cleanly onto Ward's seam and engines:

**Verify the *property*, not the representative case.** A passing obvious-case
example and correct *prose* are **false signals** — the design or engine can still
be wrong in the cases you didn't exercise. Ken learned this twice (a determinism
test that passed only because a `BTreeMap` pre-sorted, never exercising the encoder;
a positivity *algorithm* that silently dropped the hidden-negative positions its
correct prose described). Ward's analog: an engine that passes a conforming trace
can still mishandle an interleaving, a boundary equivalence-class, or the
unknown-hole projection. So — for design authoring, review, and acceptance alike:
when a mechanism has N guard/discard positions, **exercise each independently** (one
case per guard) plus one where the problem hides behind indirection; write
algorithmic/protocol pseudocode **defensively** (every discarding position
explicit-guarded, every guard backed by a rejection case). The obvious case passing
≠ the property holds.

**A discriminator boundary needs a non-degenerate *pair*, not a positive case.**
When a rule, classification, projection, or orientation decides between two outcomes
on a boundary (accept/reject, `proved`/`assumed`, `Q`/`P`, `delegated`/`proved`,
in-`Σ`/orphan), a single positive case is **green-vs-green** under the exact swap it
should catch: flip the order/orientation and the lone case still passes, for the
wrong reason. The net is **one non-degenerate pair on a *shared* input** — the two
states that must bucket differently, identical in every other respect. This is
**Ward's native soundness surface**: Ken's export flips the *same* postcondition
into `Q` when proved and into `P` (tagged `unknown`) when its proof is a hole
(Ken AC2/I1), and no Ward verdict ever lands in `proved` (the one-way gate,
AC6/I4). Author the pair, not the positive case. And the discriminator the case
keys on must be a **structural signal** (the export's per-entry status tag,
`trusted_base()` membership, the content hash, a real trace through a real
monitor), never a **self-reported string** an untrusted layer can forge.

**Exhaustive-by-construction: make load-bearing completeness a *compile error*, not
a test.** When the **completeness** of an enumeration is load-bearing — every export
claim classified into a field, every event in `Σ` handled, every engine verdict
projected — encode it as a **single `match` over a sealed type with NO `_ =>`
catch-all arm**, so adding a variant without handling it is a **compile error**, not
a silent drop. This is *structural*, stronger than any test. Ward's canonical
instances: the **no-measure `G` seal** (Ken `71 §4.1` — the support type admits no
field a weight could inhabit, so a leaked measure is *unrepresentable*, not merely
discouraged) and the **status→field totality** of the seam projection (every source
status has an explicit image; the one non-exporting verdict — a refuted claim — is
an explicit arm, not a skip). Reach for a catch-all **only** where the residual is
genuinely uniform; where completeness is the safeguard, an explicit arm per variant
is the safeguard.

## 8. Message-type taxonomy (routing metadata)

Tag each message with a type; the **first line is the thread title** — no `[TYPE]`
prefix in the body. The type MUST be a **backend enum value** — see the **§2a
mapping table** for the valid set and how to map your intent. In particular, a local
`wp/<ID>` branch ready for the Integrator to publish + merge is `git_request` (the
old `merge_ready` is rejected), and a reviewer→leader merge-Decision request is
`review_request`. When unsure, `question` is always accepted.

## 9. Topology is invariant — including the query edges

Who hands off to whom, who reviews, who merges, and **which cross-team query edges
exist** is operator-owned and fixed. The sanctioned edges are exactly:

- any team → **design enclave** leader — seam/behavioral-design questions ("what
  must this do to consume Ken's export soundly?").
- any team → **Architect** — component-design questions ("how should I structure
  this / which design?").
- any team → **Steward** — scope/priority (forwarded to the operator),
  workflow/process, and research requests.
- any team → **Integrator** — merge status (usually via the team's own leader).

Agents may improve *what they do inside a node*, never *add a communication edge or
a review cycle* between nodes. When integrating a retro lesson, reject any
carry-forward that would add/move an edge — and do not soften the rejection to
"candidate, watch one more run." That softening is how coordination entropy creeps
in.

## 10. Knowledge promotion: retro → synthesis → promotion ladder

- **The retro is a mandatory step, not an afterthought.** A work package is not
  *done* until its retro is in. The moment a WP's work is verified/merged, every
  working agent in the ring (design-author, design-reviewer) posts a short
  **`retro`** in the WP's thread — three bullets: **trap** (what cost time, or a
  defect the process caught or missed), **held** (a discipline that worked, with its
  prior-run validation count if it has one), and **carry** (a candidate rule to
  promote). Tag each bullet node-internal or topology-touching, so the Steward's
  invariance filter (§9) is pre-sorted.
- **The leader collects and hands off.** When a WP merges, the team leader confirms
  each working agent's retro landed, adds a one-bullet coordination retro, and posts
  a `retro`-typed "retros in" to the Steward with the WP ID and pointers. 15-min
  timeout: hand off what is in and name who is missing. This rides the existing
  team→Steward workflow edge (§9) — it adds no new edge.
- The **Steward** harvests retros across teams and promotes lessons up a ladder (see
  the steward playbook): team-local → archetype source → this file.
- A lesson promotes only when it passes all three: **(a) validated across ≥3 runs
  *or* independently in ≥2 teams, (b) effort-/model-/operator-agnostic, (c) a
  normative rule, not a one-off fact.** Exception: an explicit operator correction
  promotes on a single data point. On promotion, retire the source note atomically.
  Cross-team replication is a *stronger* generalization signal than single-team
  repetition — use it.

## 11. Cross-team query protocol

The edges in §9 are thin synchronous couplings between otherwise-parallel rings. Use
them sparingly and always event-driven:

1. **Filter first (§6).** Most "what should I do here" answers are already in
   `design/` + the Ken interface + the component design. Only a genuine gap or fork
   earns a query.
2. **Ask and stop.** Post a `question` mentioning **only** the target's leader
   (design-leader / Architect / Steward), set status `blocked-on-<target>`, and
   stop. Resume on notification — never poll.
3. **Bias to staying on-task.** Your team's default is to *wait out* a short block,
   preserving ring coherence; your leader reorders to an independent ready task only
   when the block is genuinely long.
4. **Front-desk on the answering side.** The target's leader triages to protect its
   own ring's focus — answers trivial/known questions itself, batches non-urgent
   ones, interrupts its active agent only for true blockers.
5. **Outcomes:** a quick interpretive answer; a **durable artifact edit** (a
   `design/` clarification + an acceptance hook, or a component-design note) so the
   next team never asks again; or, for a real fork, a **Decision**. Every query
   should leave the shared artifacts better — the query rate is a health gauge, and
   it should decay over time.

## 12. Resource discipline (shared 8-core / 16 GB laptop)

Ward is at the **design stage**, so there is **no heavy build tier yet** — the
compute concern is lighter than a code-building federation's, but the shared dev box
is still small and OOM-prone.

- **A resident agent costs RAM even when idle.** **Idle = paused.** If your ring is
  blocked or waiting (including waiting on a CI run), quiesce; don't hold the box
  hot. Never spawn hand-rolled background loops (e.g. `python3 -` markdown-reflow
  heredocs) — they infinite-loop, orphan, and leak GBs all session (the Steward's
  watchdog scans for exactly this, §7b of the steward playbook).
- **When Ward gains an implementation stack** (the L1/L2/L3 engines get built), the
  full build-slot discipline applies then, exactly as Ken's: build/test only through
  a machine-wide-locked wrapper (one build at a time across all agents), scope to
  the touched crate, run full/`--release` builds and the whole conformance suite in
  **CI**, not on the laptop, and share the compiler cache. Until then, keep sessions
  lean and quiesce when idle.
- This is a *current-hardware* constraint, not a design value — it relaxes as
  hardware grows (the Steward/operator raises the caps; do not raise them
  unilaterally).

## 13. Liveness: keep the rings turning

Token rings stall — an agent finishes, forgets to hand off, and the ring goes quiet.
Treat stalls as the **default** failure mode, defended in depth by three recurring
watchdogs, each catching the layer below it failing:

- **Team leader → its own ring.** Enumerated patterns: handed-off-but-silent,
  merge-Decision-open-no-reviewer, blocked-without-a-blocker-mention,
  idle-with-ready-work.
- **Integrator → the merge pipeline.** Branch-published-CI-pending-too-long,
  CI-green-but-Decision-unresolved, Decision-approved-but-CI-red,
  approved-and-green-but-unmerged.
- **Steward → the federation (the backstop).** A whole team idle, a *stalled
  leader*, a dropped cross-team query, a blocked dependency chain, no movement toward
  the active gate. The watcher-of-watchers — it catches a watchdog that itself
  stalled.

Rules for every layer:
- **Enumerate the stall patterns explicitly** in the watchdog prompt — a generic
  "check for activity" misses the nuance.
- **Diagnose before you restart.** Capture the stalled agent's state first; a blind
  restart no-ops a permission-prompt or rate-limit stall.
- **Distinguish waiting from stalling.** A team idle while its CI run is *in
  progress* is normal, not a stall — leave it alone. Recover only when CI has
  *finished* and no one took the next step (open the merge Decision, vote it, fix
  red, merge).
- **Graduated recovery:** detect → mention the one blocked agent → re-mention next
  interval → escalate up the chain.
- **Escalation chain:** member → team leader → Steward → the operator. The buck
  stops at the operator (human): if the Steward goes quiet, the absence of its
  updates is the operator's signal. Watchdogs are the only schedulers (§1); everyone
  else is event-driven.
- **Arm your watchdog with a *private* `CronCreate` timer — NOT the convo
  `schedule_call`.** A scheduler (team leader, Integrator, Steward) sets up its
  recurring pass at **session start, while its ring/pipeline has open work**, with
  the Claude Code harness tool **`CronCreate`** — e.g.
  `CronCreate(cron="7,17,27,37,47,57 * * * *", prompt="Watchdog tick: pull
  get_recent_context, scan the enumerated stall patterns, mention only a blocked
  agent; if clear, do nothing", recurring=true)`. `CronCreate` **enqueues a prompt
  into your own session** — it posts **nothing** to the convo space — and fires only
  while you're idle. On each fire you run your *own* direct `get_recent_context` /
  `get_space_status` read (private, not posted) and do the stall-pattern assessment
  + recovery above, **messaging the space only when there is an actual stall to
  nudge.**
  **Do NOT use the convo `schedule_call`** for a watchdog: it executes the read *on
  the backend* and posts the result back into the space as a **System event visible
  to every participant** — pure broadcast noise (and the `get_recent_context`
  variant reads its own prior fires and recursively nests them — an exponential
  self-feeding loop). A watchdog is a *private* wake, not a public post;
  `CronCreate` is private, `schedule_call` is not. **A scheduler that never arms its
  watchdog catches nothing** — Ken's operator caught exactly this (a reviewer-
  approved WP left unmerged because the leader wasn't watching).
- **`CronCreate` is session-local, which is a feature — it cannot orphan across a
  restart.** A `durable: false` (default) cron **dies when your session exits**, so
  you simply **re-arm it at session start** while you have open work, and
  **`CronDelete` it when your ring/WP closes** (`CronList` shows the jobs you own;
  reconcile after every compaction). This is strictly safer than the convo timer,
  which is cancellable **only by its creating session** and, if you compacted
  without recording its `timer_id`, became an un-killable orphan firing stale
  context until the operator killed it at the source. Recurring crons also
  auto-expire after 7 days. **If you still hold a convo `schedule_call` timer from
  old guidance, `cancel_call` it and re-arm with `CronCreate`.**

## 14. Agents never touch GitHub; the Integrator is the gateway

**Only the Integrator has GitHub credentials.** Every other agent does **local git
only** in its worktree (commit, rebase onto the already-fetched `origin/main`) — no
`gh`, no push, no fetch, no token, no PR. The Integrator is the federation's sole
GitHub-network actor: it pushes `wp/<ID>` branches to trigger CI, reads the checks,
merges, and fetches `main` (`../docs/program/04-git-and-integration.md`).

- **Agents get no GitHub notifications and never poll GitHub.** Because GitHub is
  one identity's concern, every actionable signal reaches the fleet only as a
  **mootup message that mentions the actor whose move it is.** Act on the mention,
  not on a timer.
- **CI is the Integrator's to watch — never a worker's.** Only the Integrator can
  see CI. It reads check status for the branches it published as part of its
  *existing* watchdog pass (`gh pr checks` / the checks API) and posts the outcome:
  red → mention the author (team space) with the failing job; green → advance toward
  merge. The optional `ward-ci` bridge mirrors `check_suite` results automatically;
  until then the Integrator posts them. After you hand a WP off, you **stop** and
  learn a red result from a mention.
- **Landing integrity: a merge isn't trusted until *verified on `main`* — and a
  multi-piece WP isn't landed until *every* piece is.** A **squash-merge can
  silently drop pieces** of an assembly built from multiple cherry-picks. Ken's
  federation shipped a fix's code piece while silently dropping its spec +
  conformance pieces, leaving the corpus guarding nothing — and it **survived the
  ship**, invisible in every "shipped" status/notification, caught only by grounding
  against the landed files. So: (1) **don't trust a "shipped `<sha>`" notification or
  a status line** that a thing landed — for anything load-bearing, confirm it on
  `origin/main` (the files: `git grep`/`git show`). (2) **An approved N-piece
  erratum/WP is not "landed" until you verify each piece on `main`** — the
  Integrator/leader who assembles a multi-cherry-pick branch checks the post-merge
  `main` carries all of them. (3) **Authors ground the WP *base* against the landed
  corpus, not the notifications,** before building on it.
  **(4) PREVENTIVE — a multi-piece erratum is ONE branch, ONE Decision.** The
  drop's **root cause:** when one piece has a narrow diff-scope (touching only its
  own paths), it pulls only that path's reviewers and merges **alone** — the sibling
  pieces, sitting on *other* branches, are never in the merged tree. **Fix:**
  assemble **all** N pieces onto a **single** `wp/<erratum>` branch *before* the
  merge Decision — the combined diff-scope then touches every owned path, correctly
  pulls each required vote, and all N commits ride **one squash** to `main`
  together. Never publish one piece on its own narrow branch while siblings wait.
  **The Integrator confirms the Decision's branch carries every cited piece before
  merge.**
  **(5) Single-branch is necessary but NOT sufficient — verify an assembled tip on
  TWO axes, against CURRENT `main`, right before the Decision.** One branch
  guarantees the pieces are *together in one diff*; it does **not** guarantee they
  are **complete** or that the **base is current**. So before the merge Decision,
  re-run (right then — *"rebased onto current main" is a perishable claim*):
  **content** — `git diff <author-full-tip>:<file> <assembled>:<file>` **EMPTY**,
  taking **all** the author's commits since merge-base (a dropped follow-up is
  invisible if you only diff the section you read); **base/scope** — `git diff --stat
  origin/main <assembled>` is **only** the WP's intended files and **the WP's deps
  are ancestors of the tip** (a stale base silently reverts unrelated landed
  siblings). A correct-content tip on a stale base is as unmergeable as the reverse.
  **Lane note:** the independent checker is *stronger verifying the assembler's tip
  against these gates than performing the rebase itself* — when grounding surfaces an
  assembly hazard, **flag it to the assembler**, don't reach past your lane into the
  git.
  **(6) TEMPORAL — a fold/erratum that races an in-flight merge must HOLD the merge
  or be an erratum-on-current-`main` from the start.** Points (1)–(5) assume the
  pieces sit on branches you control at Decision time; this one is about **timing**.
  A "supersedes X" line interlocks nothing, and decision-time "hasn't merged yet" is
  perishable within seconds (Ken squashed a pre-fold artifact to `main` *9 seconds
  before* the fold that fixed it landed). So: **author side** — never "fold + declare
  supersede" against a WP whose votes/merge are in flight; either mention the
  Integrator + leader to **HOLD the merge** *before* committing the fold, or author
  it as a fresh erratum-on-current-`main`. **Coordinator side** — "not a merge
  blocker" ≠ "merge *before* it lands"; when a fold is in-flight and the merge is
  seconds away, **briefly HOLD** the merge so the artifact is correct at first
  landing. The only reliable net remains **verify-on-main after**, never a
  declaration before.
- **Review is a mootup Decision — and a *soundness vote is a frontier-class
  responsibility*.** The Architect/design-reviewer read the diff from the shared
  local branch (`git diff origin/main...wp/<ID>`) and vote the merge Decision in
  mootup; there is no GitHub PR approval to mirror. **Two load-bearing reviewers,
  both Opus-tier:** the **Architect** (external soundness/design, always) and the
  design **reviewer** on `design/` paths — cast by the **frontier-class (Opus)**
  design-reviewer (the independent checker by role; design-author authors, so cannot
  self-review). The **DeepSeek coordinator (design-leader) assembles the Decision but
  does NOT cast the soundness vote** — soundness judgment needs the strongest model
  (MODELS.md tiering, made explicit for review routing).
- **The Integrator merges only on a *resolved* Decision, verified fresh — never on a
  `merge_ready` post's prose.** A `merge_ready` is a *request*; the **resolved
  Decision with recorded approvals** is the authorization. Before `gh pr merge` the
  Integrator re-reads `list_decisions` and confirms `status: resolved` (not
  `proposed`) with the Architect's (and the reviewer's, on `design/` paths) votes in
  — it never infers approval from a reviewer *named in prose* (Ken's federation
  skipped a merge gate exactly this way: "`(Architect + Reviewer)`" read as approval
  while the Architect never voted). Symmetrically, whoever posts `merge_ready` states
  `Decision: dec_XXX — status: resolved` (or `proposed — awaiting <reviewer>`) and
  fires **real @mentions** to reviewers' actor_ids (§2 live-participant), not prose
  names.
- **The Integrator mirrors each GitHub state change into mootup mentioning whoever
  moves next** — CI red → the author; merged → affected team leaders. A GitHub state
  change nobody mirrors is a silent stall.
- The full event→message map (what, where, mentioning whom, posted by whom) is in
  `../docs/program/04-git-and-integration.md §5`.

## 15. Context compaction is the Steward's (teams) or self (singletons)

Token efficiency: an agent should start each work package with a clean, minimal
context. Who triggers a compaction is fixed:

- **Teams are compacted by the Steward**, not by their own leader. The Steward
  `moot compact`s the **whole team** (e.g. the design enclave: design-leader +
  design-author + design-reviewer) **before delivering a WP** to the leader. Leaders
  never `moot compact` anyone — `request_context_reset` is self-only, so only the
  Steward can compact another agent (`moot compact`), and it does so for teams alone.
- **Gated by retros.** The Steward compacts a team **only after** its prior WP's
  retros are posted (compaction would otherwise summarize the retro away), and
  delivers the next WP **only after** compacting. So a team's WP boundary is: done →
  leader calls for retros in-thread → members post → leader signals the Steward
  "retros in" → Steward reviews → Steward compacts → next WP.
- **Singletons self-compact.** Agents with no team/leader — **Steward, Architect,
  Integrator, Librarian** — call `request_context_reset` at their own task
  boundaries (Architect after a review, Integrator after a merge, Librarian after a
  pass, Steward after a directing cycle).
- **Never mid-reasoning.** Compact only at a clean boundary; it summarizes away
  in-flight work.
- **Start new work from current `origin/main`.** A WP branch is born from the
  **fetched** ref — leaders cut `wp/<ID>` with `git branch wp/<ID>-<slug>
  origin/main`, and every member `git rebase origin/main` before working. Never build
  on stale local `main` or a stale worktree. New work = latest `main`.
- **On resume after a compaction, ground-truth your state before trusting the
  summary.** A compaction can summarize away the fact that you *finished* — so
  re-orient from reality, not the lossy summary: `orientation()` **plus** `git reflog
  -10`, `git status`, `git branch -vv`, **plus check unread mentions**
  (`orientation`'s unread count / `get_mentions`). Your pre-compact self's commits
  and checkouts are in the reflog; check there before concluding you (or a teammate)
  are "stalled" or re-doing delivered work. **Notification delivery is best-effort**
  — a mention that landed while your session was bouncing (e.g. a fleet restart) is
  recorded but may never have woken you, so a resume is when you catch it.
</content>
