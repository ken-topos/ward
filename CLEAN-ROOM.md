# Clean-Room Policy — Ward

Ward is an **MIT-licensed, clean design**. Unlike Ken, Ward is **not** a
reimplementation of any prior system — it has **no excluded inspiration**. There
is no AGPL/GPL prototype it descends from; its design is authored from Ken's
public interface (the assumption-boundary export, ken `spec/70-behavioral/`) and
from first principles.

What Ward *does* consult is a shelf of **external verification tools and
research**. The discipline below keeps their licenses from tainting Ward's MIT
source. It reuses Ken's three-tier `local/refs/` pattern, **minus the excluded
tier**.

## Reference regime (`local/refs/`, gitignored, never committed)

- **Permissive refs** — Apache-2.0 / BSD / MIT tools Ward integrates or learns
  from (**Quint**, **Apalache**, TLA+ tooling, QuickChick, the ITF trace
  format). Readable to understand behavior and interfaces; **never vendored**
  into Ward source in violation of their license. Depending on a tool under its
  license is fine; copying its source into Ward is not.
- **Copyleft refs** — GPL / LGPL / AGPL / CeCILL tools Ward may study for
  *approach and behavior* (e.g. some model checkers; MOP / JavaMOP monitor
  synthesis). Read for **behavior, not expression**: describe *what* they do in
  Ward's own words; reproduce none of their *how* (structure, identifiers,
  ordering, comments). Subject to the **originality recheck** (below) before a
  design section that consulted them is treated as clean.
- **No excluded tier.** Ward has no "never consult" source today. If one is ever
  proposed — a copyleft implementation Ward would *port* rather than *integrate*
  — **stop and ask**: that would change Ward's licensing story and must be
  decided deliberately.

## The clean channel

Ward's **design docs and spec are the clean channel**: original expression that
describes behavior and interfaces, from which any implementation is written.
Copyleft tools are used as **dependencies under their own license** (a linked or
invoked model checker), never as copied source. The MIT license rests on Ward
source being original expression plus properly-licensed dependencies.

## Originality recheck

Before a design/spec section that consulted a **copyleft** reference is locked,
confirm it is original expression:

```sh
scripts/originality-scan.py <section> local/refs/<ref> --fail 0.04
```

k-gram shingle similarity plus independent review. Long matched **runs** are the
signal; short matches over shared technical vocabulary are expected. A confirmed
overlap goes back to the author to rewrite in Ward's own words.
