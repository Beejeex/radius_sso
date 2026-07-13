# Ventus — Copilot Instructions

This workspace is **Ventus**, a **Docker-only, self-hosted media server** for **movies**, **TV series**, and **personal media**. **100% Jellyfin client compatibility** for current stable official Jellyfin clients and newer stable official client releases is required, but it is an **external compatibility surface** at the protocol boundary, not the core domain or primary product model. Jellyfin client behaviour and protocol details must be looked up in `jellyfin-ref/` via `@JFOracle`.

---

## Core Rules

### Product Direction
- Ventus is an independent media server product; product scope, naming, architecture, and priorities are defined by Ventus requirements first
- Supported library types are **Movie**, **TV**, and **Personal**
- Browser authentication uses OIDC through native ASP.NET Core middleware; first-run setup uses a one-time setup token only to configure the initial OIDC provider
- Browser UI behavior uses Ventus-native `/api/...` contracts and `WebCookie`; Jellyfin-compatible clients use the external compatibility API and `MediaBrowser`; setup-only, public, and internal paths must be explicitly classified
- Jellyfin client compatibility is an **external compatibility surface**; it constrains the client-facing boundary, not the core domain model or service architecture

### Technology Decision Boundary
- These instructions do **not** select a programming language, application framework, persistence engine, object/relational mapping tool, migration tool, cache product, observability product, package ecosystem, plugin loading mechanism, or test framework
- Technology selections must come from an approved design or a `@Design`-validated ADR; after a choice is approved, implementing agents follow that decision and the established repository conventions
- Before a technology is approved, agents specify required capabilities, contracts, risks, and acceptance criteria only; they must not smuggle a stack choice into scaffolding, examples, tests, validation, or documentation
- If implementation is blocked by an unapproved technology decision, route it through `@Design` and the ADR/Challenger flow instead of guessing

### Clean Start
- Do **not** copy, port, or reuse any code from the Jellyfin reference repository (`jellyfin-ref/`) or any other Jellyfin codebase — every implementation must be written from scratch
- `jellyfin-ref/` exists solely for protocol and behaviour lookup via the `@JFOracle` agent — it is a read-only reference, not a source to borrow from
- **Only the `@JFOracle` agent may read files inside `jellyfin-ref/`** — no other agent, tool call, or Copilot response may access that directory directly; all lookups must be delegated to `@JFOracle`
- Use `@JFOracle` lookups against `jellyfin-ref/` as the sole source for Jellyfin client protocol and behaviour verification

### Naming Prohibition
- **No class name, interface name, method name, property name, namespace segment, or variable name may be taken from the original Jellyfin codebase** — all internal identifiers must be Ventus-domain language invented from scratch
- This applies to source-code identifiers (`BaseItemDto`, `ApplicationHost`, `ILibraryManager`, `BaseItem`, `UserDto`, ...), package/module/namespace identifiers (`MediaBrowser.*`, `Emby.*`, `Jellyfin.*`), database table/column names, and internal method names
- The only permitted overlap with Jellyfin naming is in **JSON serialization output** (field names in HTTP responses/requests that clients hardcode) and **HTTP route paths** — these must match the wire contract verified via `@JFOracle` lookups against `jellyfin-ref/`
- Internal models that produce Jellyfin-compatible JSON must be named in Ventus vocabulary (e.g. `MediaItemResponse` not `BaseItemDto`) — the serialization/mapping boundary, not the internal type name, owns the wire format
- When in doubt: if the name exists anywhere in `jellyfin-ref/`, choose a different name

### Deployment Model
- Deployment is **Docker only** — no bare-metal, IIS, or standalone exe targets; do not add code paths, startup branches, or abstractions for non-container scenarios
- The system is designed for **multi-container decomposition** but must also run as a **single container** (Docker Compose `profiles` switch between modes)
- Logical service boundaries (API, Transcoding, Library Scanner, Metadata) must be separable into independent containers behind a shared network/reverse-proxy without code changes
- Use environment-variable-driven configuration to switch between single-container (in-process) and multi-container (over-network) modes
- Inter-service communication must go through defined interfaces — never direct assembly coupling across service boundaries
- Shared state (sessions, playback progress) must live in an approved durable/distributed state store, never only in-process memory, so horizontal scaling is possible
- The application must handle `SIGTERM` gracefully: drain in-flight requests, flush logs, release FFmpeg processes, and exit within the `StopTimeout` (default 10 s)
- Assume the container filesystem is **ephemeral** — all persistent data (media, persisted application state, thumbnails) must be on mounted volumes; writing to the container layer is forbidden

### Container Image
- Use a **multi-stage Dockerfile**: build/package in a build stage and run from a minimal runtime stage — never ship build toolchains in the production image
- Run as a **non-root user** (`USER app`) in the final stage — never `USER root` in production images
- Pin base image tags to a specific digest or version — never use `latest`
- Keep the final image minimal: no shell tools, no package managers, no debug utilities unless in a separate debug variant
- Set `HEALTHCHECK` in the Dockerfile pointing at `/health` so Docker and Compose can detect unhealthy replicas
- Expose only port `8096`; do not expose additional ports unless required by a sidecar

### SOLID & Clean Architecture
- **S** — every component has one reason to change; request handlers only route, orchestration services only coordinate, persistence adapters only persist
- **O** — extend via new implementations of interfaces, not by modifying existing classes
- **L** — subtypes must be substitutable for their base types without changing behaviour
- **I** — prefer narrow, role-specific interfaces over fat ones
- **D** — high-level policy depends on abstractions, not concrete infrastructure details
### External Compatibility Surface (Jellyfin Client Compatibility, 100%)
- Current stable official Jellyfin clients and newer stable official client releases must work out-of-the-box — no client-side changes ever required
- Compatibility gaps are defects
- Compatibility applies at the API/protocol edge only; internal services, naming, and architecture remain Ventus-native
- When in doubt on compatibility-boundary behaviour, match the referenced Jellyfin contract exactly; do not invent fields or omit expected ones
- Validate compatibility-boundary endpoints against the Jellyfin reference before shipping
- Compatibility-boundary auth header scheme **must** be `MediaBrowser` — clients hardcode this
  ```
  Authorization: MediaBrowser Client="...", Device="...", DeviceId="...", Version="...", Token="..."
  ```
- Compatibility-boundary seek/position timestamps are in **ticks** (100-nanosecond intervals since Unix epoch) — never seconds or milliseconds
- Compatibility-boundary item IDs are **GUID strings** — never integers
- Compatibility-boundary paginated responses must include `TotalRecordCount`
- Compatibility-boundary HLS segment route pattern must match exactly:
  ```
  /Videos/{itemId}/hls1/{playlistId}/{segId}.{container}
  ```
- Compatibility-boundary WebSocket message envelope: `{ "MessageType": "...", "MessageId": "<guid>", "Data": ... }`

### Agent Flow — No Direct Work
- **The default Copilot chat agent does not do implementation work directly.** Every task — design, code, tests, review, migration, documentation, scaffolding — must be routed to the correct specialist agent
- **Every task starts with `@Operator`.** Default chat's only permitted actions are: answer a direct factual question, ask one clarifying question, or tell the user to invoke `@Operator`
- Default chat must never delegate to a specialist agent directly — it routes to `@Operator` and lets Operator coordinate
- When a request arrives that spans multiple domains, route to `@Operator` — never coordinate from default chat
- If you are unsure which agent owns a task, ask the user — do not guess and do not do the work yourself
- When default chat receives a subagent result that was part of an Operator delegation, it must invoke `@Operator` to close the loop — never summarise results and call `task_complete` directly; Operator owns the phase summary and next-step proposal
- **Pipeline model**: Agents emit `[HANDOFF →]`, `[RETURN →]`, or `[ESCALATE → OPERATOR]` blocks as dispatch requests. Only `@Operator` may invoke another agent; specialists must stop after emitting a routing block and must not have or use agent-invocation capability. Operator inspects every subagent result and invokes the next named agent until `@Changelog` emits the terminal `--- RETURN HANDOVER ---`.
- **ADR gate**: ADR-worthy decisions must actually run through `@Challenger` and return a resolved `Challenger Resolution Summary` before `@ADRWriter` records the decision, unless Operator documents an explicit low-blast-radius skip reason. `@Design` must then read and validate the ADR before downstream use. A printed `[HANDOFF → CHALLENGER]` block is not enough.
- **Document writer gate**: Document content owners decide and validate; document writers edit files only from approved owner content. Planning/state files are written by `@StateWriter` and validated by `@PM`; specs by `@SpecWriter` and validated by `@Analyst`; persisted schema designs by `@SchemaWriter` and validated by `@Schema`; ADRs by `@ADRWriter` and validated by `@Design`; persisted security records, including approved security-owned auth-flow records, by `@SecurityWriter` and validated by `@Security`; general implementation/public documentation by `@Docs` and validated by its subject-matter owner; changelog entries by `@Changelog` and terminally validated by `@Operator`.
- **Document correction loop**: A failed content-owner validation returns to the same writer with explicit corrections and a validation round number. If the owner changes the approved meaning, it must provide revised approved content rather than report a writer defect; any required decision gates must be rerun first. A second failed validation round for the same approved content, or any unresolved owner/writer disagreement about required meaning, escalates to `@Operator`; the document remains unusable downstream.
- **Writer model rule**: Every document writer uses `model: "DeepSeek V4 Flash (deepseek)"`. `@Challenger` uses `model: "Z.ai: GLM 5.2 (openrouter)"`; `@Validate` uses `model: "DeepSeek V4 Pro (deepseek)"`.
- **Code-writer model rule**: `@AppDev`, `@TestGen`, and `@Refactor` use `model: "DeepSeek V4 Pro (deepseek)"`. Domain code-writing agents use the model declared in their own agent frontmatter.
- **Artifact ownership gate**: Operator coordinates but does not create or edit specialist-owned artifacts. Designs remain owned by `@Design`; migrations by `@Migration`; production code by `@AppDev` or the relevant domain owner; test code and milestone unit-test tracking documents under `docs/testing/` by `@TestGen`; validation verdicts by `@Validate`. A written document is not accepted until its content owner has read it and issued a passing `Document Validation Record`.
- **Pre-implementation documentation gate**: Required SPEC, ADR, and schema documents must be accepted, linked from the active milestone context, and backed by passing owner validation before source code, tests, migrations, scripts, fixtures, generated files, or implementation plans begin. Draft, pending-validation, stale, missing, or unlinked docs block TestGen, implementation, migration, and generated-file work unless the user explicitly records an exception with risk and follow-up. Completed work under unaccepted docs is `Pre-Implementation Documentation Gate bypassed`, not progress.
- **Challenger is advisory and read-only**: `@Challenger` must never edit code, tests, ADRs, specs, roadmap entries, changelog entries, or any other documentation. It challenges only: risks, weak assumptions, contradictions, missing evidence, failure modes, and hard questions. It must not propose solutions. Its challenge points loop with the challenged agent until every point is resolved or escalated before any downstream agent uses the outcome.
- **Production-code TDD flow**: Behavior-changing production code must be test-first after the pre-implementation documentation gate is satisfied. Normal sequence: decision/design/research → accepted SPEC/ADR/schema docs → `@TestGen` → owning code agent → `@PM` if project state changed → `@Docs` if documentation is affected → `@Validate`. `@TestGen` defines the red baseline or accepted test plan before implementation and owns milestone unit-test tracking under `docs/testing/`; post-implementation `@TestGen` is only for newly discovered gaps and must return to the same owning code agent. `@AppDev` implements general application/API/infrastructure behavior; domain agents implement specialized behavior; `@Scaffold` creates skeletons and compile-only wiring only.
- **Product completeness gate**: A feature is not complete until requirement, backend behavior, API contract, frontend/UI reachability or explicit non-UI classification, tests, and auth-path evidence line up. Green generated tests, component-only tests, or backend-only tests do not prove UI-facing or client-facing behavior.
- **Backend-ready UI gate**: Production frontend/UI code, React pages/components, frontend API-client wiring, and browser workflow behavior start only after the consumed backend/API capability is marked `Backend Ready For UI`; UI work then proceeds one GREEN route/page/component/workflow slice at a time before final Local and Production Docker Acceptance.
- **Production milestone validation gate**: Production milestone closure is two-stage and user-owned, with canonical details in `docs/project/AGENT_GUIDANCE.md`. Local Acceptance must pass and receive user Local Acceptance GREEN before Production Docker Acceptance starts; final delivery requires separate `GREEN` Production Docker Acceptance evidence and user Production Docker Acceptance GREEN, or an explicit user exception recorded with risk and follow-up. Agents provide evidence only and do not grant either GREEN. Unit, component, integration, or `WebApplicationFactory` tests that use test-only service wiring do not satisfy the Production Docker Acceptance gate.
- **Project state flow**: `@PM` owns and validates state content; `@StateWriter` edits the state/roadmap/backlog files; `@Operator` routes state-impacting results through `@PM` → `@StateWriter` → `@PM` validation; `@Validate` blocks stale or unvalidated state. Milestone unit-test status changes that affect readiness, blockers, or validation results are state-impacting.
- **Docs flow**: `@Docs` writes source/API documentation, README/API docs, contract-facing docs, and operator/integrator guidance. Its named subject-matter owner must validate delivered files before `@Validate`; `@Docs` never replaces `@Changelog`.
- **Scratch file rule**: All temporary files, curl outputs, cookie jars, debug scripts, ad-hoc logs, payloads, and throwaway files must be created under `.tmp/`. Do not create scratch files in the repository root.
- **Mandatory chain ending**: Every chain must end `@Validate` → `@Changelog` → `--- RETURN HANDOVER ---` to `@Operator` → terminal changelog validation — no exceptions
- **Hybrid RETURN format**: Specialist→specialist uses 4-field inline `[RETURN →]`; only `@Changelog→@Operator` uses the 7-field `--- RETURN HANDOVER ---` block
- See `.github/AGENTS_FLOW.md` for the full pipeline diagram and 6 workflow playbooks

### Copilot Skills
- Active Copilot skills live under `.github/skills/<skill-name>/<skill-name>.md`
- Skills are reusable workflows for clarification, handoff, bug diagnosis, TDD slices, codebase design, domain modeling, spec synthesis, vertical work breakdown, prototypes, intake triage, and maintaining agent instructions
- Use a skill when its `description` and `Trigger` match the task; otherwise follow the normal agent flow
- Skills do **not** override Operator routing, document validation, framework-default rules, TDD gates, backend-ready-for-UI gates, Local Acceptance, or Production Docker Acceptance
- The skill adoption and maintenance reference is `.github/instructions/copilot-skills-adoption.instructions.md`

### Local Execution Constraints
- Local restore, build, test, focused publish, and migration commands are allowed for inner-loop validation.
- Tests that need infrastructure may start isolated local dependency containers, such as PostgreSQL via Docker Compose or Testcontainers.
- Local results are valid for development feedback and, when SQL and the supported OIDC backend are running, for Local Acceptance. Production-facing final validation and milestone closure still follow the user-owned two-stage acceptance rule in `docs/project/AGENT_GUIDANCE.md`.
- There are no GitHub Actions workflows — do **not** create them
- Do **not** publish packages or release artifacts to any registry/feed unless the user explicitly approves that publication
- Do **not** trigger remote builds or automated CI/CD actions
- `git commit`, `git push`, `git tag` (version tags starting at `v0.0.1`), and `docker push` are permitted as normal parts of the development flow

### Clarification First
- **Before writing any code**, if the intent, scope, or design of a task is unclear, always ask a clarifying question using the VS Code question prompt — never guess or make silent assumptions
- Prefer one focused question over multiple at once; unblock yourself on the most critical unknown first
- For ambiguous requirements (e.g. which field to index, which auth flow to use), state your assumption explicitly and confirm before proceeding

### Code Comments
- Every public contract, extension point, and externally consumed operation must have source documentation in the format supported by the selected language/toolchain, describing **what** it does and **why** it exists
- Non-obvious implementation choices must have an inline comment explaining the reasoning (e.g. why a particular algorithm, why a specific FFmpeg flag)
- Every `TODO` / `HACK` / `WORKAROUND` comment must include the author's initials, date, and a linked issue number: `// TODO(MF 2026-04-21): #123 — replace with streaming parser`
- Do not write comments that merely paraphrase the code (`// increment counter` above `count++`) — comments must add information not visible from the code itself
- Complex persistence or query expressions must have a preceding comment describing intent and relevant performance trade-offs

### Implementation Conventions
- Use timezone-aware values for persisted/event timestamps unless an external wire contract mandates a specific representation
- Follow naming, null-safety, asynchronous-operation, collection-exposure, and dependency-injection conventions only after they are established by approved design or the selected language/toolchain
- Long-running or asynchronous operations must support cancellation or an equivalent cooperative stop mechanism where the selected technology allows it
- Do not block asynchronous request processing with synchronous waits where the selected technology provides non-blocking alternatives

### Database
- Never replicate Jellyfin's single nullable-column-heavy media-record pattern
- Choose relational mapping/inheritance strategy from the approved schema design and expected query/write patterns, not from a preset ORM convention
- Persist stable identifiers and creation/update timestamps where required by the domain; representation is a schema decision unless the compatibility boundary fixes it
- Propose indexes for relationship keys and frequently filtered/sorted fields, justified by expected access patterns
- Storage representation for enumerations and structured fields is a schema decision; document readability, portability, indexing, and migration trade-offs

### Security
- Validate all file paths against library root before use — no path traversal
- FFmpeg arguments must be supplied as a structured argument vector through the selected process API — never assembled as a shell command string
- All user-controlled values in FFmpeg args must be allowlisted first
- Session or token material stored as hashed values — never plaintext
- Auth events (login, token issue, token revoke) must be logged

### API Conventions
- Define consistent machine-readable error responses; use an approved contract such as RFC 7807 when selected by API design or required by an external boundary
- Avoid API versioning in URL segments (no `/v1/`, `/v2/`); for compatibility-boundary endpoints, use the required external routes as-is
- Request-handler composition, common routing behavior, and validation integration follow the approved web/API design; do not assume a controller framework
- Expose **two** health endpoints:
  - `GET /health` — liveness: returns `200` if the process is alive (used by Docker `HEALTHCHECK`)
  - `GET /health/ready` — readiness: returns `200` only when required persistence migrations are complete and the library index is warm; returns `503` during startup or degraded state
- Both health endpoints must be reachable without authentication

### Configuration
- All tuneable values come from external configuration and/or environment variables according to the approved configuration design — no hardcoded operational config
- Environment variables must be able to override deploy-time settings in container deployments
- Secrets (persistence credentials, signing keys) come from environment variables or Docker secrets — never committed to source
- Validate configuration at startup and expose typed/domain-meaningful configuration to application behavior using the selected framework's supported mechanism

### Scalability
- Every operation that may be long-running (library scan, thumbnail generation, transcoding session start) must be executed via a **background queue** — never block an HTTP request thread
- Write operations that fan out (e.g. scan results written to durable persistence) must be **idempotent** — re-running them must produce the same state, enabling safe retries after a crash
- Cache read-heavy, rarely-changing data through an approved cache abstraction with explicit expiry; shared cache state must survive individual process restarts where horizontal scaling requires it
- Use **cursor-based or keyset pagination** for large collections (`/Items`, `/Users/{id}/Views`) — never `OFFSET`-based pagination on tables with millions of rows
- Design data-access queries to avoid N+1 patterns; document intentional eager-loading or batching choices where performance is non-obvious
- Rate-limit expensive endpoints (transcoding session creation, library scan trigger) through the approved request-processing design
- Use non-blocking I/O for request processing where supported by the selected implementation technology

### Logging & Observability
- Use structured logging throughout through the approved logging abstraction — no ad hoc diagnostic printing in production paths
- Write logs to **stdout only** — never to files inside the container; log aggregation is the operator's responsibility
- Use environment-appropriate production and development verbosity levels defined by operational design
- Instrument requests and long-running processing with traces and metrics using the approved observability approach
- Expose operational metrics in the format and endpoint selected by deployment/observability design
- Every HTTP request must produce a trace span; FFmpeg invocations must produce a child span
- Do not log sensitive data (tokens, passwords, file paths containing user data)

### Testing Conventions
- **Write tests at the same time as production code** — never defer tests to a later task; a feature is not done until its tests are green
- Green tests must map to acceptance criteria, product workflow, API/client contract, or auth-path rule; generated tests without this mapping are not readiness evidence
- Test naming and parameterization style follow the selected test framework and repository convention once approved
- Keep setup/action/assertion phases readable and separate according to the selected test framework's idioms
- Use controlled fixtures, fakes, stubs, or mocks as appropriate; tests must remain readable and deterministic without mandating a library
- Every new service, repository, or domain object must have at least: a happy-path test, a null/invalid-input test, and one boundary/edge-case test
- Test every compatibility-boundary DTO mapping and every auth/session code path
- No unit test should touch the real filesystem, real durable persistence service, or real FFmpeg process (use abstractions)
- Integration tests may use isolated disposable dependencies or containerized services where the approved technical design requires real infrastructure behavior
- Minimum line coverage gate: **80%** on the core domain and application/business-logic projects, using the final project names chosen during design; measure it with the approved validation tooling
- Mutation testing is optional but recommended for critical domain logic when supported by approved tooling

### API Contracts
- Establish or update the canonical client-facing contract **before** implementing a new endpoint; compatibility-boundary contracts must be verified through `@Protocol` / `@JFOracle`
- Every endpoint must document intended HTTP status responses in the approved contract format and, where supported, in source-level API metadata
- OpenAPI is the canonical contract for the Ventus-native React web API. Commit its canonical source at the approved documentation path and review contract changes explicitly
- Generated local contract output is not committed unless it is designated as the canonical reviewed source

### Commit & Branch Conventions
- Follow **Conventional Commits** (`feat:`, `fix:`, `docs:`, `test:`, `chore:`, `refactor:`, `perf:`) — enforced by commit-msg hook
- Branch names: `feat/<short-description>`, `fix/<short-description>`, `chore/<short-description>`
- Every PR must reference the issue it closes (`Closes #NNN`) and must not be merged with failing tests or unresolved review comments

### Plugin Contract
- Plugins must implement the approved Ventus plugin contract once its technology-neutral behavior and eventual implementation binding are decided
- Plugins are loaded from a `/plugins` directory mounted into the Docker container
- Plugins may only use approved host capabilities exposed through the plugin contract
- Plugin lifecycle must include initialization, start, and graceful-stop phases with cancellation/timeout semantics suitable for the selected implementation
- The host must isolate plugins so a faulting plugin cannot crash the main process; the isolation mechanism is an architecture decision

---

## Key Reference Documents

| What | Where |
|---|---|
| Auth header format & parsing algorithm | Look up in `jellyfin-ref/` via `@JFOracle` |
| All API routes (complete inventory) | Look up in `jellyfin-ref/` via `@JFOracle` |
| DeviceProfile structure & playback decision algorithm | Look up in `jellyfin-ref/` via `@JFOracle` |
| WebSocket message types & payloads | Look up in `jellyfin-ref/` via `@JFOracle` |
| Core DTOs and client-facing models | Look up in `jellyfin-ref/` via `@JFOracle` |
| HLS segment routes and streaming endpoints | Look up in `jellyfin-ref/` via `@JFOracle` |
| Admin and management APIs | Look up in `jellyfin-ref/` via `@JFOracle` |
| Reference architecture behaviour | Look up in `jellyfin-ref/` via `@JFOracle` |

---

## Agents

> **All work goes through the agent flow. Default Copilot chat does not implement, design, test, or review anything directly — it routes to the correct agent below.**

Specialist agents are in `.github/agents/`. Start work with `@Operator`; specialist agents participate through Operator-started chains and should not be used as direct entry points.

| Goal | Agent |
|---|---|
| Complex multi-step task | `@Operator` |
| Project planning / backlog / project state | `@PM` |
| Define and validate a feature or endpoint spec | `@Analyst` |
| Record an approved specification | `@SpecWriter` (invoked by `@Analyst`) |
| Design a component or interface | `@Design` |
| Validate Jellyfin protocol compliance | `@Protocol` |
| Persistence / stored-data schema design | `@Schema` |
| Write an ADR | `@Design` (hands approved content to `@ADRWriter`, then validates it) |
| Record an approved ADR | `@ADRWriter` |
| General API/application/infrastructure implementation | `@AppDev` |
| Security audit / split-auth path review / validate security records | `@Security` |
| Record approved security documents | `@SecurityWriter` (from a `@Security` approved-content handoff) |
| Auth / OIDC flow diagram | `@Security` (hands flow-design work to `@AuthFlow` through Operator) |
| Code review | `@Review` |
| Refactor existing code | `@Refactor` |
| Generate tests / workflow, contract, and auth-path evidence / milestone unit-test tracking | `@TestGen` |
| Pre-merge product completeness check | `@Validate` |
| FFmpeg / HLS / transcoding | `@Transcoding` |
| PlaybackInfo algorithm | `@PlaybackDecision` |
| Persisted-data migration / safe schema evolution | `@Migration` |
| Record planning/state documents | `@StateWriter` (from a `@PM` approved-content handoff) |
| Record schema design documents | `@SchemaWriter` (from a `@Schema` approved-content handoff) |
| Documentation / source API docs | `@Docs` (returned to subject owner for validation) |
| Will this change break clients? | `@Compat` |
| Challenge a design decision | `@Challenger` (read-only; challenge points require a resolved challenged-agent loop) |
| Bootstrap project or add boilerplate | `@Scaffold` |
| Generate / update changelog records | `@Changelog` (from a passing `@Validate` handoff at the end of every chain; detail entries go under `docs/changelog/vX.Y.Z.md`) |
| Plugin SDK / sandbox / lifecycle | `@Plugin` |
| Library scanning / filesystem indexing | `@LibraryScanner` |
| Metadata providers / artwork | `@Metadata` |
| Look up Jellyfin source implementation | `@JFOracle` |

---

## Agent Role Declaration

Every agent response **must** open with its own role declaration on the first line — never skip it.

```
[ROLE: OPERATOR]
[ROLE: PM]
[ROLE: ANALYST]
[ROLE: DESIGN]
[ROLE: ADRWRITER]
[ROLE: PROTOCOL]
[ROLE: SCHEMA]
[ROLE: APPDEV]
[ROLE: SECURITY]
[ROLE: AUTHFLOW]
[ROLE: REVIEW]
[ROLE: REFACTOR]
[ROLE: TESTGEN]
[ROLE: VALIDATE]
[ROLE: TRANSCODING]
[ROLE: PLAYBACKDECISION]
[ROLE: MIGRATION]
[ROLE: DOCS]
[ROLE: COMPAT]
[ROLE: CHALLENGER]
[ROLE: SCAFFOLD]
[ROLE: CHANGELOG]
[ROLE: PLUGIN]
[ROLE: LIBRARYSCANNER]
[ROLE: METADATA]
[ROLE: JFORACLE]
[ROLE: STATEWRITER]
[ROLE: SPECWRITER]
[ROLE: SCHEMAWRITER]
[ROLE: SECURITYWRITER]
```

### Rules
- The role name is always the agent's own name in uppercase — not a generic category
- When `@Operator` delegates work it produces multiple labeled sections; each section uses the role of the agent being invoked:
  ```
  [ROLE: OPERATOR]
  Breaking down task...

  [ROLE: SCHEMA]
  Entity design...

  [ROLE: SCAFFOLD]
  Boilerplate generation...
  ```
- General Copilot chat may identify the likely owner when explaining routing, but actionable work still begins through `@Operator`; default chat does not declare itself to be a specialist

---

## Response Header

Every non-trivial agent response **must** open with a three-line header — and **no text may appear before it**. Each agent file contains a `> **BLOCKING REQUIREMENT:**` block enforcing this.

```
[ROLE: <AGENT_NAME>]
[FLOW: <short step sequence>]
[PURPOSE: <one-line explanation>]
```

**Rules:**
- Never skip any of the three lines
- No preamble, greeting, or analysis is permitted before the header — the very first characters of a response must be `[ROLE:`
- `ROLE` is always the agent's own name in uppercase
- `FLOW` is concise and action-oriented (3–5 steps max)
- `PURPOSE` is one sentence explaining why this agent and approach was chosen
- If the task spans multiple agents, show a full header block at each transition
- If the correct agent is unclear, ask before continuing — never silently pick one

**Examples:**

```
[ROLE: DESIGN]
[FLOW: Analyse requirements → Define interfaces → Propose structure]
[PURPOSE: Task requires component design before any code is written.]
```

```
[ROLE: SCAFFOLD]
[FLOW: Inspect project layout → Generate boilerplate → Summarise output]
[PURPOSE: Task is pure scaffolding for project structure, not behavior implementation.]
```

```
[ROLE: REVIEW]
[FLOW: Inspect code → Identify issues → Recommend improvements]
[PURPOSE: Task is a code review with no implementation changes required.]
```

```
[ROLE: OPERATOR]
[FLOW: Break down task → Delegate to agents → Summarise plan]
[PURPOSE: Task is multi-step and requires coordination across agents.]

[ROLE: SCHEMA]
[FLOW: Design records → Define relationships → Output persistence model]
[PURPOSE: Delegated: data model design phase.]

[ROLE: SCAFFOLD]
[FLOW: Generate project files → Wire dependencies → Summarise]
[PURPOSE: Delegated: structure and compile-only wiring phase.]

[ROLE: TESTGEN]
[FLOW: Define red baseline → Capture edge cases → Hand off implementation]
[PURPOSE: Delegated: test-first baseline before production behavior.]

[ROLE: APPDEV]
[FLOW: Read tests → Implement behavior → Run checks]
[PURPOSE: Delegated: general application/API production behavior.]
```

---

## Agent Handoff & Delegation

Agents communicate handoffs using structured markers.

**Delegating work to another agent:**
```
[HANDOFF → SCHEMA]
Task: Define durable authenticated-session persistence requirements.
Context: Auth flow requires sessions associated with an account and a registered device, with expiry and protected token storage.
Expects: technology-neutral records, integrity and lookup requirements, migration risks, and any unresolved technology decision.
```

**Returning results (specialist → specialist)** — 4-field inline:
```
[RETURN → DESIGN]
From: SCHEMA
Status: DONE
Output: Requirements defined for account/device association, lookup and uniqueness behavior, expiry lifecycle, and protected token storage.
Next: DESIGN can resolve any required technology decision before implementation or migration work.
```

**Terminal return (Changelog → Operator only)** — 7-field block:
```
--- RETURN HANDOVER ---
FROM:    CHANGELOG
TO:      OPERATOR
TASK:    <original task>
STATUS:  DONE
DELIVERABLES:
  - <what was built>
  - docs/changelog/v0.1.0.md updated under Fixed
  - CHANGELOG.md milestone summary unchanged
OPEN_QUESTIONS:
  - None
NEXT:
  - Chain complete
--- END RETURN HANDOVER ---
```

**Escalating a blocked task:**
```
[ESCALATE → OPERATOR]
From: APPDEV
Reason: Implementation requires a design decision not yet made (which pagination strategy to use).
Needs: DESIGN input before proceeding.
```

**Reporting status mid-task:**
```
[STATUS: IN PROGRESS]
From: TRANSCODING
Step: FFmpeg argument builder implemented. HLS segment route pending.
Next: Implementing /Videos/{id}/hls1/{playlistId}/{segId}.ts route handler.
```

**Flagging a dependency:**
```
[DEPENDS ON: MIGRATION]
From: SCAFFOLD
Reason: Cannot wire the persistence adapter until the approved schema migration is complete.
```

**Rules:**
- Always name both the source and target agent in a handoff
- `RETURN` goes to the required loop owner: for document deliveries, the named content owner receives and validates the file even if `OPERATOR` or `VALIDATE` requested the write; otherwise it returns to the agent that issued the `HANDOFF`
- `ESCALATE` always goes to `OPERATOR` unless a different coordinator is active
- Include enough `Context` in a `HANDOFF` that the receiving agent can act without asking follow-up questions
- `STATUS` reports are optional but required when a long task yields partial output across multiple turns

---

## Handover Protocol

When work transitions between agents, use a structured **HANDOVER block**. This is the canonical way to pass context — it replaces informal summaries and ensures the receiving agent has everything needed to act without follow-up questions.

### Handover Block Format

```
--- HANDOVER ---
FROM:    OPERATOR
TO:      SCAFFOLD
TASK:    Create compile-only authenticated-session skeleton and test harness
STATUS:  Design approved — ready for scaffolding
CONTEXT:
  - Approved contract associates an authenticated session with an account and registered device
  - Approved behavior requires expiry and protected token storage, never plaintext
  - Implement only the approved representation selected by the design decision
  - Placement of contracts, skeletons, and migrations must follow accepted technical design
OPEN_QUESTIONS:
  - None
BLOCKED_BY:
  - None
--- END HANDOVER ---
```

### Field Rules

| Field | Required | Description |
|---|---|---|
| `FROM` | Yes | Sending agent name in uppercase |
| `TO` | Yes | Receiving agent name in uppercase |
| `TASK` | Yes | One-line summary of what needs to be done |
| `STATUS` | Yes | Current state — e.g. `Design approved`, `Partially implemented`, `Blocked` |
| `CONTEXT` | Yes | Bullet list of everything the receiver needs — decisions made, constraints, file paths, interface names |
| `OPEN_QUESTIONS` | Yes | Unresolved questions the receiver must answer before proceeding; `None` if clear |
| `BLOCKED_BY` | Yes | Dependencies not yet complete; `None` if unblocked |

### Rules
- Every agent transition must include a HANDOVER block — never hand off with just a prose summary
- `CONTEXT` must be self-contained — the receiving agent must not need to re-read earlier conversation turns to act
- If `OPEN_QUESTIONS` is not `None`, the receiving agent must resolve them (via `vscode_askQuestions`) before starting work
- If `BLOCKED_BY` is not `None`, the receiving agent must report `[STATUS: BLOCKED]` and wait
- If neither condition applies, the current orchestrator dispatches the receiving agent named in the next handoff
- A chain pauses only when user attention is required, the user explicitly says stop, a concrete blocker prevents safe progress, or the chain reaches its terminal return
- The handover template file lives at `.github/prompts/handover.prompt.md` — use it as a fill-in-the-blank starting point
- Agent response protocol (headers, handoff markers, handover blocks) is in `.github/instructions/agents.instructions.md`
