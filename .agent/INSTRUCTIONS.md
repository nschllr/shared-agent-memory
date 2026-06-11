# Shared multi-agent protocol

Before starting:
1. Read `.agent/shared-state.md`.
2. Read `.agent/environment.md`.
3. Read `.agent/decisions.md`.
4. Read `.agent/task-board.md`.
5. Run `git status` if you are in a git repository.

When discovering durable information:
- Update `.agent/shared-state.md` only for durable current facts that affect
  future work. Do not record transient observations, repeated facts, routine
  command output, or facts already captured there.

When commands work or fail:
- Update `.agent/environment.md` only for reusable command knowledge: commands
  likely to be run again, stable environment constraints, non-obvious failures,
  or latest results that change future execution. Do not log routine reads,
  searches, formatting, `git status`, successful syntax checks, or one-off
  exploratory failures unless they explain a current constraint.

When making a design decision:
- Add it to `.agent/decisions.md` only if it changes how future work should be
  done. Merge with or replace an existing decision when possible; do not append
  duplicate policy restatements.

When starting work:
- Claim the task/files in `.agent/task-board.md` only for substantial work,
  long-running work, or changes that could conflict with another agent. Do not
  claim read-only audits, status checks, small instruction edits, or quick
  single-agent fixes.

When stopping:
- Append a handoff to `.agent/handoff.md` only if concrete work happened or
  useful state must survive for the next agent: files changed, commands ran with
  meaningful results, work is partially complete, or a blocker/remediation path
  needs to be preserved.
- Do not append no-op handoffs for context refreshes, instruction reads, status
  replies, skill toggles, or sessions where no concrete task started.
- Keep handoffs actionable: summarize the trigger, files changed, important
  command results, blockers, and remaining work. Avoid chat metadata and
  repeated lists of files merely read.

Do not rely on private chat history.
