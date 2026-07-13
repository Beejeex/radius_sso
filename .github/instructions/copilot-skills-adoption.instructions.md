---
description: "Use when: creating, updating, validating, or wiring Ventus GitHub Copilot skills, agents, shared instructions, or prompt templates; maps the active .github/skills workflows for grilling, handoff, bug diagnosis, TDD, codebase design, domain modeling, spec synthesis, vertical breakdown, prototypes, triage, and skill-writing guidance into the .github ecosystem"
---

# Copilot Skills Adoption

This file records the active Ventus Copilot skill set and the rules for maintaining it.

Source repository:

https://github.com/mattpocock/skills

Important: GitHub Copilot skills are active in this repo under `.github/skills/`. This instruction file explains why those skills exist, how they relate to the existing `.github` agents, and how to maintain them.

This is not a project-state document. Do not link it from `docs/project/STATE.md`, roadmaps, backlog, milestone specs, ADRs, schemas, or changelog entries unless the user explicitly asks to make Copilot skill adoption part of the product project.

The target runtime is the Ventus GitHub Copilot agent ecosystem:

- Copilot skills in `.github/skills/<skill-name>/<skill-name>.md`
- `.github/agents/*.agent.md`
- `.github/instructions/*.instructions.md`
- `.github/prompts/*.prompt.md`
- `.github/copilot-instructions.md`
- `.github/AGENTS_FLOW.md`

## Adoption Intent

Ventus is adopting the useful operating practices as Copilot skills plus supporting agent instructions. Do not copy the external repository wholesale.

Adopted practices should make Copilot agents:

- Clarify ambiguous work before coding.
- Preserve context across bounded agent sessions.
- Build tight feedback loops for bugs and implementation.
- Test through stable public seams.
- Speak with consistent design and domain-modeling vocabulary.
- Split large work into independently verifiable vertical slices.
- Improve local skills and agent instructions without bloating every agent prompt.

Existing Ventus agent rules remain authoritative. Any adopted practice must fit the blocking requirements in `.github/instructions/agents.instructions.md` and the project rules in `docs/project/AGENT_GUIDANCE.md`.

## Non-Goals

Do not adopt:

- Thin aliases from the source repository, such as `grill-me` or `grill-with-docs`.
- Deprecated source workflows as active Ventus workflows.
- Personal source workflows with hardcoded local paths.
- Claude-specific hook installation.
- Generic pre-commit/package-manager setup that conflicts with Ventus Docker-only development.
- Any workflow that bypasses Operator routing, document validation, framework-default rules, TDD gates, backend-ready-for-UI gates, Local Acceptance, or Production Docker Acceptance.

## Artifact Types

Use the right artifact type for each change:

| Artifact type | Location | Use |
| --- | --- | --- |
| Copilot skill | `.github/skills/<skill-name>/<skill-name>.md` | Reusable workflow with a clear trigger, steps, completion criteria, non-goals, and optional resources. |
| Shared instruction | `.github/instructions/<name>.instructions.md` | Reusable behavior that many agents should follow. |
| Agent update | `.github/agents/<agent>.agent.md` | Role-specific behavior for one Copilot agent. |
| Prompt template | `.github/prompts/<name>.prompt.md` | Fill-in-the-blank template for a repeated handoff or document. |
| Flow update | `.github/AGENTS_FLOW.md` or `.github/instructions/agents.instructions.md` | Only when the practice changes the global agent protocol. Keep rare. |

Prefer a Copilot skill when the practice has a reusable workflow, clear trigger, and completion criteria. Prefer shared instruction files when a rule should quietly shape many agents. Prefer agent updates when the behavior belongs to one role. Prefer prompt templates for repeated structured output.

## Active Copilot Skills

These skills are implemented under `.github/skills/`.

| Priority | Skill | Based on | Primary consumers | Why it exists as a skill |
| --- | --- | --- | --- | --- |
| 1 | `grilling` | `grilling` | `operator`, `analyst`, `design`, `schema`, `security`, `test-gen`, `review`, `refactor` | Explicit reusable interview workflow with clear triggers and a stop condition. |
| 1 | `handoff` | `handoff` | `operator`, all specialist agents | Repeatable context-transfer workflow that complements the existing handover prompt. |
| 1 | `diagnosing-bugs` | `diagnosing-bugs` | `review`, `test-gen`, fixer agents, `validate` | Strong phased loop that prevents premature debugging. |
| 1 | `tdd-vertical-slices` | `tdd` | `test-gen`, `appdev`, domain agents, `refactor` | Reusable implementation discipline with test-first completion criteria. |
| 2 | `codebase-design` | `codebase-design` | `design`, `refactor`, `review`, `test-gen`, code agents | Shared architecture vocabulary and refactor evaluation rules. |
| 2 | `domain-modeling` | `domain-modeling` | `analyst`, `design`, `schema`, `protocol`, `docs`, domain agents | Keeps domain terms and decisions coherent across documents, tests, and code. |
| 2 | `spec-synthesis` | `to-prd` | `analyst`, `pm`, `spec-writer` | Adapts PRD synthesis into Ventus's SPEC/ADR/schema workflow. |
| 2 | `vertical-work-breakdown` | `to-issues` | `operator`, `pm`, `analyst`, `test-gen` | Turns large accepted work into independently verifiable vertical slices. |
| 3 | `prototype` | `prototype` | `design`, `appdev`, UI/domain agents | Useful when a throwaway artifact can answer a design question. |
| 3 | `intake-triage` | `triage` | `operator`, `pm`, `review` | Provides a state machine for raw work before implementation. |
| 3 | `writing-agent-instructions` | `writing-great-skills` | maintainers, `docs`, future meta-agent | Helps maintain Copilot skills, agent files, instructions, and prompts without bloat. |

Every skill must specify:

- Name and trigger/description.
- When to use it.
- Agents that may invoke or reference it.
- Required workflow.
- Completion criteria.
- Non-goals.
- Handoff or return behavior.
- Conflicts with existing Ventus gates, if any.
- `.github` files it depends on.

## Adoption Tiers

### Tier 1: Adopt First

These should become normal agent habits.

| Practice | Source skill | Primary Copilot agent targets |
| --- | --- | --- |
| Clarification grilling | `grilling` | `operator`, `analyst`, `design`, `schema`, `security`, `test-gen`, `review`, `refactor` |
| Session handoff | `handoff` | `operator`, all agents that return large or resumable work |
| Bug diagnosis loop | `diagnosing-bugs` | `review`, `test-gen`, owning fixer agents, `validate` |
| TDD vertical slices | `tdd` | `test-gen`, `appdev`, domain code agents, `refactor` |

### Tier 2: Adopt For Multi-Agent Maturity

These help larger work stay coherent across sessions and agents.

| Practice | Source skill | Primary Copilot agent targets |
| --- | --- | --- |
| Codebase design vocabulary | `codebase-design` | `design`, `refactor`, `review`, `appdev`, domain agents, `test-gen` |
| Domain modeling discipline | `domain-modeling` | `analyst`, `design`, `schema`, `protocol`, `docs`, domain agents |
| Product/design synthesis | `to-prd` | `analyst`, `pm`, `spec-writer` |
| Vertical work breakdown | `to-issues` | `operator`, `pm`, `analyst`, `test-gen` |

### Tier 3: Adopt Selectively

Use these only when the task clearly benefits.

| Practice | Source skill | Primary Copilot agent targets |
| --- | --- | --- |
| Throwaway prototypes | `prototype` | `design`, `appdev`, UI-capable agents, domain agents |
| Intake state machine | `triage` | `operator`, `pm`, `review` |
| Agent-instruction writing guidance | `writing-great-skills` | maintainers editing `.github/agents`, `.github/instructions`, `.github/prompts` |

## Active Skill Files

The active skill files are the single source of truth for each workflow. Do not duplicate their full workflow text in this adoption record or in individual agent files.

| Skill | Active file |
| --- | --- |
| `grilling` | `.github/skills/grilling/grilling.md` |
| `handoff` | `.github/skills/handoff/handoff.md` |
| `diagnosing-bugs` | `.github/skills/diagnosing-bugs/diagnosing-bugs.md` |
| `tdd-vertical-slices` | `.github/skills/tdd-vertical-slices/tdd-vertical-slices.md` |
| `codebase-design` | `.github/skills/codebase-design/codebase-design.md` |
| `domain-modeling` | `.github/skills/domain-modeling/domain-modeling.md` |
| `spec-synthesis` | `.github/skills/spec-synthesis/spec-synthesis.md` |
| `vertical-work-breakdown` | `.github/skills/vertical-work-breakdown/vertical-work-breakdown.md` |
| `prototype` | `.github/skills/prototype/prototype.md` |
| `intake-triage` | `.github/skills/intake-triage/intake-triage.md` |
| `writing-agent-instructions` | `.github/skills/writing-agent-instructions/writing-agent-instructions.md` |

## Agent Mapping Matrix

Use this matrix when deciding where to install future instruction references.

| Agent | Practices to adopt |
| --- | --- |
| `operator` | grilling, handoff, vertical work breakdown, intake triage |
| `pm` | product/design synthesis, vertical work breakdown, intake triage |
| `analyst` | grilling, domain modeling, product/design synthesis |
| `spec-writer` | product/design synthesis output format only |
| `design` | grilling, codebase design, domain modeling, prototypes |
| `adr-writer` | codebase design vocabulary when recording architecture decisions |
| `schema` | grilling, domain modeling, vertical work breakdown |
| `schema-writer` | domain language consistency |
| `security` | grilling, bug diagnosis for security findings, domain modeling for auth-path terms |
| `security-writer` | domain language consistency |
| `review` | bug diagnosis, grilling, codebase design, intake triage |
| `refactor` | grilling, TDD, codebase design |
| `test-gen` | grilling, bug diagnosis, TDD, vertical slices |
| `validate` | bug diagnosis evidence, TDD evidence, handoff completeness |
| `appdev` | TDD, codebase design, bug diagnosis, prototypes when approved |
| Domain agents | TDD, codebase design, domain modeling, bug diagnosis |
| `docs` | domain modeling, handoff, agent-instruction writing guidance |
| `changelog` | no direct adoption except handoff clarity |
| `challenger` | grilling as pressure-test style, but must remain read-only and advisory |

## Suggested Rollout

1. Create shared instruction files for Tier 1 practices.
2. Reference those files from the highest-impact agents: `operator`, `review`, `test-gen`, `appdev`, `design`, and `validate`.
3. Add Tier 2 vocabulary and synthesis practices to `design`, `analyst`, `pm`, `schema`, and `refactor`.
4. Trial Tier 3 practices only after a real use case appears.
5. After one milestone, prune any adopted instruction that did not change agent behavior.

## Acceptance Criteria For Future Adoption Work

When an agent or maintainer creates, updates, or wires a Copilot skill or instruction, the change is complete only when:

- The new or edited `.github` files name exactly which agents may invoke, reference, or obey the skill/practice.
- The skill or instruction includes triggers, required behavior, non-goals, and completion criteria.
- The instruction does not conflict with `.github/instructions/agents.instructions.md`.
- The instruction does not bypass `docs/project/AGENT_GUIDANCE.md`.
- The skill or instruction is discoverable by the intended agents.
- Redundant copies are avoided or intentionally justified.
- The change is reviewed against at least one realistic Ventus workflow.

## Maintenance Rule

This skill adoption record should remain behind the agent system. It is allowed to guide `.github` skill and agent work, but it should not become part of Ventus product state unless the user explicitly chooses to track Copilot skill adoption as project scope.
