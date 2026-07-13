---
name: domain-modeling
description: Keep agent language aligned with Ventus product terms and accepted decisions. Distinguish Ventus internal concepts from Jellyfin compatibility terminology. Challenge vague or overloaded terms before they leak into specs, schemas, tests, or code.
---

# Domain Modeling

## Trigger
Use when:
- A term is ambiguous or conflicts with existing project docs.
- A feature introduces or sharpens product terminology.
- An ADR-worthy decision depends on domain language.
- Specs, schemas, tests, or code names drift from accepted vocabulary.

## Consumers
`analyst` (own product terms in specs), `design` (use domain language in architecture), `schema` (align persisted names), `protocol` (distinguish Ventus from Jellyfin compatibility shapes), `docs` (preserve accepted language), domain agents (use accepted names in code and tests)

## Required Behavior
1. **Read** relevant specs, ADRs, schema docs, and `docs/project/STATE.md` when domain language matters.
2. **Challenge** vague or overloaded terms.
3. **Distinguish** Ventus internal concepts from Jellyfin compatibility terminology.
4. **Record** durable changes only through the correct document owner and validation flow.

## Completion Criteria
- All agents in a chain use consistent domain terms.
- No Jellyfin-internal names leak into Ventus domain language.
- Ambiguous terms have been clarified or escalated.

## Non-Goals
- Do **not** create free-floating glossary docs without owner approval.
- Do **not** treat implementation class names as domain terms by default.
- Do **not** rewrite accepted domain language without routing through the owning agent.

## Conflicts
None with existing Ventus gates.

## Dependencies
- `docs/project/STATE.md`
- `docs/adr/`
- `docs/schema/`
- `docs/specs/`
