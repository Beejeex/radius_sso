# Handover Prompt

Use this prompt when one agent hands work to another.

```text
--- HANDOFF ---
FROM: <agent>
TO: <agent>
TASK: <one concrete outcome>
WHY: <user or project outcome>
SOURCE DOCUMENTS: <accepted specs, ADRs, schemas, tests, or state>
CHANGED FILES: <files or None>
EVIDENCE: <commands, results, links, or None>
ASSUMPTIONS: <explicit assumptions or None>
BLOCKERS: <blockers or None>
REQUESTED RETURN: <exact decision, edit, test, or validation>
--- END HANDOFF ---
```

The receiving agent must confirm ownership and prerequisites, then return the requested evidence or an explicit blocker.
