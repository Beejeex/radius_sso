# Agent Instructions

This file defines the shared agent communication protocol. Project policy and implementation gates are canonical in `docs/project/AGENT_GUIDANCE.md`; routing is canonical in `.github/AGENTS_FLOW.md`.

## Routing

- Start multi-step work through `@Operator`.
- Use the role that owns the decision or artifact; do not silently cross ownership boundaries.
- A direct invocation of a non-user-invocable specialist returns to `@Operator`.

## Handoffs

- Use `.github/prompts/handover.prompt.md`.
- Name the requested outcome, source documents, changed files, evidence, assumptions, blockers, and next owner.
- The receiving agent confirms ownership and prerequisites, then returns the requested evidence or a concrete blocker.

## Reporting

Every check reports its execution context, command or procedure, result, failures, skips, and evidence. Use `GREEN`, `RED-REGRESSION`, `BLOCKED`, `SKIPPED`, or `FLAKY` when a result flag is needed. Agents provide evidence; the user provides user acceptance.
