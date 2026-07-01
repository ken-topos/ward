# `local/` — your private drop zone

Anything in this directory is **gitignored** (except this README), so it never
reaches the public repo. Use it for research notes, AI-conversation transcripts,
scratch files, throwaway experiments, and the reference shelf — anything kept
locally alongside the repo but out of version control.

The ignore rule lives in `../.gitignore`:

```
/local/*
!/local/README.md
```

So a stray `git add -A` cannot sweep these into a commit. If you ever *do* want
to track something from here, move it out of `local/` first.

Reference implementations Ward reads live under [`refs/`](refs/) — clone them
with `refs/clone.sh` (see `refs/README.md`). They are **read to understand,
integrated under their license, never copied** (`../CLEAN-ROOM.md`).
