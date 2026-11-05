# Sync and version control — held, not decided

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task.

## Status

Sync and version control are **held, not decided** in this boilerplate. Do not implement an iCloud-specific solution, a git-specific solution, or any other single-provider solution on your own initiative.

The direction, recorded in the audit, is: the solution must be generic to any sync adapter (iCloud, Dropbox, Google Drive, OneDrive, Syncthing, or another), and must prompt the agent and the human to make their own decisions on install, or down the line if the human is not sure — the same setup-choice pattern as sharding, review cadence, granularity, and expiry (present options plainly, human decides or defers, record as a decision, revisit on material change).

## What is already decided, elsewhere

- The core system works without version control, and benefits from it; it does not require it.
- History is semantic and lives in the vault itself (supersession chain, decision notes, updated dates), not in a git log.
- Backup method is a setup choice, not a required git step.
- No git-specific constructs in notes.
- Git, when present, is a snapshot backup outside the sync folder.
- Index conflicts are never merged — discard and regenerate from the `.md` files. See `spec/query.md`, Index is committed, with a freshness check.

For the full held question, see `spec/open-questions.md`.
