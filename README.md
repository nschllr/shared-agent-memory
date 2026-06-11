# shared-agent-memory

Shared agent memory template for projects where multiple coding agents need to
coordinate across sessions without relying on private chat history.

## What this provides

Add these files to the root of another project:

- `AGENTS.md`
- `CLAUDE.md`
- `.agent/`

`AGENTS.md` and `CLAUDE.md` are intentionally tiny entry points. They tell
agents to read `.agent/INSTRUCTIONS.md`, which defines the shared protocol for
reading and maintaining project memory.

## How it works

Agents start by reading the shared memory files:

- `.agent/shared-state.md` for durable facts about the project.
- `.agent/environment.md` for reusable command and environment knowledge.
- `.agent/decisions.md` for decisions that should guide future work.
- `.agent/task-board.md` for active, later, and recently completed work.

Agents then update only the file that matches the kind of information they
learned. This keeps memory useful instead of turning it into a command log.

When an agent stops after doing concrete work, it appends a concise handoff to
`.agent/handoff.md` so the next agent can continue from the latest useful state.

## File guide

| Path | Purpose |
| --- | --- |
| `AGENTS.md` | Entry point for Codex-style agents. |
| `CLAUDE.md` | Entry point for Claude-style agents. |
| `.agent/INSTRUCTIONS.md` | The protocol every agent should follow. |
| `.agent/shared-state.md` | Durable current facts that affect future work. |
| `.agent/environment.md` | Stable command, tool, and environment notes. |
| `.agent/decisions.md` | Design and process decisions that remain binding. |
| `.agent/task-board.md` | Shared task queue and file claims for substantial work. |
| `.agent/handoff.md` | Actionable session handoffs after concrete work. |
| `.agent/commands/clean-up.md` | Optional explicit cleanup procedure. |

## Installing in a project

Copy the entry points and `.agent` directory into the target repository root:

```bash
cp -R AGENTS.md CLAUDE.md .agent /path/to/project/
```

Then commit them with that project. Agents that understand either entry point
will load the shared protocol before starting work.

## Usage expectations

- Keep entries factual, current, and compact.
- Do not duplicate routine command output.
- Update `.agent/task-board.md` only when work is substantial, long-running, or
  likely to conflict with another agent.
- Use `.agent/handoff.md` for meaningful continuation state, not no-op session
  summaries.
- Treat the files as shared project state, not private notes.

## Cleanup

The cleanup procedure is intentionally opt-in. To run it, explicitly ask an
agent to:

```text
run agent cleanup
```

That command is defined in `.agent/commands/clean-up.md` and includes backup and
validation requirements.
