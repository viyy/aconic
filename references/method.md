# Constraint analysis and partition heuristics

## Modeling

`X = {x1, ..., xn}` decisions; `D(x)` domain per decision; `R = {r1, ..., rm}` constraints.

Sample:
- `x1`: API contract strategy = {additive, versioned}
- `x2`: event contract strategy = {additive, new version}
- `x3`: schema migration strategy = {expand/contract, replace}
- `r1`: old clients must continue working => restricts x1, x2
- `r2`: zero-downtime schema rollout => restricts x1, x3

Create constraint edges between all variables occurring together in a restriction. Security boundaries and business invariants should remain explicitly listed and traceable.

## Candidate scoring (heuristic, not from the paper)

Compare partitions using a qualitative weighted tuple rather than pretending a formal global optimum:
1. hard-constraint violations (must be zero);
2. maximum coupled decision cluster size (smaller generally easier);
3. number/severity of cross-cluster constraints (lower better);
4. interface ambiguity and verification cost;
5. expected implementation/review contention;
6. number of tasks (avoid unnecessary fragmentation).

Do not sum arbitrary weighted values without disclosing assumptions. Prefer a transparent side-by-side comparison.

## Optional tree decomposition

A valid tree decomposition contains bags of decisions; every variable appears in some bag, every edge's endpoints share at least one bag, and bags containing one variable form a connected subtree. Width = maximum bag size - 1. The **treewidth** is the *minimum* width across all valid decompositions. A decomposition found via a heuristic gives an upper bound, not proof of optimality.

For a large graph, approved local tools may apply greedy min-fill elimination to obtain candidate bags; check the three validity requirements. Do not produce fake numerical treewidth estimates or derive them from a hand-drawn DAG.

## Separation and execution

A separator is a shared decision/contract needed to coordinate clusters. Make separators explicit and small. It is normal for neighboring bags to share variables; trying to eliminate all overlap can make the overall plan incorrect.

Task DAG edges represent prerequisite outputs, not mere relatedness. For example, `contract -> service implementation` and `contract -> consumer implementation`; the two implementations can run concurrently *only after* the contract is fixed. Integration verification depends on both.

## For .NET microservices

Review when relevant: DI registrations, DTO evolution, REST/gRPC contracts, serializers, event schemas, idempotency, retry/backoff, outbox, transactional boundaries, EF Core migrations, shared libraries, schema ownership, K8s configuration, rollout sequencing, compatibility windows, telemetry, contract tests. Do not assume any of these are used unless evidence exists.
