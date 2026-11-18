# /aconic — describe a task, receive an architecture-aware phase plan

A standalone ACONIC-inspired planning skill. **No custom prompt boilerplate, no mandatory `grill-me`.** By default it reads the user-provided task description or file paths, inspects the relevant repository architecture and constraints, and writes a phase plan with autonomous prompts for separate implementation agents. It never implements code during planning.

## Install

- **Qwen Code:** place this folder at `.qwen/skills/aconic/` (per project) or `~/.qwen/skills/aconic/` (user scope). Check the skill listing and slash invocation in your installed version.
- **Cline:** place it in the skill directory supported by your installed version, then explicitly invoke `aconic` using your client's skill interface. Slash support and argument parsing may differ.

## Usage

```text
/aconic Add support for a new document type
/aconic docs/tasks/DEV-1234.md
/aconic @docs/tasks/DEV-1234.md @docs/api-contract.yaml
/aconic review F02
/aconic replan F03 Legacy consumer requires older event schema
```

No need to say “inspect the architecture,” “identify constraints,” “split into phases,” “write prompts,” “do not implement,” or “use a new agent for each phase.” **Those are built-in instructions.** Client-specific `@file` references may instead be supplied as plain file paths.

If desired, run a separate `grill-me` skill *before* `/aconic` and supply its requirements artifact. Otherwise run `/aconic` directly.

## Plan outputs

`docs/plans/<task-slug>/` by default:

```text
plan.md
constraints.md
decisions.md
status.md
phases/index.md
phases/F01-<slug>.md
phases/F02-<slug>.md
...
```

Each phase file is a standalone **prompt for a fresh coding agent**. Execute each phase manually in a new session, with its prerequisite handoffs. `/aconic review FNN` evaluates implementation evidence, and `/aconic replan FNN <reason>` updates impacted future phases.

## Files in this package

- `SKILL.md` — all default behavior and mode routing
- `references/method.md` — constraint model and decomposition guidance
- `references/pipeline.md` — optional discovery, execution and verification
- `templates/plan.md`, `templates/phase-prompt.md`, `templates/handoff.md`
- `examples/cross-service-operation.md`

This is an engineering interpretation inspired by constraint-driven decomposition, not a claim of proven globally optimal partitioning. No external model APIs or automated agent spawning are required.
