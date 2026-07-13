---
description: "Fill-in-the-blank HANDOVER block template. Copy this, fill every field, and paste it at the end of your response when handing work to another agent."
---

# Handover Template

Copy the block below, fill every field, and include it at the **end** of your response when transitioning work to another agent.

Important: a handover block is a dispatch request. It does not run the receiving agent by itself. The current orchestrator must invoke the receiving agent with this block, and the emitting agent should stop after the handover unless it is explicitly invoked again.

```
--- HANDOVER ---
FROM:    <SENDING_AGENT_NAME_IN_UPPERCASE>
TO:      <RECEIVING_AGENT_NAME_IN_UPPERCASE>
TASK:    <One-line summary of exactly what the receiver needs to do>
STATUS:  <Design approved | Partially implemented | Blocked | Ready for review | ...>
CONTEXT:
  - <Key decision, constraint, interface name, or file path the receiver needs>
  - <Repeat for each relevant piece of context>
  - <Be specific about behavior, contracts, field names, constraints, and accepted technical decisions>
OPEN_QUESTIONS:
  - <Question the receiver must answer before starting work; "None" if clear>
BLOCKED_BY:
  - <Dependency not yet complete; "None" if receiver can start immediately>
--- END HANDOVER ---
```

---

## Field Guide

| Field | Required | What to write |
|---|---|---|
| `FROM` | Yes | Your agent name in uppercase — e.g. `OPERATOR`, `DESIGN`, `SCHEMA` |
| `TO` | Yes | Target agent name in uppercase — e.g. `SCAFFOLD`, `TESTGEN`, `APPDEV`, `MIGRATION` |
| `TASK` | Yes | One sentence. Verb + object. "Generate tests for Y", "Implement X after the red baseline", "Create migration for Z" |
| `STATUS` | Yes | The current state of the work being handed over |
| `CONTEXT` | Yes | Everything the receiver needs. Do not assume they read earlier turns. |
| `OPEN_QUESTIONS` | Yes | Write `None` if there are none — never leave blank |
| `BLOCKED_BY` | Yes | Write `None` if unblocked — never leave blank |

---

## Example — Design → Scaffold

```
--- HANDOVER ---
FROM:    DESIGN
TO:      SCAFFOLD
TASK:    Generate the approved library scanning contract and skeleton implementation
STATUS:  Interface design complete — ready for boilerplate generation
CONTEXT:
  - Contract operation: queue a scan for a stable library identifier with cancellation/abort support
  - Must depend on approved media-persistence and filesystem-access contracts
  - No real logic yet — generate skeletons/stubs and source API documentation in the selected toolchain's native form
  - Placement must follow the accepted scaffold/design decision
OPEN_QUESTIONS:
  - None
BLOCKED_BY:
  - None
--- END HANDOVER ---
```

## Example — Schema → Migration

```
--- HANDOVER ---
FROM:    SCHEMA
TO:      MIGRATION
TASK:    Add the approved persistence migration for the new access-session record
STATUS:  Persistence model approved and merged — migration not yet created
CONTEXT:
  - Record requirements: stable identifier, account relationship, registered-device identifier, hashed token value, expiry instant, lifecycle instants
  - Composite lookup index required for account plus registered device
  - Migration naming and execution mechanism must follow the approved persistence decision
  - Project placement must follow the accepted scaffold/design decision
OPEN_QUESTIONS:
  - None
BLOCKED_BY:
  - None
--- END HANDOVER ---
```

## Example — TestGen → AppDev

```
--- HANDOVER ---
FROM:    TESTGEN
TO:      APPDEV
TASK:    Implement the AccessSession API behavior to satisfy the red baseline
STATUS:  Red baseline defined — ready for production implementation
CONTEXT:
  - Tests cover valid session creation, invalid device IDs, duplicate device registration, and token hashing
  - Protocol shape has been confirmed by PROTOCOL
  - Keep implementation limited to the scenarios covered here; return to TESTGEN if new behavior is discovered
OPEN_QUESTIONS:
  - None
BLOCKED_BY:
  - None
--- END HANDOVER ---
```

## Example — OPERATOR blocking on design

```
--- HANDOVER ---
FROM:    OPERATOR
TO:      DESIGN
TASK:    Design the plugin sandbox boundary — how should a faulting plugin be isolated from the host process?
STATUS:  Implementation is blocked — sandbox strategy not decided
CONTEXT:
  - Plugins loaded from /plugins volume mount
  - Plugins may only use approved host capabilities exposed through the plugin contract
  - Risk: an unhandled exception in a plugin must not crash the main process
  - Relevant ADRs: none yet — this decision needs one
OPEN_QUESTIONS:
  - Which isolation mechanism satisfies the fault-containment and capability-boundary requirements for the selected implementation technology?
BLOCKED_BY:
  - None
--- END HANDOVER ---
```
