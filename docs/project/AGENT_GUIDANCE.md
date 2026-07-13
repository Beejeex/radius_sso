# Agent Guidance: Framework Defaults First

## Status

Accepted

## Date

2026-06-04

## Applies To

All agents that create, review, validate, or test Ventus specifications, ADRs, schemas, implementation plans, and code.

## Rule

Agents MUST prefer native framework, platform, and mature first-party or well-supported library implementations over custom Ventus implementations.

Custom implementation is allowed only when the default implementation cannot satisfy a current, concrete Ventus requirement. The exception must be documented before implementation and must identify the built-in feature being duplicated or bypassed, the specific gap, the risk introduced by custom code, the test plan, and the approving owner.

This rule is especially strict for authentication, authorization, cryptography, session handling, cookies, token validation, request parsing, redirects, CSRF, WAF/edge throttling assumptions, persistence concurrency, logging, metrics, tracing, validation, serialization, and background processing.

## Pre-Implementation Documentation Gate

Before any coding starts, the current milestone or feature MUST have accepted design documents covering the work:

- A SPEC for product behavior, workflows, APIs, acceptance expectations, and non-goals.
- ADRs for architecture, framework, security, compatibility, persistence, deployment, or technology decisions affected by the work.
- A schema document for any persisted data, EF model, migration, index, constraint, or storage change.

This gate applies before source code, tests, migrations, scripts, fixtures, generated files, or implementation plans are written. Do not implement first and backfill documents later.

Accepted means the owning content agent has read the actual file and issued a passing `Document Validation Record`; if the document has a status field, it must be `Accepted`. `Draft`, `In Review` without owner PASS, `Validation Round` pending, stale, missing, or unlinked documents are blockers for implementation, test generation, migrations, generated files, and implementation plans unless the user explicitly records an exception with risk and follow-up.

If the required SPEC, ADR, or schema document is missing, stale, unaccepted, pending validation, or not linked from the active milestone context, agents MUST stop and create, update, or validate the documentation first. Missing or unaccepted documentation is a blocker, not a milestone-close cleanup item.

Implementation may begin only after the docs are accepted and define the behavior and boundaries clearly enough for AppDev, TestGen, Schema, Security, and Validate to work from the same source of truth. Implementation, tests, migrations, scripts, fixtures, generated files, or plans already written before acceptance do not satisfy this gate; they must be reported as a pre-implementation documentation gate bypass until the missing document acceptance is completed or the user explicitly accepts the exception.

Acceptance terminology is strict: Local Acceptance means the Ventus app process runs on the host machine with dependency containers allowed only for backend services. A Dockerized Ventus app run is Production Docker Acceptance evidence, not Local Acceptance evidence.

Local Acceptance and Production Docker Acceptance are separate gates, not two labels for the same validation run. They require separate execution contexts, evidence rows, result flags, and user GREEN sign-offs. Evidence from one gate must never be copied or summarized as satisfying the other.

The user is the acceptance owner for both gates. Agents may deliver evidence and readiness recommendations, but only the user can grant Local Acceptance GREEN or Production Docker Acceptance GREEN. Agents must not grant, infer, backdate, combine, or substitute either sign-off.

Production Docker Acceptance is not eligible to start until user Local Acceptance GREEN is recorded. A Docker build or container smoke run performed before user Local Acceptance GREEN is preparation evidence only; it must not be recorded as final Production Docker Acceptance and must be rerun after Local Acceptance GREEN unless the user explicitly records an exception with risk and follow-up.

## Backend-Ready-For-UI Gate

Production frontend/UI code must not be created for a backend capability until that backend capability is implemented and validated for UI consumption.

Backend-ready-for-UI evidence requires:

- Accepted SPEC/ADR/schema inputs for the capability.
- Implemented backend service/endpoint and any persistence behavior required by the UI slice.
- GREEN backend/unit/integration tests for the capability.
- API/client contract evidence for route, status, headers, JSON shape, auth path, and errors.
- Security/auth-path classification for the UI surface.
- A validation result or handoff that explicitly records the capability as `Backend Ready For UI`.

Planning, UX notes, test planning, and harness setup may happen earlier, but production React pages/components, frontend API-client wiring, browser workflow implementation, and UI behavior code must wait for backend-ready-for-UI evidence unless the user explicitly records an exception with risk and follow-up.

## UI Slice Green Loop

Frontend/UI implementation must move one user-visible slice at a time. A slice is a route, page, component, form, dialog, table, navigation path, or workflow that can be tested independently against the validated backend contract.

For each UI slice:

1. TestGen defines the required component, API-client, browser/E2E, loading/empty/error-state, and auth-path evidence.
2. The owning implementation agent changes only that slice.
3. The slice reaches GREEN for its planned evidence before the next UI slice starts.
4. Validate records the slice result and product traceability: `backend-ready evidence -> frontend consumer -> UI workflow -> tests -> validation`.

Large frontend batches are not allowed by default. If multiple UI slices are changed before the prior slice is GREEN, Validate must fail unless the user explicitly accepted the batching risk and the tracking document records the risk and follow-up.

## Required Decision Flow

1. Identify the native framework or platform feature that normally owns the behavior.
2. Prefer configuration and documented extension points before creating replacement logic.
3. If the built-in feature appears insufficient, prove the gap against the current requirement, not a speculative roadmap scenario.
4. Keep any approved custom code isolated, small, testable, and documented.
5. Add tests for the required behavior and, where useful, architecture tests that prevent accidental reintroduction of unnecessary custom code.

## Custom Implementation Exception Record

Any spec, ADR, schema, or test plan that requires custom implementation where a default exists MUST include an exception record:

| Field | Required Content |
| --- | --- |
| Requirement | The current requirement that cannot be met by the default feature |
| Default considered | The framework, platform, or library feature normally responsible |
| Gap | The exact behavior the default cannot provide |
| Extension points considered | Configuration, events, callbacks, handlers, policies, filters, or adapters evaluated before replacement |
| Custom boundary | The smallest component that must be custom |
| Risk | Security, reliability, compatibility, operability, and maintenance risk added |
| Tests | Unit, integration, contract, and negative architecture tests required |
| Owner approval | Design and any required domain reviewer, such as Security or Protocol |
| Removal trigger | Condition that would allow replacing the custom code with the default later |

If this record cannot be completed, the custom implementation is not approved.

## Agent Responsibilities

### @Design / Architect

- Ensure the SPEC, required ADRs, and any schema document are accepted with passing owner validation records before approving implementation work.
- Compare against the native framework option first in every ADR.
- Do not approve custom infrastructure or protocol logic without a completed exception record.
- Scope compatibility requirements to the external boundary that requires them.
- Prefer boring, explicit, auditable framework composition over clever abstractions.

### @Security

- Treat unnecessary custom security-sensitive code as a finding by default.
- Require default framework behavior for auth, cookies, redirects, token validation, CSRF, CORS, cryptography, and authorization unless an exception is approved.
- Verify custom code does not mix authentication, authorization, claims mapping, session handling, or business logic.

### @AppDev

- Before coding, verify the active SPEC, ADRs, and schema docs are accepted, have passing owner validation records, and cover the requested work.
- Before frontend/UI coding, verify the consumed backend/API capability is marked `Backend Ready For UI`.
- Implement frontend/UI work one validated slice at a time; do not batch additional UI slices before the current slice is GREEN unless the user accepted the batching risk.
- Implement framework-native solutions unless the active spec or ADR contains an approved exception.
- Use documented extension points before introducing new services, middleware, handlers, stores, or parsers.
- If a spec appears to require custom code where a default exists but lacks an exception record, stop and raise a design issue instead of implementing the custom path.
- If production code already exists for work whose required docs are still Draft, pending validation, stale, or missing, stop and escalate the skipped gate instead of continuing implementation.

### @TestGen

- Do not write behavior-changing tests or accepted test plans before the active SPEC and relevant ADR/schema docs are accepted and define the expected behavior.
- Record backend-ready-for-UI evidence before planning production UI implementation tests.
- Split frontend/UI evidence by route, page, component, form, dialog, table, navigation path, or workflow slice, and keep later slices blocked until the prior slice is GREEN unless the user accepted batching risk.
- Test externally required behavior and framework composition, not unnecessary custom internals.
- Do not create tests that require custom implementations unless the corresponding exception is approved.
- Do not rely on fake-client component tests as the only coverage for UI/API behavior. Fakes are allowed for component state tests, but they do not prove routes, JSON wire format, auth headers, DI registration, or browser runtime behavior.
- For every OpenAPI-generated TypeScript API client or equivalent frontend API wrapper, require contract tests that exercise the real client routes, headers, status handling, and DTO shapes against a representative HTTP boundary or test host.
- For API endpoints consumed by web clients or compatibility clients, require wire-format tests with representative JSON payloads so property names, required fields, enum/string values, and pagination shapes cannot drift silently.
- For production-facing web UI milestones, require a browser boot/render smoke path that loads the real React/Vite app and verifies key pages render without static asset, routing, API-client, or console-error failures. If no browser harness exists, record the missing harness as a blocking validation gap unless the user explicitly accepts the risk.
- Add composition or registration tests when pages/components inject new services, so missing DI registrations fail before Local Acceptance.
- Add negative architecture tests where useful, such as ensuring a custom protocol handler or store is not registered.

### @Challenger / @Validate

- Challenge every custom implementation that duplicates default framework behavior.
- Fail validation when implementation, tests, migrations, scripts, fixtures, generated files, or implementation plans began before required SPEC, ADR, or schema documents were accepted.
- Fail validation when production frontend/UI code is created before backend-ready-for-UI evidence exists, or when UI slices are batched without a user exception before the prior slice is GREEN.
- Fail validation when specs, ADRs, schemas, tests, or code require custom default-replacement logic without an approved exception.
- Confirm the implementation stayed inside the approved boundary and did not spread hidden behavior across controllers, middleware, or services.

### @Protocol / @Compat

- Allow custom code only at the compatibility boundary required by external clients or wire protocols.
- Do not let compatibility constraints leak into unrelated product architecture.
- For Jellyfin compatibility, custom `MediaBrowser` header parsing and a dedicated authentication handler may be justified; this does not justify hand-written browser OIDC, cookie, session, or authorization flow replacement.

### @Schema / @SchemaWriter

- Write, validate, and accept the schema document before EF model, migration, index, constraint, or storage code is created.
- Do not introduce tables, stores, or persisted records for behavior owned by framework middleware unless an exception is approved.
- Keep schema documents focused on durable Ventus product data, not transient framework protocol state.

## Authentication-Specific Application

Browser OIDC should use native ASP.NET Core authentication middleware whenever possible:

- `AddAuthentication()`
- named schemes
- `AddCookie()`
- `AddOpenIdConnect()`
- `UseAuthentication()`
- `UseAuthorization()`
- `[Authorize]`, policies, requirements, roles, and claims

Custom code may still be appropriate for the Jellyfin-compatible `Authorization: MediaBrowser ...` header because that wire format is not a standard bearer-token format. That custom code must remain isolated in a dedicated authentication handler and must not replace browser OIDC or cookie behavior.
