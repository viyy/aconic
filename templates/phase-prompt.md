# Phase FNN — <name> | Plan <id> version <version>

You are a **fresh execution agent**. You do not know any previous chat or agent reasoning. Read the following persisted files before making changes:
- `<plan-dir>/plan.md`
- `<plan-dir>/constraints.md`
- `<plan-dir>/decisions.md`
- `<plan-dir>/phases/index.md`
- `<exact upstream handoff files, if any>`

## Mission
<One precise independently verifiable deliverable.>

## Preconditions / dependencies
- Requires passed reviews for: <phase IDs, or none>
- Fixed separator contracts: <IDs and authoritative paths>
- Plan version: <version>. If incompatible with on-disk plan, STOP.

## Allowed changes
<Explicit files, projects or modules supported by repository evidence.>

## Forbidden changes
<Explicit out-of-scope modules, contract rewrites, migrations, deployments and risks.>

## Constraints / invariants
<Relevant hard constraint IDs plus short statements. Repeat critical constraints even if in global docs.>

## Implementation requirements
<Observable behavior and choices already decided. Do not invent unspecified contracts.>

## Validation
<Concrete, repo-confirmed commands and expected outcomes. If unknown, specify a discovery step rather than fabricate commands.>

## Completion / handoff
- Implement only this phase. Do not begin dependent phases.
- Write `<plan-dir>/handoffs/FNN.md` using `templates/handoff.md` (or copy its structure).
- Report actual changed files, commands/results, unresolved blockers, and diff/commit ID.
- Mark handoff READY_FOR_REVIEW, BLOCKED or FAILED; **never approve your own gate**.
- If a frozen assumption, constraint or contract must change, STOP and request `/aconic replan FNN <reason>`.
