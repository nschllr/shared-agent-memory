# Command: run agent cleanup

Trigger only when user explicitly says `run agent cleanup`.

Do not run for casual phrases like `cleanup`, `clean up`, `clean-up`, or
general requests to tidy code/docs. If the user asks about cleanup without the
literal trigger, discuss scope or ask whether to run `run agent cleanup`.

Goal: reduce stale/noisy coordination and project state while preserving
recoverability and current technical truth.

## Hard Rules

- Do not modify `.agent/INSTRUCTIONS.md`.
- Do not delete source code, exploit artifacts, build outputs, or experiment
  data unless user explicitly names the target.
- Do not rewrite unrelated user changes.
- Prefer updating stale docs/state over deleting uncertain information.
- Before edits, create a backup snapshot.
- Keep at least the three newest cleanup backup snapshots.
- Report exact backup path, changed files, removed/updated stale items, and
  validation checks.

## Backup Procedure

1. Create timestamped snapshot with one timestamp variable:

```bash
ts=$(date +%Y%m%d-%H%M%S)
mkdir -p ".agent/backup/$ts"
cp .agent/*.md ".agent/backup/$ts/"
```

2. If `.agent/commands/` exists, copy command specs too:

```bash
test ! -d .agent/commands || cp -R .agent/commands ".agent/backup/$ts/"
```

3. Never overwrite existing backup files.
4. Retain at least three newest timestamped dirs under `.agent/backup/`.
5. Keep `.agent/backup/` containing timestamped snapshot directories only; do
   not place loose files directly under `.agent/backup/`.
6. If legacy loose files exist, move them into a timestamped snapshot directory
   before creating or pruning backups.
7. If pruning is needed, remove only timestamped cleanup snapshots older than
   the newest three, and only after a fresh snapshot exists.

Use same timestamp variable for every backup command:

```bash
ts=$(date +%Y%m%d-%H%M%S)
mkdir -p ".agent/backup/$ts"
cp .agent/*.md ".agent/backup/$ts/"
test ! -d .agent/commands || cp -R .agent/commands ".agent/backup/$ts/"
```

## Cleanup Scope

### Agent Coordination Files

- `.agent/shared-state.md`: keep durable current facts, latest result
  summaries, artifact paths, and current state needed by future agents. Remove
  duplicated historical narration already covered in handoff or committed docs.
- `.agent/environment.md`: keep reusable commands, validation commands,
  environment constraints, stable tool/runtime facts, and non-obvious command
  failure modes. Do not keep a historical/latest-results ledger here; move
  result summaries and artifact paths to `.agent/shared-state.md`.
- `.agent/decisions.md`: keep durable policy/design decisions that should guide
  future work. Do not duplicate routine agent protocol, task-board formatting
  rules, cleanup mechanics, command recipes, or result summaries owned by
  `.agent/INSTRUCTIONS.md`, `.agent/commands/clean-up.md`,
  `.agent/task-board.md`, `.agent/environment.md`, or
  `.agent/shared-state.md`.
- `.agent/task-board.md`: move finished active items to completed; remove tasks
  that are obsolete because later state superseded them; keep actionable next
  tasks. Enforce the task-board rules header, `Active`/`Later`/`Done Recently`
  sections, `Next`/`Plan`/`Claim` fields, and at most 5 `Done Recently` items.
- `.agent/handoff.md`: reduce aggressively. Keep latest handoff, current
  blockers, current artifacts, and pointers to full backups. Historical detail
  can be summarized when already present in shared state or docs.
- `docs/plans/*.md`: check for orphan plan files and broken task-board `Plan:`
  links, excluding `docs/plans/TEMPLATE.md`. A plan file should have exactly
  one task-board entry pointing to it; a task-board `Plan:` path should exist.
  Completed plans are kept only if they remain useful design docs; otherwise
  fold durable content into normal docs or remove them. Do not create a
  completed-plan archive.

During cleanup, explicitly check for cross-file ownership drift:

- Result summaries, run directories, artifact paths, and counts should not live
  in `.agent/environment.md` unless they are expected command output or a stable
  tool/runtime fact.
- Cleanup backup rules and task-board field rules should not live in
  `.agent/decisions.md`; their source is this command spec plus
  `.agent/task-board.md`.
- Durable policy should not be repeated in `.agent/shared-state.md` when the
  same point already exists in `.agent/decisions.md`.


## Compression Rules

- Preserve code blocks, inline code, commands, paths, URLs, dates, counts,
  task IDs, image tags, and decisions.
- Prefer compact factual bullets.
- Remove duplicated history when another file already records it.
- If unsure whether information is still needed, keep short version plus pointer
  to backup/doc.

## Validation

After cleanup:

```bash
git status --short
find .agent/backup -maxdepth 2 -type d -print
test -f .agent/INSTRUCTIONS.md
```

When code/docs changed, run the smallest relevant validation:
- Python touched: `python3 -m py_compile <files>`
- Shell touched: `bash -n <files>`
- Tests touched or behavior changed: targeted pytest/unit command if available.

Final response must include:
- Backup snapshot path.
- Files changed.
- Files intentionally not changed.
- Validation run or why not run.
