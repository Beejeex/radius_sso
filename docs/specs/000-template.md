# SPEC-000: Title

## Status

Draft

Allowed values: `Draft`, `In Review`, `Accepted`, `Superseded`.

## Ownership

- Analyst:
- Designer:
- Reviewers:

## Last Updated

YYYY-MM-DD

## Pre-Implementation Gate

This SPEC must be accepted with a passing Analyst `Document Validation Record` before implementation, test generation, migrations, scripts, fixtures, generated files, or implementation plans start. It is the behavior source of truth for AppDev, TestGen, Security, Schema, and Validate. Do not code first and backfill this SPEC later.

## Summary

Describe the specification in a few sentences.

## Goals

- Goal 1.
- Goal 2.

## Non-Goals

- Non-goal 1.
- Non-goal 2.

## Framework Defaults First

This specification must follow [Agent Guidance: Framework Defaults First](../project/AGENT_GUIDANCE.md).

Before specifying custom services, middleware, protocol handling, persistence stores, parsers, validators, schedulers, or security logic, identify the native framework, platform, or mature library feature that normally owns the behavior.

If this spec requires custom implementation where a default exists, include a custom implementation exception record here:

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

## Users And Stakeholders

- User or stakeholder group 1.
- User or stakeholder group 2.

## Requirements

### Functional Requirements

- Requirement 1.
- Requirement 2.

### Non-Functional Requirements

- Requirement 1.
- Requirement 2.

## Domain Notes

Describe the Ventus concepts, relationships, invariants, lifecycle rules, and naming introduced or affected by this specification.

Jellyfin may be used as an external compatibility reference, but this specification must not copy Jellyfin internal class names, storage shapes, or implementation structure into the Ventus core domain.

## UX And Workflow Notes

Describe user-visible workflows, administrator workflows, operational workflows, and expected states.

## API And Compatibility Boundary

Describe public API behavior, Jellyfin compatibility behavior, DTO mapping requirements, and boundary rules.

## Data And Persistence

Describe required persisted data, retention rules, migration concerns, backup/restore expectations, and cleanup behavior.

## Security And Authorization

Describe authentication, authorization, privacy, audit logging, WAF/edge throttling assumptions, and sensitive-data handling requirements.

## Observability

Describe logs, metrics, tracing, health, diagnostics, and operator visibility requirements.

## Open Questions

- Question 1.
- Question 2.

## Risks

- Risk 1.
- Risk 2.

## Related Documents

- ADRs:
- OpenAPI:
- Other specs:

## Implementation Status

Not started

Allowed values: `Not started`, `In progress`, `Done`, `Blocked`.
