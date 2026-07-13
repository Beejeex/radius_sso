# Copilot Instructions

This repository is a generic greenfield project template. Product name, domain, stack, deployment model, and external contracts are intentionally `TBD` until the new project defines them.

## Read First

- [Starting Guide](../STARTING_GUIDE.md) for the greenfield workflow.
- [Project State](../docs/project/STATE.md) for current context and active work.
- [Agent Flow](AGENTS_FLOW.md) for routing and ownership.
- [Agent Guidance](../docs/project/AGENT_GUIDANCE.md) for canonical project policy and gates.

Accepted specifications, ADRs, schemas, and testing documents are the source of truth for their decisions. Do not invent requirements, architecture, API shapes, dependencies, or operational behavior.

## Routing

Use `@Operator` for multi-step work. Route product intent to `@PM` and `@Analyst`, design to `@Design`, all document writing to `@Docs`, implementation to `@AppDev`, tests to `@TestGen`, scaffolding to `@Scaffold`, review to `@Review`, security to `@Security`, and evidence to `@Validate`.

## Repository Hygiene

- Preserve unrelated user changes.
- Keep secrets, local environment files, generated binaries, and private data out of source control.
- Keep changes narrow and update project state when accepted scope or decisions change.
