# Agent Flow - Greenfield Project

## Purpose

This flow keeps product intent, decisions, implementation, and validation connected while the project is still being discovered. The active project state and accepted documents are the source of truth.

## Core Chain

```text
User request
    |
    v
@Operator
    |
    +--> @PM ---------> outcome, scope, priority, backlog
    |
    +--> @Analyst ----> behavior, acceptance criteria, specification input
    |
    +--> @Design -----> architecture and boundary decisions
    |       |
    |       +---------> @ADRWriter / @Schema / @Security / @Protocol as needed
    |
    +--> @TestGen ----> scenarios, test plan, evidence plan
    |
    +--> @Scaffold ----> project structure and harness only
    |       |
    |       +----------> @AppDev or another approved implementation owner
    |
    +--> @Review ------> code and design risk review
    |
    +--> @Validate ----> evidence, gaps, readiness, and acceptance handoff
    |
    +--> @Docs / @Changelog / @StateWriter for recorded outcomes
```

## Role Ownership

| Role | Owns | Typical handoff |
| --- | --- | --- |
| `@Operator` | Routing, scope, prerequisites, and terminal status | Start and close every multi-step chain |
| `@PM` | Outcomes, priority, milestones, and backlog | `@Analyst` or `@Design` |
| `@Analyst` | Testable behavior and acceptance criteria | `@SpecWriter` then `@Design` |
| `@Design` | Architecture, boundaries, and technical direction | `@ADRWriter`, `@Schema`, `@Security`, or implementation |
| `@SpecWriter` | Specification file edits | `@Analyst` for validation |
| `@ADRWriter` | ADR file edits after a decision is resolved | `@Design` for validation |
| `@Schema` / `@SchemaWriter` | Durable data design and schema document edits | `@Migration` or implementation |
| `@Security` / `@SecurityWriter` | Threats, security decisions, and security records | `@Design` or implementation owner |
| `@TestGen` | Test scenarios, coverage, and validation plans | `@AppDev` or `@Validate` |
| `@Scaffold` | Empty project structure and tooling setup | `@TestGen` or implementation owner |
| `@AppDev` | General production behavior | `@Review` then `@Validate` |
| `@Review` | Findings, regressions, and residual risk | `@Operator` or owning agent |
| `@Refactor` | Behavior-preserving restructuring | `@Review` and `@Validate` |
| `@Docs` / `@Changelog` / `@StateWriter` | Documentation and project-state updates | `@Operator` |
| `@Validate` | Evidence-based readiness decision | `@Operator` |

## Non-Negotiable Gates

- Missing, stale, unaccepted, or unlinked required documents block implementation.
- Custom replacements for framework or platform behavior require a written exception record.
- Behavior-changing implementation requires a matching test plan or approved test exception.
- A validation result must identify what was tested, where, how, and what remains unknown.
- Agents report evidence; the user owns any required acceptance sign-off.
- Handoffs must identify the next owner and every unresolved blocker.

## Completion

`@Operator` may close a chain only when the requested behavior, documentation, tests, validation evidence, and project state agree, or when the user has explicitly accepted the recorded risk and follow-up.
