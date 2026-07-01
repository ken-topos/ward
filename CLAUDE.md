# Working in `ward`

Ward is Ken's behavioral-assurance sibling — the engine for what proof cannot
reach (one logic, two engines; Ken ADR 0006). This repo is at the **design
stage**.

## First, load your role

If you are an agent in the Ward federation: call `orientation()`, read
[`agent/COORDINATION.md`](agent/COORDINATION.md) (federation law) and
[`agent/MODELS.md`](agent/MODELS.md), then invoke your role playbook under
[`agent/playbooks/`](agent/playbooks/).

## Orientation

- **Mission + map:** [README.md](README.md).
- **The reasoning charter:** [`docs/PRINCIPLES.md`](docs/PRINCIPLES.md) — the
  priors every design call is weighed against when the spec doesn't dictate.
- **The Ken interface Ward is built against:** [`spec/10-seam/`](spec/10-seam/)
  — the assumption-boundary export, the discharge attestation, and the runtime
  CT validation. This is the **fixed input**; ken `spec/70-behavioral/` +
  `spec/60-security/{63,65}` are its authoritative source.
- **The campaign:** [`docs/program/`](docs/program/) — strategy, roadmap, work
  packages; living status in `DESIGN-PROGRESS.md`.

## Reference material — off-limits unless cleared

`local/refs/` (gitignored) holds external verification tools and research. Read
them only per [CLEAN-ROOM.md](CLEAN-ROOM.md): permissive refs to understand;
copyleft refs for behavior-not-expression, under the originality recheck. Never
copy reference source into Ward.

## Conventions

- Wrap markdown at **80 columns**. Mermaid for diagrams.
- Open each spec/design section with a `> Status:` blockquote.
