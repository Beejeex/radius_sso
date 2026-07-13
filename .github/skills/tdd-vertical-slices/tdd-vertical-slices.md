---
name: tdd-vertical-slices
description: Keep implementation grounded in tests that describe observable behavior through stable seams. One vertical behavior slice at a time: red test → minimal implementation → green → refactor. Test public interfaces, not private details.
---

# TDD Vertical Slices

## Trigger
Use when:
- Implementing new behavior.
- Fixing a bug after reproduction.
- Refactoring behavior where a public seam can be protected.
- Building frontend/UI slices after backend-ready-for-UI evidence exists.

## Consumers
`test-gen` (define red baseline), `appdev` and domain agents (implement narrowly to satisfy tests), `refactor` (preserve green while improving shape), `validate` (verify claimed test evidence)

## Required Workflow
1. Prefer **one vertical behavior slice** at a time.
2. Write or accept **one red/expected behavior** before production code.
3. Test **public interfaces and observable outcomes**, not private implementation details.
4. **Refactor only after green.**
5. If implementation reveals a missing scenario, **hand back to `test-gen`**.
6. For UI work, keep **one route/page/component/workflow slice green** before starting the next.

## Completion Criteria
- Each slice has a red baseline → implementation → green → clean refactor sequence.
- Tests exercise public seams only.
- No speculative production behavior without current-slice coverage.

## Non-Goals
- Do **not** write all tests first and then all implementation as a horizontal batch.
- Do **not** mock internal collaborators just to reach private implementation.
- Do **not** add speculative production behavior not covered by the current slice.

## Conflicts
None with existing Ventus gates. Complements the pre-implementation documentation gate and backend-ready-for-UI gate.

## Dependencies
- `.github/instructions/agents.instructions.md` (handoff protocol)
- `docs/project/AGENT_GUIDANCE.md` (validation gates)
