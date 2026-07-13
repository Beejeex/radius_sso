---
description: "Use when: writing unit tests, integration tests, generating test cases, increasing coverage, testing services, persistence adapters, request handlers, or domain behavior"
applyTo: "**/*test*"
---

# Testing Conventions

## Technology Boundary
- No test runner, assertion library, fixture/data-generation library, mocking library, integration harness, or coverage tool is selected by this file
- Use approved testing tooling and existing repository convention; if none exists, define scenarios and harness requirements before selecting tools

## Naming
- Test names must communicate the subject, scenario and expected behavior using the selected framework's convention

## Structure
- Keep arrange/action/assertion phases clear and readable
- Use the selected framework's data-driven test mechanism for parameterized cases
- Keep one logical assertion focus per test

## Tooling
- Use deterministic builders/fixtures or explicit minimal inputs as appropriate for clarity
- Use fakes, stubs or mocks only where isolation requires them, following approved tooling
- Do not use fixed sleeps for synchronization; use deterministic coordination supported by the selected technology
- Local test/build commands are allowed for inner-loop validation
- Tests that need infrastructure may start isolated local dependency containers, such as PostgreSQL via Docker Compose or Testcontainers
- Record execution context for every run: `Local`, `Local + dependency containers`, or `Production Docker container`

## Isolation
- Unit tests must not touch the real filesystem, durable data service, or real FFmpeg process
- Integration tests may use isolated disposable/containerized dependencies where real infrastructure behavior matters
- Exercise cancellation/abort behavior for operations that expose it

## Minimum Coverage per Class
- At least one happy-path test
- At least one null / invalid-input test
- At least one boundary / edge-case test

## Jellyfin-Specific Test Cases
Always include tests for:
- Auth header parsing — valid `MediaBrowser` scheme, missing fields, wrong scheme
- Tick values — ensure no accidental seconds or milliseconds
- GUID item IDs — must reject integer-format IDs
- Paginated responses — verify `TotalRecordCount` is present
- WebSocket message envelope — correct `MessageType` routing

## Coverage Gate
- **80%** line coverage on the core domain and application/business-logic projects, using final project names chosen during design and approved measurement tooling
- A feature is not done until its tests are green — never defer tests to a later task
- Local runs support inner-loop confidence. For production milestones, Local Acceptance must prove the running local instance uses SQL and the supported OIDC backend before user Local Acceptance GREEN. Final delivery still requires Production Docker Acceptance evidence and user Production Docker Acceptance GREEN when that gate applies.
