---
name: intake-triage
description: State machine for raw bugs, requests, and incoming work before implementation. States: needs-triage → needs-info → ready-for-agent → ready-for-human → wontfix. Preserve reporter intent and verify bug claims when possible.
---

# Intake Triage

## Trigger
Use when:
- Raw bugs, feature requests, or incoming work arrive without classification.
- Work needs to be evaluated before routing to an agent.
- A triage state machine helps organize backlog shaping.

## State Machine
```
needs-triage  →  needs-info  →  ready-for-agent
                     ↓                ↓
                  wontfix       ready-for-human
```
- **needs-triage**: maintainer or agent must evaluate.
- **needs-info**: waiting for missing reporter/user detail.
- **ready-for-agent**: fully specified and can be picked up by an agent.
- **ready-for-human**: requires human judgment or external access.
- **wontfix**: will not be actioned.

## Consumers
`operator` (classify incoming work before routing), `pm` (reflect accepted backlog changes), `review` (evaluate bug reports and reproduce claims)

## Required Behavior
1. **Preserve reporter/user intent.**
2. **Verify bug claims** when possible.
3. **Ask specific missing-info questions** (one at a time, per grilling practice).
4. Move to **`ready-for-agent`** only when acceptance, scope, owner, and evidence needs are clear.

## Completion Criteria
- Every incoming item has a triage state.
- `ready-for-agent` items have clear acceptance, scope, owner, and evidence requirements.
- `needs-info` items have specific, actionable questions for the reporter.

## Non-Goals
- Do **not** assume GitHub Issues automation exists.
- Do **not** bypass Ventus SPEC/ADR/schema gates for triaged items.
- Do **not** triage items that are already in active implementation chains.

## Conflicts
None with existing Ventus gates.

## Dependencies
- `docs/project/BACKLOG.md`
- `.github/instructions/agents.instructions.md` (handoff protocol)
