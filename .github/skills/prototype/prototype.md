---
name: prototype
description: Answer uncertain design questions quickly with throwaway artifacts. State the question, build the minimal prototype under .tmp/, capture the lesson, then delete or explicitly absorb. Never let prototype code become production by accident.
---

# Prototype

## Trigger
Use when:
- A state model, business rule, or UI direction is hard to judge on paper.
- A prototype can answer a specific question faster than a full design cycle.
- Design uncertainty is experiential and cannot be resolved through discussion alone.

## Consumers
`design` (request prototype when uncertainty is experiential), `appdev` and domain agents (build only approved throwaway prototypes), `test-gen` (usually does not test prototypes)

## Required Workflow
1. **State the question** the prototype answers.
2. **Mark the prototype clearly as throwaway.**
3. Put scratch artifacts under **`.tmp/`** unless the user explicitly approves another location.
4. Use **no durable production data** by default.
5. **Capture the decision learned** from the prototype.
6. **Delete or absorb** the prototype when done.

## Completion Criteria
- The specific design question is answered.
- The lesson is captured in a durable form (design note, ADR input, spec clarification).
- Prototype artifacts are cleaned up or explicitly absorbed with user approval.

## Non-Goals
- Do **not** let prototype code become production by accident.
- Do **not** add tests, generalized abstractions, or persistence unless the question requires them.
- Do **not** use prototypes to bypass required SPEC/ADR/schema acceptance for production work.

## Conflicts
None. Prototypes are explicitly non-production.

## Dependencies
None beyond standard `.tmp/` directory convention.
