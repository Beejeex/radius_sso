---
name: spec-synthesis
description: Turn an already-clarified conversation into a durable specification artifact. Map product synthesis into the existing Ventus flow: analyst → spec-writer → analyst validation, with ADR/schema/security flows as required.
---

# Spec Synthesis

## Trigger
Use when:
- A feature conversation has reached sufficient clarity to produce a durable spec.
- Product behavior, acceptance criteria, auth path, UI/API reachability, non-goals, and definition of done need to be captured.
- The analyst has approved the content and is ready for `spec-writer` to record it.

## Consumers
`analyst` (synthesize and validate), `pm` (keep roadmap/backlog aligned), `spec-writer` (write the accepted spec)

## Required Workflow
1. **Synthesize from existing discussion** — do not restart a full interview unless critical information is missing.
2. Use **Ventus source documents and accepted terminology**.
3. **Explicitly capture out-of-scope behavior**.
4. **Identify needed** ADRs, schema docs, security records, and test evidence.
5. Route through `analyst → spec-writer → analyst validation` flow.
6. Link the accepted spec from the active milestone context.

## Completion Criteria
- Spec is accepted by `analyst` with passing `Document Validation Record`.
- Out-of-scope behavior is explicitly documented.
- Required downstream artifacts (ADR, schema, security) are identified.
- Spec is linked from milestone context.

## Non-Goals
- Do **not** create generic PRDs without Ventus SPEC/ADR/schema integration.
- Do **not** bypass the analyst validation gate.
- Do **not** proceed to implementation before the pre-implementation documentation gate is satisfied.

## Conflicts
None. Must respect the pre-implementation documentation gate.

## Dependencies
- `docs/specs/000-template.md`
- `.github/instructions/agents.instructions.md` (document writer gate)
- `docs/project/AGENT_GUIDANCE.md` (pre-implementation documentation gate)
