---
description: "Use when: writing or reviewing implementation source, public contracts, asynchronous or long-running behavior, dependency boundaries, naming, and source documentation"
applyTo: "**/*"
---

# Source Implementation Conventions

## Technology Boundary
- No language, runtime, framework, dependency-wiring mechanism, or documentation syntax is selected by this file
- Follow approved technical decisions and existing repository conventions for the implementation language actually selected
- If a needed language/runtime convention is not approved or established, escalate to design instead of inventing it

## Contracts & Naming
- Use Ventus-domain identifiers rather than Jellyfin internal implementation names
- Avoid exposing mutable implementation detail through public contracts
- Use timezone-aware/instant-preserving values for domain times unless an external contract fixes the wire representation
- Document public contracts and extension points using the selected toolchain's native documentation format

## Serialization
- In the React frontend, do not hand-write ad hoc API DTOs when the OpenAPI-generated TypeScript client already defines the contract; update the OpenAPI source and regenerate the client instead.
- Treat frontend API calls that bypass the generated client or the shared `request` wrapper as a blocking source-review issue unless the active ADR explicitly approves the exception.

## Dependencies & Long-Running Work
- Keep high-level behavior separated from concrete infrastructure details through approved abstraction boundaries
- Pass cancellation or abort controls through long-running/asynchronous flows where the selected technology supports them
- Avoid synchronous waiting inside request-processing paths where non-blocking operation is supported
- Release files, streams, processes, network resources, and other owned resources reliably

## Comments & Structure
- Comment non-obvious intent and trade-offs, especially complex queries and performance-sensitive processing
- Do not write comments that merely narrate visible code
- Keep components focused; split components that accumulate multiple responsibilities
