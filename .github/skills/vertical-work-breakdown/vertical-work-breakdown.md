---
name: vertical-work-breakdown
description: Split large accepted work into independently implementable and verifiable vertical slices. Each slice cuts through all required layers (schema/API/application/UI/tests/docs). Prefer vertical over horizontal batching.
---

# Vertical Work Breakdown

## Trigger
Use when:
- Large accepted work needs to be split into implementable chunks.
- Multiple agents will work on related slices.
- A feature spans backend, API, frontend, and tests.

## Consumers
`operator` (choose routing and phase boundaries), `pm` (update backlog/roadmap), `analyst` (keep slices tied to accepted behavior), `test-gen` (map tests per slice)

## Required Behavior
1. **Prefer vertical slices over horizontal batches.**
2. A slice should cut through **all required layers** for one behavior: schema/API/application/UI/tests/docs where applicable.
3. **Identify blockers** between slices.
4. Keep each slice **demoable or independently verifiable**.
5. **Record batching exceptions explicitly** when the user accepts risk.
6. Respect the **pre-implementation documentation gate** and **backend-ready-for-UI rule** per slice.

## Completion Criteria
- Each slice is independently implementable and verifiable.
- Slice dependencies are explicitly mapped.
- No slice starts production UI before its backend-ready-for-UI evidence exists.
- Horizontal batches are avoided unless explicitly justified and user-accepted.

## Non-Goals
- Do **not** create all-backend, all-UI, or all-tests batches by default.
- Do **not** start production UI before backend-ready-for-UI evidence exists.
- Do **not** break slices so small they lose behavioral coherence.

## Conflicts
None. Must respect backend-ready-for-UI gate.

## Dependencies
- `docs/project/AGENT_GUIDANCE.md`
- `docs/project/BACKLOG.md`
- `docs/project/ROADMAP.md`
