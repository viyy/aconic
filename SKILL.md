---
name: aconic
description: Analyze a software task from its description or referenced files, inspect relevant repository architecture, extract constraints, minimize coupling in a phase decomposition, and produce standalone prompts for fresh implementation agents. Optional grill-me input; planning only.
---

# /aconic — task in, executable phase plan out

**Contract:** The user supplies ONLY a task description, a task file/path, or a set of source files. You supply the architecture inspection, constraint analysis, decomposition, dependency ordering, and fresh-agent prompts **without requiring the user to restate this workflow**.

This is an ACONIC-inspired engineering workflow, not an exact implementation of a published formal optimization algorithm. Never claim an optimal graph partition or measured treewidth without actually computing and validating it.

## How to invoke

```text
/aconic Implement idempotent bulk document status updates
/aconic docs/tasks/DEV-1234.md
/aconic @docs/spec.md @docs/contracts/events.yaml
/aconic review F02
/aconic replan F03 Newly discovered legacy consumer requires v1 schema
```

`@file` notation is client-dependent: ordinary paths work as input when the agent has filesystem access. `review` and `replan` are **text conventions interpreted by this skill**, not guaranteed native subcommands. If no review/replan mode is requested, **plan by default**. Do not require `/aconic plan`.

## Hard operating rules

1. **Do not implement the task.** Only create/update planning artifacts (and review/replan reports when explicitly requested). Do not edit production code, tests, manifests, CI, or schemas during planning. Treat repository exploration as read-only.
2. **No prerequisite grill-me.** Accept its output if explicitly supplied; otherwise work directly from the task. Do not initiate an interview by default. Ask one focused question only when a genuine blocker makes safe planning impossible; otherwise proceed with explicit assumptions and unresolved risks.
3. **Inspect the repository automatically** if repository tools and access are available. Do not demand instructions like “consider the architecture” or “prepare phase prompts.” If no repository is accessible, use supplied evidence, mark architectural findings unverified, and never invent paths, symbols or executable commands.
4. **Use targeted exploration.** Inspect repo instructions (`AGENTS.md`, applicable project instructions), top-level structure, relevant solution/project files, entry points, public contracts, shared libraries, existing analogues, test layout, deployment/migration mechanisms. Follow call paths and dependencies only where relevant. Avoid bulk reading huge monorepos or flooding the context window. Record `Observed (path/symbol)`, `Inferred`, or `Unknown`.
5. **Preserve constraints before optimizing coupling.** Security, domain invariants, correctness, backwards compatibility and operational safety outrank independence and parallelism. Never split inseparable changes to manufacture parallel phases.
6. **Output phase prompts runnable in completely fresh agents.** No dependence on chat history, unstated decisions or tacit knowledge. Every prompt must declare allowed/forbidden scope, prerequisites, fixed contracts, expected outputs, verification and handoff path. Link stable local source files instead of pasting whole codebases.
7. **Default to manual orchestration.** Do not spawn other agents or execute phases. Mark parallel phases as eligible only with isolation (e.g., separate worktrees), ready prerequisites and safe shared contracts.
8. **No proprietary data egress.** Prefer local tools; don't upload repository contents to external services. Don't make unsupported claims about scans or passing tests.

## Mode routing

- **Default:** `/aconic <task and/or file paths>` → full planning pipeline below.
- **Review:** `/aconic review FNN [plan-path]` → inspect phase contract, actual diff, tests and handoff; emit `reviews/FNN.md` with evidence-based `PASS | FAIL | BLOCKED`. A self-reported handoff is not proof.
- **Replan:** `/aconic replan FNN <reason> [plan-path]` → re-evaluate impacted constraints, shared contracts and downstream DAG; increment plan version, flag stale prompts, preserve accepted history and completed phases.

If `review` or `replan` lacks a plan path, locate the corresponding plan in the workspace, and ask only when several matching plans are genuinely ambiguous.

## Default planning pipeline (mandatory)

### 1. Resolve inputs and scope

Interpret plain-language instructions, user-named files, attachments and relevant repository docs as task sources. Read each accessible source, record provenance, reconcile contradictions by precedence: explicit latest user instruction > approved task spec > repo evidence > assumptions. Keep acceptance criteria and non-goals visible. If a file is missing, report it and continue where feasible.

### 2. Investigate architecture (automatic)

Discover the **smallest relevant slice** of the codebase: entry points, service boundaries, API/event contracts, persistence, shared abstractions, integration patterns, tests, rollout configuration, and any legacy/monorepo coupling. Distinguish directly inspected evidence from speculation; include file paths/symbols and known limits. Detect existing implementation patterns that should be reused rather than rebuilt.

### 3. Extract constrained decisions

Create a conceptual CSP `C=(X,D,R)` where `X` are meaningful decisions, `D` their options and `R` hard/soft constraints. Include source, scope, confidence, owner/check, and test or acceptance proof for each hard constraint. Identify cross-service separators (shared schema, public contract, domain invariants, migration order). Do not generate fake quantitative precision.

### 4. Decompose by coupling

Model an undirected constraint graph for interdependent decisions, then a separate directed DAG for task execution. Group tightly coupled choices within one phase where possible; favor stable, narrow interfaces between phases. For nontrivial work, compare at least two plausible partitions and briefly justify the selected one by coupling, verification complexity, risk and size. No fixed number of phases. Merge cycles or introduce a contract decision phase when justified. Use actual measured heuristics only if an approved local graph algorithm was really executed.

### 5. Create phase contracts and standalone prompts

Every phase must specify: goal, exact scope and exclusions, dependencies, prerequisite verified artifacts, key repo paths/symbols (only those observed), frozen interfaces and invariants, implementation outline as guidance not prescription, acceptance criteria, test/validation commands only when known, failure/rollback risks, expected files, output handoff path and conditions to stop for replan. Generate prompts following `templates/phase-prompt.md`; ensure each prompt starts with an explicit instruction to operate **as a new agent without prior conversation context**.

### 6. Plan verification and handoffs

Define gate checks for each phase: evidence from actual changed files, relevant automated tests and compatibility checks. Make the handoff a portable artifact; do not use raw chat transcripts for state. Plan integration/rollout gates and identify potential parallel execution waves, with repository isolation requirements. See `references/pipeline.md`.

### 7. Persist deliverables and stop

Save under `docs/plans/<task-slug>/` by default (or user's chosen location):

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

Create `handoffs/` and `reviews/` as phases run, or include `templates/handoff.md` as a future artifact; do not invent actual results. If workspace write access is unavailable, present complete copyable plan and prompts instead of claiming files were created. Finish with: plan location, assumptions/blockers, dependency order, phases ready to start and a copyable invocation for the first agent. **Never begin implementation.**

## Quality gate before finishing

- Every acceptance criterion is owned by at least one phase and verified by a gate.
- Every hard constraint has an owner and a proposed check.
- All phase prerequisites and DAG edges are explicit and justified.
- All fresh-agent prompts can be understood without the planning chat.
- Shared contracts have an owner, version/decision source and change control.
- Unknown architecture facts remain marked unknown, not fabricated.
- Planning artifacts, and only planning artifacts, were changed.

Read `references/method.md`, `references/pipeline.md`, `templates/plan.md`, `templates/phase-prompt.md`, and `templates/handoff.md` when creating an actual plan.
