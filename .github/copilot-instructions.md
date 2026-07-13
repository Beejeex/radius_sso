# Copilot Instructions

This repository is a generic greenfield project template. The product name, domain, stack, deployment model, and external contracts are intentionally `TBD` until the new project defines them.

## Source Of Truth

- Read `STARTING_GUIDE.md` for the intended greenfield workflow.
- Read `docs/project/STATE.md` for current project context and active work.
- Read `docs/project/AGENT_GUIDANCE.md` for documentation gates and implementation rules.
- Treat accepted specifications, ADRs, schemas, and testing documents as the source of truth for their owned decisions.
- Do not invent requirements, architecture, API shapes, dependencies, or operational behavior that are not supported by the current project documents.

## Working Rules

- Start with the smallest useful vertical slice.
- Prefer native framework, platform, and library behavior before writing replacement code.
- Keep product vocabulary separate from infrastructure and vendor vocabulary.
- Keep domain, transport, persistence, and infrastructure concerns behind explicit boundaries.
- Do not write behavior-changing code before the required documentation and test plan are accepted.
- Do not commit secrets, local environment files, generated binaries, or private data.
- Do not silently broaden scope or refactor unrelated code.
- Update project state, related documents, and changelog entries when work changes accepted behavior or decisions.

## Quality And Security

- Validate user-controlled input at trust boundaries.
- Use established security primitives for authentication, authorization, secrets, sessions, tokens, and request protection.
- Make failure, recovery, idempotency, and concurrency behavior explicit where they matter.
- Test the real boundaries that users or external systems consume; unit tests alone are not enough for every feature.
- Report execution context, command, result, skipped checks, and residual risk for validation work.

## Agent Routing

Use `@Operator` as the coordinator for multi-step work. Route requirements to `@PM` and `@Analyst`, design decisions to `@Design`, all document writing to `@Docs`, implementation to `@AppDev`, test planning to `@TestGen`, project setup to `@Scaffold`, review to `@Review`, security review to `@Security`, and final evidence to `@Validate`.

`@Schema` and `@Migration` apply when the project adopts durable data. Add other specialist roles only when a concrete project requirement justifies them.
