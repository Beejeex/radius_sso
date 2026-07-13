# Ventus - Project State

> Last updated: 2026-06-23 (v0.3.0 complete — entering v0.4.0 Client Compatibility Layer & Web Search)
> Phase: v0.4.0 Client Compatibility Layer & Web Search — implementation
> Status: v0.3.0 complete. v0.4.0 scope defined: Jellyfin client compatibility endpoints, WebSocket compatibility, React web search, OIDC claims-driven user roles, and personal media embedded/sidecar metadata. 7 epics, 20 stories.

## Current Authentication Model

| Client type | Mechanism | Boundary |
| --- | --- | --- |
| Browser/web users | OIDC authorization code flow + secure HttpOnly browser cookie | ASP.NET Core `AddOpenIdConnect("Oidc")` and `AddCookie("WebCookie")` |
| Jellyfin-compatible/API clients | `Authorization: MediaBrowser ...` token header | Dedicated `JellyfinTokenHandler` authentication handler |

First-run bootstrap uses setup mode when no validated OIDC provider exists. No browser password sign-in, local recovery account, hand-written OIDC protocol flow, custom callback controller, or custom browser session protocol is part of the active architecture.

## Active Source Documents

| Area | Document |
| --- | --- |
| Framework-default agent rule | [Agent Guidance](AGENT_GUIDANCE.md) |
| Web frontend technology | [ADR-005](../adr/005-web-interface-technology-selection.md) |
| React component/styling stack | [ADR-011](../adr/011-react-component-styling-stack.md) |
| Browser OIDC | [ADR-007](../adr/007-oidc-authorization-code-flow-architecture.md) |
| Browser session cookies | [ADR-010](../adr/010-browser-session-token-storage.md) |
| Split auth schemes | [ADR-013](../adr/013-split-auth-schemes.md) |
| First-run setup | [ADR-014](../adr/014-first-run-oidc-setup.md) |
| Identity shell spec | [SPEC-001](../specs/001-v010-identity-access-ui-shell.md) |
| Media library spec | [SPEC-002](../specs/002-v020-media-library-core-browse-ui.md) |
| Web UI testing | [v0.2.0 Web UI Tracking](../testing/011-v020-web-ui.md) |
| v0.3.0 metadata spec | [SPEC-003](../specs/003-v030-metadata-artwork-item-detail.md) |
| v0.3.0 metadata schema | [SCHEMA-003](../schema/003-v030-metadata-artwork-schema.md) |
| v0.3.0 testing — metadata foundation | [Testing](../testing/012-v030-metadata-foundation.md) |
| v0.3.0 testing — TV metadata & subtitles | [Testing](../testing/013-v030-tv-metadata-subtitles.md) |
| v0.3.0 testing — artwork | [Testing](../testing/014-v030-artwork.md) |
| v0.3.0 testing — item detail API | [Testing](../testing/014-v030-item-detail-api.md) |
| v0.3.0 testing — item detail web UI | [Testing](../testing/015-v030-item-detail-web-ui.md) |
| v0.4.0 client compatibility spec | [SPEC-004](../specs/004-v040-client-compatibility-web-search.md) |
| v0.4.0 claims roles schema | [SCHEMA-004](../schema/004-v040-claims-roles-schema.md) |
| v0.4.0 testing — client compat & roles | [Testing](../testing/016-v040-client-compat-roles-search.md) |

## Active Frontend Direction

| Area | Active Choice |
| --- | --- |
| Frontend | React 19 + TypeScript strict + Vite |
| Routing | TanStack Router |
| Server state | TanStack Query |
| Local state | Zustand |
| Forms | React Hook Form + Zod |
| Tables | TanStack Table |
| Styling | Tailwind CSS + Radix-style accessible primitives |
| API contract | ASP.NET Core OpenAPI |
| API client | Generated TypeScript client plus shared fetch wrapper |
| Web realtime | SignalR |
| Compatibility realtime | Raw WebSockets for Jellyfin clients |
| Unit/component tests | Vitest + React Testing Library |
| Browser tests | Playwright |
| Deployment | Vite `dist` served by ASP.NET Core |

## Implemented Scope

- Native ASP.NET Core OIDC and cookie authentication.
- Dedicated Jellyfin-compatible token handler for the MediaBrowser header boundary.
- Setup mode with restart-required OIDC provider bootstrap.
- Protected web API user rehydration through `/api/auth/me`.
- Library CRUD, media record persistence, library scanning services, access control, and user management services.
- React/Vite frontend scaffold with setup, sign-in, library home, library browse, admin users, admin libraries, and admin OIDC pages.
- Ventus-native `/api/...` web routes and OpenAPI contract seed.
- SignalR hub reserved for Ventus-native web realtime.
- TMDB metadata providers for movie and TV: plot synopsis, cast, ratings, genres, release year.
- TMDB artwork providers: poster and backdrop with community API key default.
- Local artwork file discovery for poster, backdrop, and logo.
- Sidecar subtitle discovery (`.srt`, `.vtt`, `.ass`, `.ssa`) and serving via compatibility endpoint.
- Item detail API endpoint at `GET /api/items/{id}` with full metadata, artwork references, cast, and subtitle availability.
- Metadata refresh via Hangfire background job queue (per-item and post-scan).
- React item detail page at `/items/{id}` with hero artwork, metadata panel, cast list, subtitles, and playback stub.

## v0.2.0 Acceptance Evidence

| Gate | Status |
| --- | --- |
| Frontend OpenAPI codegen | GREEN — all 17 React-consumed endpoints in `docs/openapi/ventus-web.json`; no gaps |
| Frontend unit/build | GREEN — Vitest 1/1, lockfile unchanged, build clean |
| .NET non-browser solution tests | GREEN — 498/498 across 5 projects (Domain 175, Application 195, Persistence 40, Api 76, Integration 12) |
| Browser smoke tests | GREEN — 5/5 Playwright tests passing |
| Production Docker build | GREEN — image 365 MB |
| Runtime health/readiness/root smoke | GREEN |
| Frontend high-severity npm audit | GREEN |
| Local Acceptance (host-run) | GREEN — User sign-off complete |
| Production Docker Acceptance | GREEN — docker compose up successful, User sign-off complete |
| **Total test suite** | **504/504 passing** (Domain 175, Application 195, Persistence 40, API 76, Integration 12, Frontend 1, Browser 5) |

## v0.3.0 Acceptance Evidence

| Gate | Status |
| --- | --- |
| Core tests (676/676) | GREEN — Domain 268, Application 367, Persistence 41 |
| Integration tests (12/12) | ✅ All passing |
| v0.3.0 routes registered and auth-gated | ✅ Verified |
| `GET /health` | ✅ 200 OK |
| `GET /health/ready` | ✅ 200 OK |
| Database schema: `artwork_records` and `subtitle_tracks` tables | ✅ Present |
| IItemLookup DI fix verified | ✅ Resolved during validation |
| Local Acceptance (host-run) | GREEN — User sign-off complete |
| Production Docker Acceptance | GREEN — docker compose up successful, User sign-off complete |
| **Total test suite** | **676/676 passing** (Domain 268, Application 367, Persistence 41) |

## Next Work

1. Migration: add role_source column to user_accounts (@Migration).
2. Implement compatibility DTOs, route handlers, WebSocket, claims roles, and search pipeline (@AppDev + domain agents).
3. P1 (deferred): personal media metadata extraction, React search UI, compatibility test suite.
4. Validate → Changelog → milestone closure.

## Milestone Status

| Milestone | Status | Notes |
| --- | --- | --- |
| v0.0.1 - Project Foundation | Complete | Docker-first foundation, health endpoints, logging, config, and protocol-edge header parsing delivered |
| v0.1.0 - Identity, Access & UI Shell | Complete | Browser shell targets React/Vite |
| v0.2.0 - Media Library Core & Browse UI | Complete | All 5 increments delivered and accepted. 504 tests GREEN (Domain 175, Application 195, Persistence 40, API 76, Integration 12, Frontend 1, Browser 5). Local Acceptance and Production Docker Acceptance both user GREEN. |
| v0.3.0 - Metadata, Artwork & Item Detail UI | Complete | All 5 increments delivered and accepted. 676 tests GREEN (Domain 268, Application 367, Persistence 41). Local Acceptance and Production Docker Acceptance both user GREEN. See [v0.3.0 changelog](../changelog/v0.3.0.md). |
| v0.4.0 - Client Compatibility Layer & Web Search | Not started | Feature work on the React/Vite frontend |
| v0.5.0 - Playback & Player UI | Not started | Uses native video + hls.js adapter |
| v0.6.0 - Web Interface Completion | Not started | Blocked by v0.5.0 |
| v0.7.0 - Background Work, Observability & Jobs UI | Not started | SignalR expected for web UI realtime |
| v0.8.0 - Plugin System & Plugin UI | Not started | Blocked by v0.7.0 |
| v0.9.0 - Multi-Container & Scale | Not started | Blocked by v0.8.0 |
| v1.0.0 - Feature-Complete Release | Not started | Blocked by all prior milestones |
