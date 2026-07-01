# Contributing to Ward

Authoritative workflow lives in `docs/program/04-git-and-integration.md` once the
campaign lands it. Until then, three rules that matter:

1. **`main` is always green.** Every merge keeps the design coherent and its
   cross-references resolvable.
2. **Teams open PRs; they never merge.** A single **Integrator** is the only
   GitHub identity that merges to `main`; design and review roles propose.
   (See `agent/COORDINATION.md`.)
3. **Clean design.** Honor [CLEAN-ROOM.md](CLEAN-ROOM.md): copyleft reference
   tools are read for behavior, integrated under their license, and never copied
   into Ward source.

Conventions: wrap markdown at **80 columns**; Mermaid for diagrams; open each
spec/design section with a `> Status:` blockquote declaring its normativity.
