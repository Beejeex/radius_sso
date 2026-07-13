# Persistence Instructions

Use these rules for durable data, migrations, caches, queues, or state stores.

- Start from accepted behavior, lifecycle, ownership, and access patterns.
- Prefer framework-managed persistence behavior before adding custom state.
- Keep durable state separate from transient process state and derived data.
- Make identifiers, uniqueness, relationships, invariants, concurrency, retention, deletion, and recovery explicit.
- Design migrations for existing data and mixed-version operation when applicable.
- Never perform destructive data changes without an explicit approval and recovery plan.
- Do not put unrelated business behavior into migrations or persistence adapters.
- Add tests for mappings, constraints, migrations, backfills, failure, and idempotency as appropriate.
