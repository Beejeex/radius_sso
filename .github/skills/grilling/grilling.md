---
name: grilling
description: Force shared understanding before committing to a plan, design, spec, test plan, or implementation. Ask one clarifying question at a time with a recommended default until scope, non-goals, affected surfaces, blockers, validation evidence, and next handoff are all clear.
---

# Grilling

## Trigger
Use when:
- The request has ambiguous scope.
- The user asks to pressure-test, grill, challenge, clarify, or validate a plan.
- The agent sees missing product behavior, ownership, acceptance criteria, auth-path classification, schema impact, API contract, UI reachability, or validation evidence.
- A future step would require guessing.

## Consumers
`operator`, `analyst`, `design`, `schema`, `security`, `test-gen`, `review`, `refactor`

## Required Workflow
1. Ask **one question at a time**.
2. Include a **recommended answer or default path** with each question.
3. Before asking, inspect local docs/code when the answer is discoverable.
4. Stop when the agent can name: goal, non-goals, affected surfaces, blockers, validation evidence, and next handoff.
5. If an answer reveals a hard gate, emit the correct `[HANDOFF →]` or `[ESCALATE →]` block and stop.

## Completion Criteria
- Goal, non-goals, affected surfaces, blockers, validation evidence, and next handoff are all explicitly named.
- No remaining ambiguity that would force a downstream agent to guess.

## Non-Goals
- Do **not** ask several questions at once.
- Do **not** use grilling to bypass accepted SPEC, ADR, schema, or owner validation requirements.
- Do **not** keep interviewing after the next required handoff is clear.

## Conflicts
None with existing Ventus gates when used correctly.

## Dependencies
- `.github/instructions/agents.instructions.md` (handoff/return block format)
- `docs/project/AGENT_GUIDANCE.md` (validation gates)
