---
description: "Use when: create, update, validate, or wire a reusable project skill, agent, instruction, or prompt"
name: "Skill Adoption"
applyTo: ".github/**"
---

# Skill And Agent Maintenance

This repository contains reusable development guidance for a greenfield project. Keep skills, agents, instructions, and prompts consistent with the generic workflow in `.github/AGENTS_FLOW.md` and `docs/project/AGENT_GUIDANCE.md`.

## Rules

- A skill must have a clear trigger, purpose, inputs, outputs, boundaries, and validation method.
- Prefer a small skill that composes with the existing agent flow over a duplicate workflow.
- Do not encode product, vendor, stack, or deployment assumptions in a generic skill.
- Keep source-of-truth ownership explicit and avoid conflicting instructions.
- Include examples only when they are stack-neutral or clearly marked as placeholders.
- Test a skill against at least one realistic workflow and record any limitation.
