---
name: writing-agent-instructions
description: Guidance for creating and maintaining Copilot skills, agent files, instructions, and prompts. Keep each instruction behavior-changing, prefer concise triggers, avoid duplication, and maintain a single source of truth for each rule.
---

# Writing Agent Instructions

## Trigger
Use when:
- Creating or editing `.github/agents/*.agent.md`.
- Creating or editing `.github/instructions/*.instructions.md`.
- Creating or editing `.github/prompts/*.prompt.md`.
- Pruning overly long agent instructions.

## Consumers
Human maintainers, `operator`, `docs`, or any future meta-agent tasked with agent maintenance.

## Required Behavior
1. **Keep each instruction behavior-changing.** If a line doesn't change what an agent does, remove it.
2. **Prefer concise trigger descriptions.** Use `Use when:` patterns.
3. **Use shared instruction files** for repeated behavior across multiple agents.
4. **Avoid duplicating** the same rule in many places unless the repetition is intentionally load-bearing.
5. **Put detailed reference behind links** when only some agents need it.
6. **Keep a single source of truth** for each rule.

## Completion Criteria
- Each instruction file changes agent behavior in a specific, testable way.
- No redundant copies of the same rule exist without explicit justification.
- Must-read rules are not hidden behind weakly worded references.
- Files are discoverable by their intended agents.

## Non-Goals
- Do **not** add process prose that does not change agent behavior.
- Do **not** copy external workflows verbatim without adapting to GitHub Copilot agent mechanics.
- Do **not** hide must-read rules behind weakly worded references.

## Conflicts
None. This skill helps maintain consistency with existing Ventus agent conventions.

## Dependencies
- `.github/instructions/agents.instructions.md`
- `.github/AGENTS_FLOW.md`
- `docs/project/AGENT_GUIDANCE.md`
