# SCHEMA-NNN: [Persisted data capability]

**Status:** Draft
**Owner:** TBD
**Date:** YYYY-MM-DD
**Related specification:** SPEC-NNN

## Purpose And Scope

[Describe the durable data needed by the accepted behavior.]

## Ownership And Lifecycle

| Data | Owner | Created by | Updated by | Deleted by | Retention |
| --- | --- | --- | --- | --- | --- |
| [Record or aggregate] | [Boundary] | [Operation] | [Operation] | [Operation] | [Policy] |

## Logical Model

| Entity | Meaning | Required fields | Relationships |
| --- | --- | --- | --- |
| [Entity] | [Meaning] | [Fields] | [Links] |

## Integrity Rules

- [Required invariant]
- [Uniqueness or referential rule]
- [Concurrency or idempotency rule]

## Access Patterns And Indexes

| Query or command | Expected frequency | Required ordering or filtering | Supporting index |
| --- | --- | --- | --- |
| [Operation] | [Frequency] | [Pattern] | [Index or TBD] |

## Migration And Recovery

- Migration approach: TBD
- Backward compatibility: TBD
- Rollback or forward-fix strategy: TBD
- Backup and restore impact: TBD

## Framework Defaults Check

[Identify which persistence behavior is provided by the selected framework or library. Record an exception before adding custom state for behavior it normally owns.]

## Validation Record

| Round | Agent | Result | Notes |
| --- | --- | --- | --- |
| 1 | TBD | Pending | Awaiting owner review |
