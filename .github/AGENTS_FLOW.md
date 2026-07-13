# Agent Flow — Ventus Media Server

Reference for the 30-agent pipeline ecosystem. Every task starts with `@Operator`. Agents pass work **forward** by emitting `[HANDOFF →]` blocks, and the current orchestrator dispatches those handoffs to the next bounded subagent call. Results return to OPERATOR only at the end via `--- RETURN HANDOVER ---` from CHANGELOG, followed by Operator validation of the changelog edit. Any agent may `[ESCALATE → OPERATOR]` for cross-domain decisions. No specialist may bypass the Operator flow.

---

## Agent Layers

```
┌─────────────────────────────────────────────────────────────────────┐
│  PLANNING         PM, Analyst                                       │
├─────────────────────────────────────────────────────────────────────┤
│  COORDINATION     Operator                                      │
├────────────────────────┬────────────────────────────────────────────┤
│  DESIGN            Design, ADRWriter                                │
│  COMPLIANCE        Protocol, Security, AuthFlow                     │
│  IMPLEMENTATION    AppDev, Schema, Migration, Plugin,                │
│                    LibraryScanner, Metadata, Transcoding,           │
│                    PlaybackDecision                                  │
│  QUALITY           Review, Refactor, TestGen, Validate              │
│  SPECIAL           Compat, Challenger, Scaffold                     │
│  DOCUMENTATION     StateWriter, SpecWriter, SchemaWriter,           │
│                    SecurityWriter, Docs, Changelog                  │
│  REFERENCE         JFOracle                                         │
├────────────────────────┴────────────────────────────────────────────┤
│  MANDATORY CHAIN END   Validate → Changelog → Operator validation   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Agent Roster

| Agent | Role | Invocable? |
|---|---|---|
| `@Operator` | Breaks down tasks, starts chains, receives final RETURN | Yes |
| `@PM` | Defines scope/backlog/state content and validates StateWriter deliveries | Via Operator |
| `@StateWriter` | Writes PM-approved state, roadmap, and backlog documents | Via PM |
| `@Analyst` | Defines and validates specs, product surface, auth-path expectations, acceptance criteria, and definition of done | Via Operator |
| `@SpecWriter` | Writes Analyst-approved specification documents | Via Analyst |
| `@Design` | Component design, interfaces, patterns, ADRWriter delegation and validation | Via Operator |
| `@ADRWriter` | Writes Design-approved Architecture Decision Records | Via Design |
| `@Challenger` | Applies the 10th-man rule; default pressure-test for high-impact decisions before Security or commitment | Via Operator |
| `@AppDev` | Implements general application/API/infrastructure behavior under TDD after tests are defined | Via Operator |
| `@Schema` | Persisted-data design, relationships, integrity and lookup strategy; validates schema documents | Via Operator |
| `@SchemaWriter` | Writes Schema-approved persisted schema design documents | Via Schema |
| `@Scaffold` | Boilerplate generation, project structure, compile-only skeletons, and infrastructure scaffolding | Via Operator |
| `@Migration` | Approved persistence migrations and safe schema evolution | Via Operator |
| `@Transcoding` | FFmpeg integration, HLS, hardware-accelerated encoding | Via Operator |
| `@PlaybackDecision` | `POST /Items/{id}/PlaybackInfo`, DirectPlay/Transcode logic | Via Operator |
| `@Plugin` | Plugin SDK, host, sandbox, lifecycle | Via Operator |
| `@LibraryScanner` | Filesystem scanning, change detection, background queue | Via Operator |
| `@Metadata` | External metadata providers, artwork, caching | Via Operator |
| `@Protocol` | Jellyfin protocol compliance — does this match the spec? | Via Operator |
| `@Compat` | Client breakage analysis — what does this change affect? | Via Operator |
| `@Security` | OWASP audit, split-auth path review, auth design, threat model; validates security documents | Via Operator |
| `@SecurityWriter` | Writes Security-approved persisted security documents | Via Security |
| `@AuthFlow` | Auth sequence diagrams (OIDC, setup bootstrap, session/token lifecycle, split-auth boundary) | Via Security/Design |
| `@Review` | Read-only code review, quality check | Via Operator |
| `@Refactor` | Restructure existing code for SOLID/maintainability | Via Operator |
| `@TestGen` | Defines red-baseline tests, workflow/contract/auth-path evidence, coverage gaps, milestone unit-test tracking, and test doubles using approved tooling | Via Operator |
| `@Validate` | Product-completeness gate for API/UI parity, test evidence, auth-path evidence, and readiness — second-to-last in every chain | Via Operator |
| `@Changelog` | Writes detailed milestone changelog entries and updates root milestone summaries only when needed; terminal delivery is validated by Operator | Via Validate |
| `@Docs` | Writes source/API documentation, README and usage guides for subject-owner validation | Via Operator / owning agent / Validate |
| `@JFOracle` | Read-only Jellyfin source lookup (`jellyfin-ref/` only) | Via Specialist HANDOFF |

---

## Pipeline Model

**Hard rules:**
- Every task starts with `@Operator` — no specialist may bypass the flow
- Specialists only work from an explicit `[HANDOFF →]` block
- `[HANDOFF →]`, `[RETURN →]`, and `[ESCALATE → OPERATOR]` blocks do not self-execute; Operator must dispatch each emitted route to the named target agent
- Only `@Operator` may have and use agent-invocation capability; specialists emit routing blocks and stop
- Every chain ends with **Validate → Changelog → RETURN HANDOVER → Operator changelog validation**
- `@Challenger` is read-only and advisory: it must never edit code, tests, ADRs, specs, roadmap entries, changelog entries, or any other documentation
- `@Challenger` must challenge only: it raises risks, weak assumptions, contradictions, missing evidence, failure modes, and hard questions, but never proposes solutions
- `@Challenger` challenge points are never automatically accepted; they enter a resolution loop with the challenged agent before any downstream work uses them
- Every dedicated document writer uses `model: "DeepSeek V4 Flash (deepseek)"`; `@Challenger` uses `model: "Z.ai: GLM 5.2 (openrouter)"`; `@Validate` uses `model: "DeepSeek V4 Pro (deepseek)"`
- `@AppDev`, `@TestGen`, and `@Refactor` use `model: "DeepSeek V4 Pro (deepseek)"`; domain code-writing agents use the model declared in their own agent frontmatter
- A written document is pending until its content owner reads the actual file and issues a passing `Document Validation Record`
- Pre-implementation documentation is mandatory: required SPEC, ADR, and schema documents must be accepted with passing owner validation before source code, tests, migrations, scripts, fixtures, generated files, or implementation plans begin
- Framework defaults first is mandatory: custom replacements for framework/platform/library behavior require the exception record defined in `docs/project/AGENT_GUIDANCE.md` before downstream work may consume them
- Active Copilot skills live under `.github/skills/<skill-name>/<skill-name>.md`; use them when their trigger matches, but they never override Operator routing or Ventus gates
- Product completeness is mandatory: requirement, backend behavior, API contract, frontend/UI reachability or explicit non-UI classification, test evidence, and auth-path classification must line up before `@Validate` may pass
- Split-auth boundaries are mandatory: browser OIDC/`WebCookie`, Jellyfin-compatible `MediaBrowser`, setup-only, anonymous/public, and internal paths must not be silently mixed

```
Operator
    │
    ├──[HANDOFF →]──► Agent A
    │                     │
    │                     ├──[HANDOFF →]──► Agent B
    │                     │                    │
    │                     │                    └──[HANDOFF →]──► Validate
    │                     │                                          │
    │                     │                    PASS                  │
    │                     │                    └──[HANDOFF →]──► Changelog
    │                     │                                          │
    │◄─── --- RETURN HANDOVER --- ─────────────────────────────────┘
    │
    │   (any agent may [ESCALATE → OPERATOR] for cross-domain decisions)
```

**Rules:**
- Operator starts the chain and dispatches every emitted handoff until terminal RETURN
- Specialists declare the next step with `[HANDOFF →]` and a full context block; the current bounded call stops there
- Every chain ends with **Validate → Changelog → Operator changelog validation** — no exceptions
- Validate on FAIL: emits targeted `[HANDOFF →]` back to the relevant fixer; Operator dispatches it
- Production milestone closure requires Local Acceptance with SQL and the supported OIDC backend running, user Local Acceptance GREEN, `GREEN` Production Docker Acceptance, and user Production Docker Acceptance GREEN, or an explicit user exception recorded with risk and follow-up
- Inner-loop build/test commands may run locally, with isolated local dependency containers when needed; Production Docker Acceptance starts only after user Local Acceptance GREEN
- Changelog always emits `--- RETURN HANDOVER ---` (7-field block) to Operator, which reads and validates the changed detailed milestone changelog and root summary when modified before completion
- Inline `[RETURN →]` (4-field) used for specialist→specialist returns only

### Runtime Dispatch Rule

Subagents cannot self-chain. When a subagent emits `[HANDOFF → CHALLENGER]`, `[HANDOFF → ADRWRITER]`, `[HANDOFF → VALIDATE]`, or any other handoff, that text is a required instruction to the orchestrator. It does not launch the next agent by itself.

Operator must inspect every subagent result for:

- `[ESCALATE → OPERATOR]` — resolve or ask the user, then re-dispatch
- `[HANDOFF → TARGET]` — invoke `TARGET` with the handoff context
- `[RETURN → CALLER]` — feed the result back to `CALLER` if the caller has unresolved loop work
- `--- RETURN HANDOVER ---` from `CHANGELOG` — terminal delivery requiring Operator changelog validation

Missing a required dispatch is an Operator gate failure.

### Artifact Ownership Gate

Dispatch decides who runs next. Artifact ownership decides who is allowed to change what. Operator and non-owning agents must not impersonate another specialist by writing that specialist's artifact inline.

| Artifact or verdict | Writer | Content owner / validator |
|---|---|---|
| Architecture decision records in `docs/adr/` | `@ADRWriter` | `@Design` |
| Project state, roadmap, and backlog documents | `@StateWriter` | `@PM` |
| Product, domain, UX, compatibility, and operational specs | `@SpecWriter` | `@Analyst` |
| Inline component/interface design outputs | `@Design` | `@Design` |
| Persisted data/schema designs | `@SchemaWriter` | `@Schema` |
| Persisted threat models and security assessments | `@SecurityWriter` | `@Security` |
| Approved persistence migrations | `@Migration` | `@Migration` |
| Production code | `@AppDev` or the relevant domain owner | Same code owner |
| Test code and test-plan output | `@TestGen` | `@TestGen` |
| Milestone unit-test tracking documents in `docs/testing/` | `@TestGen` | `@Validate` |
| Public/API/source/README/operator documentation | `@Docs` | Relevant subject-matter owner |
| Validation verdict | `@Validate` | `@Validate` |
| Changelog entry and terminal return handover | `@Changelog` | `@Operator` |

If a document must be created or changed, dispatch its writer from the content owner, then dispatch the writer result back to that owner for validation. If a subagent result says it created, updated, or edited an artifact assigned to another writer, or downstream work consumes a document without owner validation, treat the result as a protocol failure and recover through Operator.

### Document Validation Gate

Document writers record approved content; content owners approve meaning. After every document delivery, its content owner must read the actual written file and emit:

```
Document Validation Record
Document: <path>
Writer: <agent>
Owner: <agent>
Validation Round: 1 | 2
Status: PASS | FAIL
Checked: <content compared against approved decision/requirements/facts>
Corrections: None | <required corrections returned to writer>
```

On `FAIL`, the owner must state whether the failure is a recording defect against unchanged approved content or revised approved content:

- For a recording defect, Operator returns explicit corrections to the same writer and routes the corrected file back to the owner with the next `Validation Round`.
- If the owner changes the intended meaning, the owner supplies revised approved content to the same writer; the new content starts at validation round 1, and any decision gate invalidated by the revision must run again first.
- After validation round 2 fails for the same approved content, or when writer and owner cannot resolve what the approved content requires, Operator receives an escalation and stops downstream use of the document.

`@Validate` checks that all documents consumed in the chain have passing records; it does not substitute for content-owner review.

### Pre-Implementation Documentation Gate

Source code, tests, migrations, scripts, fixtures, generated files, and implementation plans must not begin until the required SPEC, ADR, and schema documents are accepted, linked from the active milestone context, and backed by passing owner `Document Validation Record` evidence.

`Draft`, `In Review` without owner PASS, pending validation, stale, missing, or unlinked docs block `@TestGen`, `@AppDev`, domain implementation, `@Migration`, and generated-file work unless the user explicitly records an exception with risk and follow-up.

If implementation, tests, migrations, scripts, fixtures, generated files, or implementation plans already exist before required document acceptance, report `Pre-Implementation Documentation Gate bypassed`. Existing work and green tests do not make Draft or pending documents accepted; downstream use remains blocked until the document loop completes or the user accepts the exception.

### Framework Defaults First Gate

If an agent proposes, approves, tests, documents, scaffolds, or implements custom services, middleware, stores, parsers, validators, schedulers, security logic, protocol handling, persistence state, or framework replacement code, Operator and Validate must verify that the exception record required by `docs/project/AGENT_GUIDANCE.md` exists.

If the exception record is missing, the chain is incomplete. Route the work back to the owning Design, Security, Schema, Analyst, or TestGen agent to use the default framework feature or to produce an approved exception record before implementation continues.

### Product Completeness Gate

If an agent changes or claims product behavior, Operator and Validate must verify traceability from requirement to backend behavior, API contract, frontend consumer or explicit non-UI classification, UI/API workflow, tests, and validation evidence.

`@Analyst` defines the intended product surface and auth-path expectations. `@TestGen` maps tests to acceptance criteria, workflows, contracts, and auth paths. `@Security` reviews split-auth classification when auth/session/setup/users/roles/protected endpoints or client compatibility are touched. `@Validate` fails the chain when backend capability, frontend reachability, tests, and security/auth-path evidence do not line up.

Green generated tests, component-only tests, or backend-only tests do not prove UI-facing or client-facing product completeness.

### Backend-Ready UI Gate

Production frontend/UI code waits for `Backend Ready For UI` evidence for the consumed backend/API capability, as defined in `docs/project/AGENT_GUIDANCE.md`.

Rules:
- Backend/API behavior, tests, contract evidence, and auth-path classification must be known before production React pages/components, frontend API-client wiring, or browser workflow behavior is created.
- UI work proceeds one route/page/component/workflow slice at a time.
- Each UI slice must be GREEN for its planned component/API-client/browser evidence before the next UI slice starts, unless the user records a batching exception with risk and follow-up.
- Local Acceptance and Production Docker Acceptance are final milestone gates after backend readiness and UI slice validation.

### Gate Failure Recovery

If Operator discovers that a required handoff was emitted but not dispatched, the chain is incomplete.

- Stop downstream work that depends on the skipped gate.
- Dispatch the missing agent with the actual proposal, ADR draft, design, or decision record that bypassed the gate.
- Return every writer result to its content owner for the normal document validation loop.
- Route any project-state correction through `@PM` → `@StateWriter` → `@PM` validation before workflow validation.
- Do not treat skipped-gate ADRs, specs, scaffolds, or implementation work as accepted until the missing loop is completed or escalated.

---

## RETURN Format Reference

**Specialist → Specialist** (4-field inline):
```
[RETURN → DESIGN]
From: SCHEMA
Status: DONE
Output: Requirements defined for account/device association, lookup and uniqueness behavior, expiry lifecycle, and protected token storage.
Next: DESIGN can resolve any required technology decision before implementation or migration work.
```

**Changelog → Operator** (7-field block — terminal document delivery):
```
--- RETURN HANDOVER ---
FROM:    CHANGELOG
TO:      OPERATOR
TASK:    <original task description>
STATUS:  DONE
DELIVERABLES:
  - <what was built / documented>
  - docs/changelog/v0.1.0.md updated under Fixed
  - CHANGELOG.md milestone summary unchanged
OPEN_QUESTIONS:
  - None
NEXT:
  - Operator reads and validates the changelog entry; chain completes only on PASS
--- END RETURN HANDOVER ---
```

---

## Workflow Playbooks

In abbreviated playbook paths below, `@PM if state changed` means `@PM` → `@StateWriter` → `@PM` document validation; `@Docs if needed` means `@Docs` → the named subject-matter owner for document validation; and a terminal Changelog return is complete only after `@Operator` validates the changelog edit.

### TestGen Placement Rules

`@TestGen` should be introduced before implementation whenever accepted specs, contracts, or review findings are clear enough to define expected behavior. Draft, pending-validation, stale, missing, or unlinked required docs block behavior-changing test generation unless the user explicitly records an exception with risk and follow-up.

Rules:
- Required SPEC, ADR, and schema documents must be accepted with passing owner validation records before `@TestGen` creates behavior-changing tests, red baselines, or accepted test plans.
- For new features and increments, `@TestGen` creates the red baseline or expected coverage before implementation is considered complete.
- If no test project or harness exists yet, `@Scaffold` may create only the minimal test structure first, then hand off to `@TestGen` before production behavior is implemented.
- A normal flow must not place `@TestGen` only after implementation. Post-implementation `@TestGen` is only an exception loop for newly discovered gaps.
- If an implementation agent discovers an uncovered scenario, it hands back to `@TestGen`; after tests are updated, work returns to the same owning implementation agent.
- Bug fixes should get a failing regression test before the final fix whenever the bug is reproducible.
- Security fixes must include `@TestGen` for security regression coverage before `@Validate`.
- Domain behavior, invariant, compatibility mapping, or persistence-rule changes must include `@TestGen` before `@Validate`.
- `@TestGen` owns milestone unit-test tracking documents under `docs/testing/`.
- If milestone unit-test readiness, coverage gaps, deferred/skipped tests, or run result flags change, route through `@TestGen` to update the tracking document before `@Validate`.
- If production-facing behavior or milestone closure is in scope, route through `@TestGen` to define and track Local Acceptance, user Acceptance sign-off checkpoints, and required Production Docker Acceptance before `@Validate`.
- Every test/build/runtime run reported by an agent must include execution context: Local, Local + dependency containers, or Production Docker container.

### Production Code TDD Rules

All production-code writing agents work under a tests-first contract.

Production-code writing agents:
- `@AppDev` for general application, API, middleware, service orchestration, and infrastructure behavior
- `@Transcoding`, `@PlaybackDecision`, `@Plugin`, `@LibraryScanner`, and `@Metadata` for specialized domain behavior
- `@Schema` and `@Migration` for persistence code and migrations
- `@Refactor` for behavior-preserving production-code restructuring
- `@Scaffold` only for skeletons, boilerplate, and compile-only wiring; behavior implementation moves to `@AppDev` or a domain owner

Rules:
- Required SPEC, ADR, and schema documents must be accepted with passing owner validation records before behavior-changing production code, persistence migrations, scripts, fixtures, generated files, or implementation plans begin.
- Before behavior-changing production code, a `@TestGen` red baseline or accepted test plan must exist.
- The implementing agent writes the smallest production change needed to satisfy the defined tests.
- If implementation reveals missing scenarios, the implementing agent hands back to `@TestGen` before broadening behavior, then resumes through the same owning implementation agent.
- Test-running agents must report result flags (`RED-EXPECTED`, `RED-REGRESSION`, `GREEN`, `BLOCKED`, `SKIPPED`, `FLAKY`) for milestone tracking. `RED-EXPECTED` is reserved for the intentional TestGen baseline before implementation.
- Local and local-dependency-container runs support inner-loop confidence and Local Acceptance when SQL and the supported OIDC backend are running; they do not replace Production Docker Acceptance when that gate is required.
- If the task is pure compile-only scaffolding or a generated migration in the approved persistence approach, the agent may proceed without a red baseline only after required docs are accepted, and must still hand off to `@TestGen` for behavior coverage before `@Validate`.
- `@Validate` fails any behavior-changing implementation that lacks tests or an accepted reason tests are not applicable.

### Production Milestone Validation Rules

"Tested" does not mean production-ready when the Local Acceptance and Production Docker Acceptance paths were not both exercised in order.

Rules:
- Startup composition, dependency injection, configuration binding, persistence, migrations, auth/session behavior, routing, health/readiness, Docker/container runtime behavior, deployment settings, and milestone closure require the two-stage production milestone gate.
- Stage 1 is Local Acceptance: agents deliver a running local instance with SQL and the supported OIDC backend, plus local build/test/smoke evidence.
- The user owns and gives Local Acceptance GREEN after local testing; agents only record the user's decision.
- Stage 2 starts only after user Local Acceptance GREEN: build and test the production Docker image with the supported backend.
- Production Docker Acceptance must use the real production Docker container or an explicitly approved production-equivalent path.
- The user owns and gives Production Docker Acceptance GREEN after validating the production Docker build; agents only record the user's decision.
- The detailed ownership and split rules are canonical in `docs/project/AGENT_GUIDANCE.md`: agents do not grant either GREEN, the gates require separate evidence/sign-offs, and Docker runs before Local GREEN are preparation evidence only.
- Local and local-dependency-container results do not satisfy the Production Docker Acceptance gate, even when all tests are `GREEN`.
- Unit, component, integration, or `WebApplicationFactory` tests that use test-only service replacement, stubs, or manual service registration do not satisfy this gate.
- `@TestGen` defines and tracks both Local Acceptance and required Production Docker Acceptance in `docs/testing/`.
- `@Validate` fails missing user Local Acceptance GREEN, missing `GREEN` Production Docker Acceptance evidence, missing user Production Docker Acceptance GREEN, `SKIPPED`, `BLOCKED`, deferred, fixture-only, or stale validation unless the user explicitly accepts the exception and the chain records the risk and follow-up.
- `@Operator` must not accept terminal closure when this gate is applicable and either user GREEN, the `GREEN` Production Docker Acceptance result, or a recorded user exception is missing.
- Local Acceptance blockers must be reported separately and before Production Docker Acceptance blockers. If Local Acceptance evidence or user Local Acceptance GREEN is missing, Production Docker Acceptance is blocked/not eligible to start; Operator and Validate must not reduce that state to only "No Production Docker Acceptance."

---

### Docs Placement Rules

`@Docs` is the writer for public/API/source/README/operator documentation and belongs before `@Validate` whenever implementation or design work changes those documents. Its handoff must name the subject-matter content owner that validates its delivery.

Rules:
- Normal code flow is `@TestGen` → owning code agent → `@PM` → `@StateWriter` → `@PM` validation if project state changed → `@Docs` → subject-owner validation if documentation is affected → `@Validate`.
- `@Docs` documents only implemented or formally accepted behavior; it must not invent product behavior, protocol details, or design decisions.
- `@Validate` fails missing required source/API docs, README/API docs, ADR-linked documentation gaps, or missing document-owner validation and hands writing failures to `@Docs` with a named content owner, then re-runs validation after owner approval.
- `@Docs` never replaces `@Changelog`; changelog remains the last step after `@Validate` passes.

---

### Project State Rules

`@PM` owns the content of `docs/project/STATE.md`, `docs/ROADMAP.md`, and `docs/BACKLOG.md`; `@StateWriter` is their document writer.

Rules:
- `@Operator` routes a focused state-update handoff to `@PM` whenever the phase, gate, increment, next required approval, active work, blocker, risk, open question, decision need, or verification result changes; PM gives approved content to `@StateWriter` and validates the returned file.
- Milestone unit-test tracking status changes that affect readiness, blockers, or validation results are state-impacting, including `Ready For Validation`, final `GREEN`, `RED-REGRESSION`, `BLOCKED`, unresolved `SKIPPED`, or `FLAKY`.
- Specialist agents report state-impacting facts in handoff output, but do not directly maintain `docs/project/STATE.md`.
- `@Validate` checks the state file and PM validation record before passing. If it finds drift or missing owner validation, it escalates to `@Operator`; `@Operator` routes the update through `@PM` → `@StateWriter` → `@PM`, then resumes validation.
- `@Changelog` never updates project state.

---

### Challenger Resolution Loop

`@Challenger` is a pressure-test agent, not an implementation, documentation, design, or decision-record author.

Rules:
- `@Challenger` must not change files. This includes code, tests, ADRs, specs, roadmap entries, workflow docs, changelog entries, and configuration.
- `@Challenger` must not propose solutions, implementation approaches, ADR wording, documentation text, schema shapes, or code changes.
- `@Challenger` returns challenge points to the agent whose work was challenged. Each point must be a concern, contradiction, missing evidence, weak assumption, failure mode, or hard question.
- The challenged agent must validate every Challenger item and respond with `Accepted`, `Rejected`, `Alternative Proposed`, or `Escalate`, with sufficient argumentation.
- The response returns to `@Challenger`, which marks each item `Resolved`, `Unresolved`, or `Escalated`. If unresolved, `@Challenger` explains the remaining concern without proposing a solution.
- The challenged agent and `@Challenger` repeat this loop until every item is resolved or escalated.
- Default limit: two Challenger re-check rounds. One extra round is allowed for high-impact decisions only when the challenged agent states what changed and why another pass is likely to resolve the issue. After that, unresolved items must escalate to `@Operator` with both sides' arguments.
- Before continuing downstream, the challenged agent must produce a `Challenger Resolution Summary` listing each point, final status, rationale, and follow-up owner.
- Accepted or alternative outcomes become executable work only when the challenged agent hands them to the correct writer or code owner. Examples: `@ADRWriter` records ADRs for `@Design` validation, `@Docs` edits documentation for subject-owner validation, `@SchemaWriter` records persisted schema designs for `@Schema` validation, `@Scaffold` creates skeletons, and `@AppDev` or a domain owner changes production behavior.
- No downstream agent may treat Challenger challenge points as accepted unless the challenged agent has supplied a resolved Challenger summary.
- ADR-worthy decisions must not be handed to `@ADRWriter` until Challenger has actually run and `@Design` has produced a `Challenger Resolution Summary`, unless Operator explicitly documents a low-blast-radius skip reason. After writing, `@Design` must validate the ADR document.
- A printed `[HANDOFF → CHALLENGER]` block is not evidence that Challenger ran. Operator must dispatch it and feed the result back through the resolution loop.

Resolution summary format:

| Point | Final Status | Rationale | Follow-up Owner |
|---|---|---|---|
| CH-1 | Accepted - Resolved / Rejected - Resolved / Alternative - Resolved / Escalated | Owning-agent reasoning and Challenger resolution note | `@ADRWriter` / `@Docs` / `@SchemaWriter` / `@Scaffold` / `@AppDev` / domain owner / None / `@Operator` |

---

### Pattern 1 — New Feature

Full development cycle from scope to changelog.

Use `@Challenger` by default for ADR-worthy, security-sensitive, client-facing, or hard-to-reverse decisions.

Use the early `@TestGen` pass to define the red baseline or expected coverage before implementation starts. Use a second `@TestGen` pass only when implementation exposes additional gaps, then return to the same owning implementation agent.

```
User → @Operator
    │
  └──[HANDOFF →]──► PM              (scope/state content)
              ├──[HANDOFF →]──► StateWriter      (if planning/state document changes)
              │◄──[RETURN →]────┘                (PM reads and validates document)
              │
              └──[HANDOFF →]──► Analyst         (feature spec content + acceptance criteria)
                        ├──[HANDOFF →]──► SpecWriter       (record specification)
                        │◄──[RETURN →]────┘                (Analyst reads and validates spec)
                        │
                        └──[HANDOFF →]──► Design          (component interfaces)
                                    │
                                    ├──[HANDOFF →]──► Challenger       (default for high-impact decisions)
                                    │◄──[RETURN →]────┘
                                    │
                                    ├── Design ↔ Challenger             (resolution loop until all points resolved/escalated)
                                    │
                                    ├──[HANDOFF →]──► ADRWriter        (record accepted decision, if needed)
                                    │◄──[RETURN →]────┘                (Design reads and validates ADR)
                                    │
                                    └──[HANDOFF →]──► Schema          (persistence requirements + migration risks)
                                              ├──[HANDOFF →]──► SchemaWriter   (if persisted schema document)
                                              │◄──[RETURN →]────┘             (Schema reads and validates document)
                                              │
                                              └──[HANDOFF →]──► Security        (threat model / OWASP check)
                                                                    │
                                                                    ├──[HANDOFF →]──► AuthFlow     (if auth flow design is needed)
                                                                    │◄──[RETURN →]────┘            (flow content returns to Security)
                                                                    ├──[HANDOFF →]──► SecurityWriter (if persisted security record)
                                                                    │◄──[RETURN →]────┘             (Security reads and validates document)
                                                                    │
                                                                    └──[HANDOFF →]──► Scaffold     (structure/harness only, if needed)
                                                                                │
                                                                                └──[HANDOFF →]──► TestGen      (red baseline / expected coverage)
                                                                                          │
                                                                                          └──[HANDOFF →]──► Migration   (persistence migration, if behavior-impacting)
                                                                                                      │
                                                                                                      └──[HANDOFF →]──► AppDev / Domain Owner
                                                                                                                  │      (production behavior)
                                                                                                                  │
                                                                                                                  ├──[HANDOFF →]──► TestGen
                                                                                                                  │              (only if new gaps are discovered)
                                                                                                                  │◄──[RETURN →]────┘
                                                                                                                  │
                                                                                                                  └──[HANDOFF →]──► PM
                                                                                                                              │      (if project state changed)
                                                                                                                              │
                                                                                                                              └──[HANDOFF →]──► Docs
                                                                                                                                          │      (if public/API/source docs affected)
                                                                                                                                          │
                                                                                                                                          └──[HANDOFF →]──► Validate
                                                                                                                                            │  PASS
                                                                                                                                            └──[HANDOFF →]──► Changelog
                                                                                                                                                        │
                                                                                                                           ◄── --- RETURN HANDOVER --- ──┘
```

---

### Pattern 2 — Bug Fix

Targeted fix with review and regression tests.

If the owning fixer is unclear or the bug spans multiple domains, `@Review` must `[ESCALATE → OPERATOR]` instead of guessing.

If the bug is reproducible after review, `@TestGen` must capture the failing regression before the fixer completes the final change.

```
User → @Operator
    │
  └──[HANDOFF →]──► Review      (identify root cause and owning fixer)
              │
              └──[HANDOFF →]──► TestGen     (failing regression test, if reproducible)
                        │
                        └──[HANDOFF →]──► [Fixer]     (AppDev / domain agent / Security / Schema / Refactor — whichever owns the bug)
                                    │
                                    ├──[HANDOFF →]──► TestGen     (only if new gaps are discovered)
                                    │              │
                                    │◄──[RETURN →]─┘
                                    │
                                    └──[HANDOFF →]──► PM          (if project state changed)
                                                │
                                                └──[HANDOFF →]──► Docs        (if public/API/source docs affected)
                                                            │
                                                            └──[HANDOFF →]──► Validate
                                                          │  PASS
                                                          └──[HANDOFF →]──► Changelog
                                                                      │
                                                         ◄── --- RETURN HANDOVER --- ──┘
```

If review cannot reproduce the bug yet, the fixer may investigate first, then hand off to `@TestGen` once the failing condition is understood, then complete the final fix before `@Validate`.

---

### Pattern 3 — Design with Second Opinion

Use when a decision is non-obvious or has significant trade-offs.

`@Design` must complete the `@Challenger` resolution loop before handing off to `@ADRWriter`, `@Validate`, or any implementation-facing owner. It must read and validate the ADRWriter delivery before downstream use.

```
User → @Operator
    │
  └──[HANDOFF →]──► Design      (initial proposal)
              │
              ├──[HANDOFF →]──► Challenger   (stress test the proposal)
              │◄──[RETURN →]────┘
              │
              ├── Design ↔ Challenger         (resolution loop until all points resolved/escalated)
              │
              ├──[HANDOFF →]──► ADRWriter    (record the decision)
              │◄──[RETURN →]────┘            (Design reads and validates ADR)
              │
              └──[HANDOFF →]──► PM          (if project state changed)
                        │
                        └──[HANDOFF →]──► Docs        (if docs/guides need updating)
                                  │
                                  └──[HANDOFF →]──► Validate
                                  │  PASS
                                  └──[HANDOFF →]──► Changelog
                                              │
                                 ◄── --- RETURN HANDOVER --- ──┘
```

---

### Pattern 4 — Security Audit

Audit existing code or a proposed design.

If `@Challenger` is injected before Security, its challenge points return to the challenged owner (`@Security` for a threat model/security plan, or the originating design agent for a design under security review). That owner must complete the resolution loop before Security findings or fixes are handed downstream.

If Security finds issues across multiple owning domains and cannot identify one primary fixer, it must `[ESCALATE → OPERATOR]` instead of guessing.

If Security finds a fixable issue, `@TestGen` must add or update security regression coverage before `@Validate`.

```
User → @Operator
    │
  └──[HANDOFF →]──► Security    (scope threat model / audit)
                        │
                        ├──[HANDOFF →]──► Challenger  (default — pressure-test threat model)
                        │◄──[RETURN →]────┘
                        │
                        ├── Security ↔ Challenger      (resolution loop until all points resolved/escalated)
                        │
                        └── Security                   (full OWASP + auth audit)
                        │
                        ├──[HANDOFF →]──► AuthFlow     (if auth flow design is needed)
                        │◄──[RETURN →]────┘            (flow content returns to Security)
                        ├──[HANDOFF →]──► SecurityWriter (if persisted security record)
                        │◄──[RETURN →]────┘             (Security reads and validates document)
                        │
                        └──[HANDOFF →]──► TestGen     (security regression coverage)
                                    │
                                    └──[HANDOFF →]──► [Fixer]     (if issues found — AppDev / domain agent / Schema / etc.)
                                                │
                                                └──[HANDOFF →]──► PM          (if project state changed)
                                                            │
                                                            └──[HANDOFF →]──► Docs        (if public/API/source docs affected)
                                                                        │
                                                                        └──[HANDOFF →]──► Validate
                                                                          │  PASS
                                                                          └──[HANDOFF →]──► Changelog
                                                                                      │
                                                                         ◄── --- RETURN HANDOVER --- ──┘
```

---

### Pattern 5 — Pre-Merge Check

Validate protocol compliance and client compatibility before merging.

If Protocol cannot determine the contract after targeted `@JFOracle` lookup, it must `[ESCALATE → OPERATOR]` instead of guessing.

```
User → @Operator
    │
  └──[HANDOFF →]──► Protocol    (does this match the Jellyfin spec?)
              │
              ├──[HANDOFF →]──► JFOracle    (targeted Jellyfin source lookup, if needed)
              │◄──[RETURN →]────┘
              │
              └──[HANDOFF →]──► Compat      (will this break any existing client?)
                        │
                        └──[HANDOFF →]──► Review      (final code quality pass)
                                    │
                                    └──[HANDOFF →]──► PM          (if project state changed)
                                                │
                                                └──[HANDOFF →]──► Docs        (if public/API/source docs affected)
                                                            │
                                                            └──[HANDOFF →]──► Validate
                                                              │  PASS
                                                              └──[HANDOFF →]──► Changelog
                                                                          │
                                                             ◄── --- RETURN HANDOVER --- ──┘
```

---

### Pattern 6 — Domain Deep Work

Focused single-domain implementation (transcoding, metadata, scanning, etc.).

The normal domain rule is: research/design decisions first, then `@TestGen`, then the owning domain implementation agent, then `@PM` → `@StateWriter` → `@PM` validation if project state changed, then `@Docs` → subject-owner validation if documentation is affected, then `@Validate`.

Examples:
- Playback: `@Protocol` / `@JFOracle` if needed → `@TestGen` → `@PlaybackDecision` → `@Transcoding` if needed → `@AppDev` if endpoint orchestration is needed → validated state/docs loops if needed → `@Validate`
- Library scanning: `@Schema` / `@SchemaWriter` → `@Schema` validation if a schema document is needed → `@TestGen` → `@LibraryScanner` → `@Metadata` if enrichment is in scope → validated state/docs loops if needed → `@Validate`
- Plugin: `@Security` / `@Challenger` / `@ADRWriter` with `@Design` validation if needed → `@Schema` if needed → `@TestGen` → `@Plugin` → validated state/docs loops if needed → `@Validate`
- Metadata: `@Security` if remote fetch or SSRF risk exists → `@Schema` if needed → `@TestGen` → `@Metadata` → validated state/docs loops if needed → `@Validate`

```
User → @Operator
    │
    └──[HANDOFF →]──► [Research / Design Owner]
                          │          (Protocol / JFOracle / Security /
                          │           Schema / ADRWriter / Challenger as needed)
                          │
                          └──[HANDOFF →]──► TestGen     (red baseline / expected coverage)
                                                │
                                                └──[HANDOFF →]──► [Domain Agent]
                                                              │   (Transcoding / Metadata /
                                                              │    LibraryScanner / PlaybackDecision / Plugin)
                                                              │
                                                              ├──[HANDOFF →]──► TestGen
                                                              │              (only if new gaps are discovered)
                                                              │◄──[RETURN →]────┘
                                                              │
                                                              └──[HANDOFF →]──► PM
                                                                          │      (if project state changed)
                                                                          │
                                                                          └──[HANDOFF →]──► Docs
                                                                                      │      (if public/API/source docs affected)
                                                                                      │
                                                                                      └──[HANDOFF →]──► Validate
                                                                                        │  PASS
                                                                                        └──[HANDOFF →]──► Changelog
                                                                                                            │
                                                                     ◄── --- RETURN HANDOVER --- ──────────┘
```

If the work becomes a broader cross-domain design or ownership decision, the domain agent must `[ESCALATE → OPERATOR]`.

### Optional TDD Gap Loop

Use only when the owning code agent discovers a scenario that was not covered by the existing red baseline or accepted test plan.

```
Owning Code Agent
    │
    └──[HANDOFF →]──► TestGen             (add missing scenario)
              │
              └──[RETURN → OWNING AGENT]  (updated tests / plan)
                        │
                        └── Owning Code Agent completes the smallest production change
```

This loop must return to the same owning implementation agent unless `@Operator` reassigns ownership.

---

## Escalation

Any agent that hits a **cross-domain decision, a missing design, or a blocker** it cannot resolve alone must escalate — never guess:

```
[ESCALATE → OPERATOR]
From: SCAFFOLD
Reason: Migration not yet complete — cannot wire persistence access without the final schema.
Needs: MIGRATION to finish before SCAFFOLD can proceed.
```

Operator re-coordinates and re-issues `[HANDOFF →]` after the blocker is resolved.

---

## Flow Enforcement

- A specialist agent invoked directly by the user must redirect the task back to `@Operator` and must not start the task
- Implementation, design, review, documentation, schema, protocol, testing, migration, and plugin work all require an Operator-started chain
- Missing `Validate`, `Changelog`, terminal Operator changelog validation, or required document-owner validation is a protocol violation, not an optional shortcut
