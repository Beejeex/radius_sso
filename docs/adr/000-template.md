# ADR-000: Title

## Status

Proposed

## Pre-Implementation Gate

This ADR must be accepted with a passing Design `Document Validation Record` before implementation, test generation, migrations, scripts, fixtures, generated files, or implementation plans start for work affected by this decision. Do not code first and backfill this ADR later.

## Context

Describe the problem, constraints, and forces that make a decision necessary.

## Decision

Describe the decision in clear, direct language.

## Framework Defaults First

This ADR must follow [Agent Guidance: Framework Defaults First](../project/AGENT_GUIDANCE.md).

Identify the native framework, platform, or mature library feature that normally owns this behavior. Prefer configuration and documented extension points before selecting custom implementation.

If this ADR chooses custom implementation where a default exists, include the exception record:

| Field | Required Content |
| --- | --- |
| Requirement | Current requirement that cannot be met by the default feature |
| Default considered | Framework, platform, or library feature normally responsible |
| Gap | Exact behavior the default cannot provide |
| Extension points considered | Configuration, events, callbacks, handlers, policies, filters, or adapters evaluated before replacement |
| Custom boundary | Smallest component that must be custom |
| Risk | Security, reliability, compatibility, operability, and maintenance risk added |
| Tests | Unit, integration, contract, and negative architecture tests required |
| Owner approval | Design and required domain reviewers |
| Removal trigger | Condition that would allow replacement with the default later |

## Consequences

Describe the expected benefits, trade-offs, risks, and follow-up work.

## Alternatives Considered

- Default framework/platform option: why it was or was not chosen.
- Alternative 1: why it was not chosen.
- Alternative 2: why it was not chosen.
