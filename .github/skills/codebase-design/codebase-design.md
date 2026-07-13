---
name: codebase-design
description: Shared architecture vocabulary for design and refactoring decisions. Core concepts: module, interface, implementation, seam, adapter, depth, leverage, locality. Prefer deep modules with small stable interfaces; place seams where variation actually exists.
---

# Codebase Design

## Trigger
Use when:
- Making architecture or refactoring decisions.
- Evaluating module boundaries and seam placement.
- Reviewing code for structural quality.
- Selecting test seams.

## Core Vocabulary
- **Module**: anything with an interface and implementation.
- **Interface**: everything callers must know to use the module correctly, including invariants and error modes.
- **Implementation**: what sits inside a module.
- **Seam**: a place where behavior can vary without editing the caller.
- **Adapter**: a concrete implementation at a seam.
- **Depth**: how much behavior sits behind a small interface.
- **Leverage**: caller value gained per unit of interface learned.
- **Locality**: concentration of change, bugs, and verification in one place.

## Consumers
`design` (architecture decisions), `refactor` (judge depth/locality improvements), `review` (flag shallow modules and leaky seams), `test-gen` (select test seams), `appdev` and domain agents (preserve or deepen modules)

## Required Behavior
- Prefer **deep modules** with small, stable interfaces.
- Place seams **where variation actually exists**.
- Treat **one adapter** as a hypothetical seam; **two adapters** as a real seam.
- **Test through the interface** whenever possible.
- Apply the **deletion test**: if deleting a module merely spreads complexity into callers, it earns its keep; if complexity vanishes, it may be pass-through.

## Completion Criteria
- Architecture decisions use precise vocabulary (module/interface/seam/adapter, not generic "service"/"component").
- Seams are justified by actual or clearly anticipated variation.
- Module depth/locality improves or is preserved.

## Non-Goals
- Do **not** add abstraction only because it is aesthetically tidy.
- Do **not** expose internal seams through public interfaces solely for tests.
- Do **not** call everything a service/component/API when module/interface/seam is more precise.

## Conflicts
None with existing Ventus gates.

## Dependencies
None beyond standard Ventus agent conventions.
