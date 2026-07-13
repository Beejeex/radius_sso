# Starting Guide

## Purpose

This repository is a reusable starting point for a greenfield software project. It contains the documentation workflow, agent guidance, and templates needed to move from an idea to a validated implementation.

It intentionally does not choose a product name, domain, architecture, programming language, deployment model, or technology stack. Those decisions belong to the new project and must be recorded as they are made.

## Greenfield Principles

- Start from the user or business outcome, not from a preferred implementation.
- Keep product vocabulary separate from framework, vendor, and infrastructure vocabulary.
- Prefer native framework, platform, and library behavior before writing replacement code.
- Make important decisions explicit, reviewable, and reversible where possible.
- Define behavior and acceptance evidence before implementation begins.
- Keep secrets, generated output, machine state, and local configuration out of source control.
- Keep the repository understandable to a new contributor who has not seen the original idea.

## First Setup

1. Choose the project name and write a short problem statement.
2. Record the target users, primary outcome, non-goals, and constraints in the project state file.
3. Choose the initial development and deployment approach and record the decision in an ADR when it affects future work.
4. Replace the placeholder roadmap and backlog with the first meaningful milestone and a small set of ordered stories.
5. Create an accepted specification before implementing the first user-visible or externally consumed behavior.
6. Add the actual source, test, configuration, and deployment structure only after the required decisions are clear.

## Documentation Workflow

Use the repository documents in this order:

1. `docs/project/STATE.md` records the current phase, decisions, active work, risks, and next steps.
2. `docs/project/BACKLOG.md` records ordered work and its acceptance boundaries.
3. `docs/project/ROADMAP.md` groups backlog work into outcomes and milestones.
4. `docs/specs/` records product behavior and externally visible contracts.
5. `docs/adr/` records architectural and technology decisions.
6. `docs/schema/` records durable data design before persistence changes.
7. `docs/testing/` records evidence, test coverage, and validation gaps.
8. `docs/changelog/` records delivered user-visible or operational changes.

The owning document agent must validate a document before downstream implementation relies on it. Keep links between related documents current.

## Definition Of Ready

Before implementation starts, the work should have:

- a clear outcome and scope;
- explicit non-goals and open questions;
- an accepted specification for behavior;
- accepted architecture, security, deployment, or data decisions where relevant;
- an executable test or validation plan; and
- an owner for the next handoff.

## Definition Of Done

Work is not complete until the implementation, tests, documentation, configuration, and operational evidence agree. Record unresolved risks, skipped checks, and accepted exceptions rather than silently carrying them forward.

## First Commit Checklist

- Replace all `TBD` values that are required for the first milestone.
- Add a project-specific README and ignore rules when the stack is chosen.
- Confirm that no credentials, private keys, generated binaries, or local environment files are tracked.
- Keep the generic workflow files that remain useful; remove or adapt any role that does not apply to the new project.
- Update `docs/project/STATE.md` after the first project decisions are accepted.
