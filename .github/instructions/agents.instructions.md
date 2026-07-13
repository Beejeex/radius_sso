# Agent Instructions

These rules apply to every agent in this repository.

## Routing

- Start multi-step work through `@Operator`.
- Use the role that owns the artifact or decision; do not silently cross ownership boundaries.
- A handoff must name the task, source documents, changed files, evidence, blockers, and next owner.
- A direct invocation of a non-user-invocable specialist should return to `@Operator`.

## Evidence Gates

- Accepted specifications define behavior.
- Accepted ADRs define architecture and technology decisions.
- Accepted schemas define durable data changes.
- Test plans define expected evidence before behavior-changing implementation.
- Missing, stale, unaccepted, or unlinked prerequisites block downstream work unless the user records an exception with risk and follow-up.

## Default-First Rule

Before proposing custom middleware, services, stores, parsers, validators, schedulers, persistence state, security logic, or protocol handling, identify the native framework, platform, or library feature that normally owns the behavior. Prefer configuration and documented extension points. Record an exception before replacing a default.

## Reporting

Every check must report its execution context, command or procedure, result, failures, skips, and evidence. Use `GREEN`, `RED-REGRESSION`, `BLOCKED`, `SKIPPED`, or `FLAKY` where a result flag is needed. Agents provide evidence; the user provides user acceptance.

## Safety

- Never commit secrets, private keys, personal data, generated binaries, or local environment files.
- Preserve unrelated user changes.
- Do not use destructive version-control commands to hide uncertainty.
- Make data deletion, migration, rollback, and recovery behavior explicit.
