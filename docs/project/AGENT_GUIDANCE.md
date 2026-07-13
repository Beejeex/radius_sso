# Agent Guidance: Greenfield Defaults First

## Status

Template

## Applies To

All agents that create, review, validate, or implement project documentation, source code, tests, configuration, or generated files.

## Core Rule

Prefer native framework, platform, and mature library behavior over custom replacements. A custom implementation is allowed only when a current project requirement cannot be met by the default behavior and the gap is documented before implementation.

An exception record must identify the requirement, default behavior considered, exact gap, smallest custom boundary, risks, tests, approving owner, and removal trigger.

## Documentation Gate

Before implementation begins, the active work must have accepted documentation for the behavior and decisions it depends on:

- a SPEC for product behavior, workflows, contracts, acceptance expectations, and non-goals;
- ADRs for architecture, security, technology, compatibility, or deployment decisions that affect the work; and
- a schema document for persisted data, migrations, indexes, constraints, or storage changes.

Accepted means the owning document agent has read the actual file and recorded a passing validation record. Draft, stale, missing, unlinked, or pending documents are blockers unless the user records an explicit exception with risk and follow-up.

## Implementation Rules

- Define the smallest useful vertical slice.
- Write or approve the relevant test plan before behavior-changing production code.
- Keep domain behavior separate from transport, persistence, framework, and infrastructure concerns.
- Use explicit boundaries for authentication, authorization, secrets, external services, and user-controlled input.
- Do not add persistent state for behavior already owned by the framework or platform without an approved exception.
- Do not mix unrelated refactors into a feature slice.
- Update project state and related documentation when scope or decisions change.

## Testing And Validation

Tests should prove required behavior and important composition boundaries, not accidental implementation details. Select the test layers that match the risk: unit, integration, contract, browser, performance, security, or operational checks.

Every reported check must include its execution context, command or procedure, result, failures, skips, and remaining gaps. A test result is evidence; it is not a substitute for user acceptance where the project requires user sign-off.

## Security

- Never commit secrets or real personal data.
- Validate trust boundaries and user-controlled input.
- Use framework security primitives before custom cryptography, session, token, authorization, or request-protection code.
- Keep error messages useful without exposing secrets or hidden resource state.
- Record security-sensitive decisions and residual risks.

## Handoffs

Handoffs must name the requested outcome, source documents, changed files, evidence, blockers, and the next owner. If a prerequisite is missing or a requested change crosses ownership boundaries, stop and hand it back rather than silently absorbing the work.
