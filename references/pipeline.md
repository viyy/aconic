# Execution pipeline — optional grill-me → /aconic → fresh agents → gate

## Two equally supported entry paths

**Direct:** `task description or files → /aconic → phase prompts`

**With discovery:** `task → grill-me → requirements file → /aconic → phase prompts`

`grill-me` is a separate optional skill. `/aconic` must never require it, emulate its full interview, or automatically call it. The user's default input can be nothing more than a path or task description.

## After the plan is built

1. **Inspect and freeze** approved constraints and shared contracts; retain a plan version.
2. **Execute FNN:** start a fresh coding agent and provide exactly the generated phase prompt. It reads specified local files and prerequisite handoffs, executes within declared scope, runs accessible checks, and writes `handoffs/FNN.md`.
3. **Review:** a separate review session (or human) uses `/aconic review FNN` to evaluate the actual diff, check results and contract. Mark `PASS | FAIL | BLOCKED` in `reviews/FNN.md`.
4. **Continue** only after prerequisite phases pass gates. Independent phases may run concurrently only with isolated worktrees/branches and compatible integration practices.
5. **Replan** on newly discovered hard constraints or shared contract changes; revise affected downstream phase prompts and plan version, without rewriting completed historical artifacts.

## What the new agent must receive

- **Global invariant set:** task goal, hard constraints, architecture decisions and plan version via stable files.
- **Phase-specific instructions:** goal, dependencies, read/write scope, fixed contracts, tests and acceptance checks.
- **Verified upstream handoffs:** only prerequisites named in its prompt, not the entire prior agent transcript.

Handoff (`handoffs/FNN.md`) records: plan version and phase ID; commit or diff reference where available; changed paths; behavior delivered; test commands/results with failures or skips; interface decisions and evidence; deviations and risks; status `READY_FOR_REVIEW | BLOCKED | FAILED` (never self-certified PASS).

A gate verifies against actual code and outputs `PASS | FAIL | BLOCKED`. Manual execution is the default; the planning skill does not launch or implement phases.

## Keep planning proportional

Small: one or two phases and a concise plan. Medium: contract boundaries, phased work and gates. Large: detailed constraint graph, alternative partitions, decision log, parallel waves and integration plan. Avoid splitting for the sake of many agents.
