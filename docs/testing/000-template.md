# <Milestone> Unit Test Tracking

| Field | Value |
| --- | --- |
| Status | Draft |
| Date | YYYY-MM-DD |
| Owner | @TestGen |
| Validator | @Validate |
| Milestone | v0.x.x |
| Specs | SPEC-xxx |
| ADRs | ADR-xxx |
| Stories | STORY-xxx |

Allowed status values: `Draft`, `In Progress`, `Ready For Validation`, `Accepted`, `Blocked`, `Superseded`.

## Scope

Describe the unit-test scope for this milestone.

Include:
- Features, stories, or behaviors covered.
- Projects, namespaces, or components covered.
- Explicitly excluded tests, such as integration, compatibility, load, or manual accessibility checks.

## Framework Defaults First

This test plan must follow [Agent Guidance: Framework Defaults First](../project/AGENT_GUIDANCE.md).

Tests should verify required behavior and approved framework composition before locking in custom implementation details. Do not create tests that require custom services, middleware, protocol handling, stores, parsers, validators, or security logic when a native framework feature should own the behavior.

If custom implementation is approved, reference the exception record and list the tests that constrain its boundary:

| Custom Component | Approved Exception Source | Boundary Tests | Negative Architecture Tests |
| --- | --- | --- | --- |
| Component name | SPEC/ADR reference | Tests proving required behavior | Tests preventing behavior from spreading or replacing defaults |

## Boundary And Runtime Coverage

Fake clients and component-only tests are useful for UI state coverage, but they are not sufficient for UI/API behavior. TestGen must add or explicitly mark the following coverage for every production-facing milestone that touches web/API behavior:

| Coverage Layer | Required When | Proves | Status |
| --- | --- | --- | --- |
| Real web API client contract tests | An OpenAPI-generated TypeScript client or equivalent API wrapper is added or changed | Routes, JSON serialization, headers, status handling, and DTO shapes match the server boundary | Planned |
| API wire-format tests | An endpoint is consumed by a web client, compatibility client, or external caller | Representative JSON payloads use the expected property names, required fields, enum/string values, pagination shape, and error shape | Planned |
| Web DI/composition tests | Pages or components inject new services, clients, providers, or handlers | Required services are registered and pages do not fail at composition time | Planned |
| Browser boot/render smoke | Production-facing web UI is added or changed | The real app loads in a browser and key pages render without React boot, routing, asset, or API-client failures | Planned |

If a required layer is missing, record it as a blocking coverage gap unless the user explicitly accepts the risk. Do not mark a milestone ready for validation when fake-client tests are the only evidence for UI/API behavior.

## Backend Ready For UI

Use this section when a milestone includes production frontend/UI work. UI implementation may start only after the consumed backend/API capability is implemented, tested, contract-validated, auth-classified, and explicitly marked `Backend Ready For UI`.

| Backend Capability | Required For UI Slice | Backend Tests | API/Client Contract Evidence | Auth Path Evidence | Backend Ready For UI? | Evidence / Notes |
| --- | --- | --- | --- | --- | --- | --- |
| TBD | TBD | SKIPPED | None | None | No | Pending backend validation |

## UI Slice Green Loop

Track frontend/UI work one slice at a time. Do not start the next route, page, component, form, dialog, table, navigation path, or workflow until the previous slice is GREEN, unless the user explicitly accepts batching risk and follow-up.

| Order | UI Slice | Consumed Backend Capability | Required Evidence | Latest Result Flag | Batching Exception | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | TBD | TBD | Component/API-client/browser evidence | SKIPPED | None | Waiting for backend-ready evidence |

## Result Flags

Use these flags for every recorded test run.

| Flag | Meaning | Validation Impact |
| --- | --- | --- |
| RED-EXPECTED | Failing baseline created before implementation as part of TDD | Allowed only before implementation or while a known red baseline is active |
| RED-REGRESSION | A test that should pass is failing | Blocks validation |
| GREEN | All tests in the recorded scope passed | Eligible for validation |
| BLOCKED | The approved command or required dependency container could not run | Blocks validation until resolved or explicitly accepted by the user |
| SKIPPED | Tests were intentionally not run | Blocks validation unless the reason is accepted |
| FLAKY | Results are inconsistent across runs | Blocks validation until investigated |

## Current Summary

| Area | Total Required | Implemented | Passing | Failing | Blocked | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| Domain | 0 | 0 | 0 | 0 | 0 | Pending |
| Application | 0 | 0 | 0 | 0 | 0 | Pending |
| API/request pipeline | 0 | 0 | 0 | 0 | 0 | Pending |
| API wire contracts | 0 | 0 | 0 | 0 | 0 | Pending |
| Web API client contracts | 0 | 0 | 0 | 0 | 0 | Pending |
| Web DI/composition | 0 | 0 | 0 | 0 | 0 | Pending |
| Browser boot/render smoke | 0 | 0 | 0 | 0 | 0 | Pending |
| Persistence mappings | 0 | 0 | 0 | 0 | 0 | Pending |
| Production Docker Acceptance | 0 | 0 | 0 | 0 | 0 | Pending |

## Required Unit Test Coverage

| Story / Requirement | Behavior | Test Project | Test File | Required Scenarios | Status |
| --- | --- | --- | --- | --- | --- |
| STORY-xxx | Behavior summary | tests/<Project> | <TestFile>.cs | Happy path; invalid input; boundary case | Planned |

Allowed status values: `Planned`, `RED-EXPECTED`, `Implemented`, `GREEN`, `RED-REGRESSION`, `Blocked`, `Deferred`.

## Test Inventory

| Test Project | Test File | Test Name | Type | Covers | Current Flag | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| tests/<Project> | <TestFile>.cs | TestName_WhenCondition_ReturnsOutcome | Unit | Requirement or behavior | Planned | Pending implementation |

Allowed type values: `Unit`, `Component`, `Integration`, `Contract`. This template tracks milestone unit tests first; other types may be listed only when they directly support the milestone gate.

## Red Baseline

Record the expected failing state before implementation.

| Baseline | Date | Agent | Scope | Expected Failures | Result Flag | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| 001 | YYYY-MM-DD | @TestGen | Unit baseline | 0 | RED-EXPECTED | Initial TDD baseline |

## Approved Test Commands

Local commands are allowed for inner-loop validation. Tests that need infrastructure may start isolated local dependency containers, such as PostgreSQL via Docker Compose or Testcontainers. Production milestones require Local Acceptance before user Local Acceptance GREEN: the Ventus app process must run on the host machine, while backend dependencies such as SQL and the supported OIDC backend may run in Docker. A Dockerized Ventus app does not satisfy Local Acceptance; it belongs to Production Docker Acceptance. Production Docker Acceptance commands must run only after user Local Acceptance GREEN and must exercise the real production Docker container or an explicitly approved production-equivalent path.

| Scope | Execution Context | Dependency Containers | Command | Satisfies Final Production Docker Acceptance? | Notes |
| --- | --- | --- | --- | --- | --- |
| Milestone unit tests | Local | None | `dotnet test Ventus.slnx` | No | Inner-loop evidence only |
| Integration tests | Local + dependency containers | e.g. PostgreSQL 16 via Docker Compose or Testcontainers | `dotnet test <target>` | No | Inner-loop evidence only |
| Local Acceptance | Host app process + dependency containers | SQL + supported OIDC backend only | Host-run app command plus local smoke/browser checks | No | The Ventus app must run on the host; dependency containers are allowed; Dockerized app belongs to Production Docker Acceptance |
| Production Docker Acceptance | Production Docker container | Production-shaped stack dependencies | `docker compose <approved-files> up --build <services>` | Yes | Run only after user Local Acceptance GREEN; replace with the approved command for the milestone's Acceptance smoke check |

## Acceptance Ownership And Split

The user is the acceptance owner for both Local Acceptance and Production Docker Acceptance. Agents may provide technical evidence and readiness recommendations, but only the user can mark `User Local Acceptance GREEN` or `User Production Docker Acceptance GREEN`.

Local Acceptance and Production Docker Acceptance must remain separate:
- Separate execution contexts: host-run Ventus app for Local Acceptance; production Docker container for Production Docker Acceptance.
- Separate evidence rows: do not reuse one command, runtime, browser session, screenshot, smoke result, or test run for both gates.
- Separate user sign-offs: Local GREEN does not imply Production Docker GREEN; Production Docker GREEN cannot exist before Local GREEN unless an explicit user exception is recorded.
- Docker evidence collected before user Local Acceptance GREEN is preparation evidence only and must be rerun after Local GREEN for final Production Docker Acceptance.

## Local Acceptance

Complete this section before Production Docker Acceptance for a production milestone. Agents deliver a running local Ventus app process on the host machine, with SQL and the supported OIDC backend running as dependency containers when needed. The user validates the host-run local app and records Local Acceptance GREEN before the production Docker build is tested.

| Scope | Required | Execution Context | Dependency Containers | Command / Check | Latest Result Flag | Evidence | User Acceptance Sign-Off | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Local app build/start | Yes | Host app process | None | TBD | SKIPPED | None | Not signed | Ventus app must run on host, not in Docker |
| SQL dependency running | Yes | Local + dependency containers | SQL | TBD | SKIPPED | None | Not signed | Required before local acceptance |
| Supported OIDC backend running | Yes | Local + dependency containers | OIDC backend | TBD | SKIPPED | None | Not signed | Required before local acceptance |
| Local health/readiness smoke | Yes | Host app process + dependency containers | SQL + OIDC backend only | TBD | SKIPPED | None | Not signed | Proves host-run app can reach Docker-backed dependencies |
| Local auth/sign-in smoke | Yes | Host app process + dependency containers | SQL + OIDC backend only | TBD | SKIPPED | None | Not signed | Browser validates the host-run app, not the Dockerized app |
| Feature-specific local smoke | Yes | Host app process + dependency containers | SQL + OIDC backend only | TBD | SKIPPED | None | Not signed | Required for changed production-facing behavior on the host-run app |
| User Local Acceptance GREEN | Yes | N/A | N/A | Explicit user approval | SKIPPED | None | Not signed | Required before Production Docker Acceptance |

Allowed `User Acceptance Sign-Off` values: `Not signed`, `GREEN`, `Exception`.

## Production Docker Acceptance

Complete this section whenever the milestone touches startup composition, dependency injection, configuration binding, persistence, migrations, auth/session behavior, routing, health/readiness, Docker/container runtime behavior, deployment settings, or milestone closure. Do not start this section until `User Local Acceptance GREEN` is recorded or an explicit user exception exists.

Unit, component, integration, and `WebApplicationFactory` tests do not satisfy this section when they rely on test-only service replacement, stubs, or manual service registration that bypasses production startup.

| Scope | Required | Reason | Execution Context | Command / Check | Latest Result Flag | Evidence | User Acceptance Sign-Off | Accepted Exception | Follow-Up |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Published image build | No | Not production-facing, or not yet assessed | Production Docker container | TBD | SKIPPED | None | Not signed | None | Assess before validation |
| Published container startup | No | Not production-facing, or not yet assessed | Production Docker container | TBD | SKIPPED | None | Not signed | None | Assess before validation |
| Liveness: `GET /health` | No | Not production-facing, or not yet assessed | Production Docker container | TBD | SKIPPED | None | Not signed | None | Assess before validation |
| Readiness: `GET /health/ready` | No | Not production-facing, or not yet assessed | Production Docker container | TBD | SKIPPED | None | Not signed | None | Assess before validation |
| Authenticated flow smoke | No | Not production-facing, or not yet assessed | Production Docker container | TBD | SKIPPED | None | Not signed | None | Assess before validation |
| Feature-specific runtime checks | No | Not production-facing, or not yet assessed | Production Docker container | TBD | SKIPPED | None | Not signed | None | Assess before validation |
| User Production Docker Acceptance GREEN | No | Not production-facing, or not yet assessed | N/A | Explicit user approval | SKIPPED | None | Not signed | None | Required for milestone delivery |

Allowed `Required` values: `Yes`, `No`.
Allowed `Latest Result Flag` values: `GREEN`, `RED-REGRESSION`, `BLOCKED`, `SKIPPED`, `FLAKY`.
Allowed `User Acceptance Sign-Off` values: `Not signed`, `GREEN`, `Exception`.

Validation impact:
- Local Acceptance requires the Ventus app process running on the host with SQL and supported OIDC backend as dependencies, plus user Local Acceptance GREEN, before Production Docker Acceptance starts.
- Agent result flags are evidence, not acceptance.
- Technical GREEN is required when Production Docker Acceptance is required.
- User Production Docker Acceptance GREEN is required for milestone delivery.
- Baseline Production Docker Acceptance smoke covers image build, container startup, `/health`, `/health/ready`, and one authenticated flow when applicable.
- Feature-specific runtime checks must cover every production-facing behavior changed or claimed complete by the chain.
- Host-run local app results satisfy Local Acceptance only; Dockerized app results satisfy Production Docker Acceptance only. Do not substitute one for the other.
- `RED-REGRESSION`, `BLOCKED`, `SKIPPED`, `FLAKY`, deferred, fixture-only validation, missing user Local Acceptance GREEN, or missing user Production Docker Acceptance GREEN blocks `Ready For Validation`.
- An exception is valid only when accepted by the user and recorded with the unvalidated behavior, risk, date, and required follow-up.

## Test Run Log

Record every test run that affects this milestone.

| Run | Date | Agent | Scope | Execution Context | Dependency Containers | Command | Result Flag | Failed | Skipped | User Acceptance Sign-Off | Production Docker Evidence | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 001 | YYYY-MM-DD | @TestGen | Unit baseline | N/A | N/A | Not run by TestGen; scenarios defined | RED-EXPECTED | 0 | 0 | N/A | No | Initial expected baseline |
| 002 | YYYY-MM-DD | @AppDev | Focused unit tests | Local | None | `dotnet test Ventus.slnx` | GREEN | 0 | 0 | N/A | No | Implementation satisfies focused scope |
| 003 | YYYY-MM-DD | @Validate | Local Acceptance evidence | Host app process + dependency containers | SQL + OIDC backend only | Host-run app command plus local smoke/browser checks | GREEN | 0 | 0 | Not signed | No | Awaiting user Local Acceptance GREEN |
| 004 | YYYY-MM-DD | User | User Local Acceptance GREEN | N/A | N/A | User approval | GREEN | 0 | 0 | GREEN | No | Production Docker Acceptance may start |
| 005 | YYYY-MM-DD | @Validate | Production Docker Acceptance evidence | Production Docker container | Production-shaped stack dependencies | `docker compose ... up ...` | GREEN | 0 | 0 | Not signed | Yes | Awaiting user Production Docker Acceptance GREEN |
| 006 | YYYY-MM-DD | User | User Production Docker Acceptance GREEN | N/A | N/A | User approval | GREEN | 0 | 0 | GREEN | Yes | Milestone can be delivered |

## Coverage Gaps

| Gap | Impact | Owner | Required Follow-Up | Status |
| --- | --- | --- | --- | --- |
| Missing edge case | Validation risk | @TestGen | Add unit test | Open |
| Fake-client-only UI/API coverage | Route, JSON, header, DI, or browser runtime regressions may be missed | @TestGen | Add real API client contract tests, API wire-format tests, composition tests, or browser smoke coverage | Open |

## Deferred Or Skipped Tests

| Test / Scope | Reason | Accepted By | Follow-Up | Status |
| --- | --- | --- | --- | --- |
| Test name or scope | Why skipped or deferred | User / @Validate | Required next action | Open |

## Validation Notes

@Validate records the final milestone verdict here or references its validation output.

| Date | Agent | Verdict | Notes |
| --- | --- | --- | --- |
| YYYY-MM-DD | @Validate | Pending | Awaiting Local Acceptance, user GREEN sign-offs, and final Production Docker Acceptance run |

## Related Documents

- Specs:
- ADRs:
- State:
- Changelog:
