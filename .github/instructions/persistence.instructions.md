---
description: "Use when: designing stored data, schema evolution, persistence queries, indexes, integrity constraints, durable shared state, and persistence-backed background operations"
applyTo: "**/*schema*,**/*migration*,**/*persistence*,**/*repository*"
---

# Persistence Conventions

## Technology Boundary
- No database engine, data model style, ORM, migration tool, cache product, or provider-specific feature is selected by this file
- Bind persistence designs to implementation syntax only after an approved technical decision selects the technology

## Data Design
- Do not replicate a single nullable-column-heavy media record merely because the reference implementation used one
- Define stable identifiers, relationships, integrity constraints, and lifecycle timestamps from domain needs and wire constraints
- Choose structured-value and type-specialization storage based on access patterns, integrity, portability and migration risk

## Indexes & Queries
- Propose indexes for relationship keys and frequent filter/sort patterns with an explicit usage justification
- Avoid N+1 read/write patterns and document deliberate batching/eager-loading choices
- Use keyset/cursor pagination for very large ordered collections when it matches product requirements
- Ad-hoc/raw queries must be parameterized, justified and security-reviewed

## Evolution & Shared State
- Every persisted-schema change needs an approved migration/evolution path
- Flag destructive changes and require explicit confirmation before applying data-loss operations
- Provide a rollback path or an explicitly justified recovery plan
- Shared state needed across replicas must live in approved durable/distributed persistence, never only in process memory
- Long-running write operations must be idempotent and executed through approved background-work handling
