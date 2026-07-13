---
name: handoff
description: Preserve enough context for a fresh bounded agent session to continue without rediscovering prior reasoning. Use when sessions approach context limits, work transfers between agents, or decisions/evidence must survive outside the chat.
---

# Handoff

## Trigger
Use when:
- A session is approaching context limits.
- Work is being transferred between agents.
- A prototype, investigation, review, or bug diagnosis needs to resume later.
- The agent produces decisions or evidence that must survive outside the chat.

## Consumers
`operator` (orchestrate handoffs), all specialist agents (include compact continuation context)

## Required Workflow
1. Use `.github/prompts/handover.prompt.md` as the canonical format for agent-to-agent dispatch.
2. Do not add, rename, or replace handover fields unless the handover prompt itself changes.
3. Put quality details inside the existing fields: goal in `TASK`; next agent in `TO`; status in `STATUS`; decisions, evidence, changed files, and path references in `CONTEXT`; open questions in `OPEN_QUESTIONS`; blockers in `BLOCKED_BY`.
4. Reference durable artifacts by path instead of duplicating their content.
5. Redact secrets and personal data.
6. Keep handoff text concise enough for the next agent to read fully.

## Completion Criteria
- The next agent can resume work without re-asking previously answered questions.
- All durable evidence is referenced by path.
- No secrets or personal data in the handoff.

## Non-Goals
- Do **not** create project-state updates solely because a handoff happened.
- Do **not** copy full specs, ADRs, diffs, or logs into a handoff when paths and summaries are enough.

## Conflicts
None. The handover prompt controls dispatch format; this skill controls when to use it and how complete the handoff context must be.

## Dependencies
- `.github/prompts/handover.prompt.md` (handover template)
- `.github/instructions/agents.instructions.md` (handoff markers)
