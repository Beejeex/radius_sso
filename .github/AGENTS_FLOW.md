# Agent Flow - Greenfield Project

## Purpose

This file defines routing and ownership. The project policy, documentation gates, default-first rule, security rules, and evidence requirements live canonically in [Agent Guidance](../docs/project/AGENT_GUIDANCE.md).

## Core Chain

```text
User request
    |
    v
@Operator
    |
    +--> @PM ---------> outcome, scope, priority, backlog
    +--> @Analyst ----> behavior, acceptance criteria
    +--> @Design -----> architecture and boundaries
    |       +---------> @Schema / @Security when needed
    +--> @Docs --------> specifications, decisions, state, testing, changelog
    +--> @TestGen -----> scenarios, test plan, evidence plan
    +--> @Scaffold ----> project structure and harness only
    |       +----------> @AppDev or another approved implementation owner
    +--> @Review ------> code and design risk review
    +--> @Validate ----> evidence, gaps, readiness, acceptance handoff
```

## Role Ownership

| Role | Owns | Typical handoff |
| --- | --- | --- |
| `@Operator` | Routing, scope, prerequisites, and terminal status | Start and close every multi-step chain |
| `@PM` | Outcomes, priority, milestones, and backlog | `@Analyst` or `@Design` |
| `@Analyst` | Testable behavior and acceptance criteria | `@Docs`, then `@Design` |
| `@Design` | Architecture, boundaries, and technical direction | `@Docs`, `@Schema`, `@Security`, or implementation |
| `@Docs` | All project documentation and records | Content owner for validation |
| `@Schema` | Durable data design and persistence constraints | `@Docs`, then `@Migration` or implementation |
| `@Security` | Threats, security decisions, and security records | `@Docs`, then implementation owner |
| `@TestGen` | Test scenarios, coverage, and validation plans | `@AppDev` or `@Validate` |
| `@Scaffold` | Empty project structure and tooling setup | `@TestGen` or implementation owner |
| `@AppDev` | General production behavior | `@Review`, then `@Validate` |
| `@Review` | Findings, regressions, and residual risk | `@Operator` or owning agent |
| `@Refactor` | Behavior-preserving restructuring | `@Review` and `@Validate` |
| `@Migration` | Applying accepted durable-data changes | `@Validate` |
| `@Validate` | Evidence-based readiness decision | `@Operator` |

## Routing Rules

- Use `@Operator` for multi-step work and specialist coordination.
- Use `@Docs` as the only project-document writer; content owners validate the resulting files.
- Add a specialist role only when a concrete project requirement justifies it.
- Handoffs must use the shared format in `.github/prompts/handover.prompt.md`.
