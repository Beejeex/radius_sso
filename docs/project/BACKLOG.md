# Ventus — Backlog

> **Status:** Active — greenfield
> **Last updated:** 2026-06-23
> **Format:** Epics with stories. Priority: P0 = must-have for milestone gate, P1 = important, P2 = nice-to-have. Scope: S = small (<1 day), M = medium (1–3 days), L = large (3–5 days), XL = extra-large (>5 days; should be broken down before starting).
> **Frontend scope:** Web interface backlog items target the React/Vite stack selected by ADR-005 and ADR-011. They are feature work on that stack.

---

## Milestone v0.0.1 — Project Foundation

### EPIC: Container & Build Pipeline

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| F-001 | Multi-stage Dockerfile: build stage + minimal runtime stage, non-root user, pinned base image, HEALTHCHECK at `/health` | P0 | M | Scaffold |
| F-002 | `docker-compose.yml` with single-container profile, environment-variable config, and named volume mounts | P0 | S | Scaffold |
| F-003 | Project structure with approved language/runtime (pending ADR), linting, and formatting config | P0 | M | Scaffold |
| F-004 | In-container build and test commands documented; no host toolchain required | P0 | S | Scaffold |
| F-005 | Commit-message hook for Conventional Commits enforcement | P0 | S | Scaffold |

### EPIC: Configuration Loading

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| F-006 | Typed configuration contract loaded from environment variables | P0 | M | AppDev |
| F-007 | Required configuration validated at startup; missing or invalid values produce a clear structured error and non-zero exit | P0 | M | AppDev |
| F-008 | All supported environment variables documented in configuration reference | P1 | S | Docs |

### EPIC: Health Endpoints

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| F-009 | `GET /health` — liveness; no authentication required; always `200` if process is alive | P0 | S | AppDev |
| F-010 | `GET /health/ready` — readiness; returns `503` until persistence migrations complete and library index is warm; `200` when ready | P0 | M | AppDev |

### EPIC: Structured Logging

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| F-011 | Approved structured logging abstraction wired; emit JSON to stdout | P0 | M | AppDev |
| F-012 | Log verbosity level configurable via environment variable | P0 | S | AppDev |

### EPIC: HTTP Request Handling Skeleton

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| F-013 | HTTP server with approved web framework; `404` and `500` return RFC 7807 error responses | P0 | M | AppDev |
| F-014 | Parse `Authorization: MediaBrowser ...` header; extract Client, Device, DeviceId, Version, Token fields | P0 | M | AppDev |
| F-015 | Request trace span emitted for every HTTP request | P1 | M | AppDev |

---

## Milestone v0.1.0 — Identity, Access & UI Shell

### EPIC: OIDC Sign-In

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| I-001 | OIDC discovery document fetch and provider configuration at startup | P0 | M | AppDev |
| I-002 | OIDC authorization code flow: redirect, callback, token exchange | P0 | L | AppDev |
| I-003 | Map OIDC identity claims to Ventus user account; create account on first sign-in when allowed | P0 | M | AppDev |
| I-004 | OIDC provider configuration stored in persistence and editable by administrators | P1 | M | AppDev |

### EPIC: First-Run Setup and OIDC Provider Bootstrap

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| I-005 | First-run setup mode is active until one enabled, validated OIDC provider exists | P0 | M | AppDev |
| I-006 | Setup UI configures the initial OIDC provider without a setup key | P0 | M | AppDev |
| I-007 | Setup provider creation requires restart so the native `Oidc` scheme loads from the single validated provider | P0 | M | AppDev |

### EPIC: Session & Device Lifecycle

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| I-008 | Session creation: associate with user account and registered device; persist with configurable TTL expiry | P0 | M | AppDev |
| I-009 | Session expiry: enforce token TTL; reject expired sessions with `401` | P0 | M | AppDev |
| I-010 | Session revocation: invalidate session immediately on sign-out or admin revoke | P0 | M | AppDev |
| I-011 | Device registration: persist device name, type, and client info from auth header fields | P0 | M | AppDev |
| I-012 | Session list endpoint: users see their own active sessions; admins see all | P1 | M | AppDev |

### EPIC: Auth Events Audit Logging

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| I-013 | Audit log entries for: login success, login failure, token issued, token revoked, session expired | P0 | M | AppDev |
| I-014 | Each audit log entry includes timestamp, user identity, device, hashed source IP, and action | P0 | S | AppDev |

### EPIC: Route Authorization

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| I-015 | Protected routes reject unauthenticated requests with `401`; auth header parsed and session validated | P0 | M | AppDev |
| I-016 | Administrator-only routes enforce admin role; return `403` for authenticated non-admins | P0 | M | AppDev |

### EPIC: Web Interface — Authentication UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-001 | Sign-in page: OIDC redirect flow | P0 | M | AppDev |
| W-002 | First-run setup page for initial OIDC provider configuration | P0 | M | AppDev |
| W-003 | Sign-out and session expiry redirect handling | P0 | S | AppDev |

---

## Milestone v0.2.0 — Media Library Core & Browse UI

### EPIC: Library Management

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| L-001 | Persist library definitions: name, type (Movie/TV/Personal), root paths, enabled state | P0 | M | AppDev |
| L-002 | Administrator CRUD for library definitions via API | P0 | M | AppDev |
| L-003 | Library root path validated against allowed mount points; path traversal rejected | P0 | S | AppDev |
| L-004 | Library removal deletes only Ventus-managed records; original media files are not touched | P0 | S | AppDev |
| L-005 | Destructive library actions require confirmation; logged to audit | P0 | S | AppDev |

### EPIC: Library Scanning

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| L-006 | Filesystem walker: discover media files by extension for Movie, TV, and Personal library types | P0 | L | LibraryScanner |
| L-007 | TV series/season/episode structure recognition from directory layout and filename | P0 | L | LibraryScanner |
| L-008 | Create or update media records from scan results; preserve existing records and all user state on re-scan | P0 | L | LibraryScanner |
| L-009 | Scan is idempotent: same filesystem state always produces same media records | P0 | M | LibraryScanner |
| L-010 | Scan trigger API endpoint (admin only); scan enqueued as background job | P0 | M | LibraryScanner |
| L-011 | Scan progress and completion status visible to administrators via API | P1 | M | LibraryScanner |
| L-012 | File-gone detection: mark media records unavailable when source file is missing on re-scan | P0 | M | LibraryScanner |

### EPIC: Media Records

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| L-013 | Persist movie record: title, year, file path, format info, stable GUID identifier | P0 | M | AppDev |
| L-014 | Persist TV series, season, and episode records with proper relationships | P0 | M | AppDev |
| L-015 | Persist personal media record: filename, path, format info, stable GUID identifier | P0 | M | AppDev |
| L-016 | All media records include creation and update timestamps | P0 | S | AppDev |

### EPIC: Library Access Control

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| L-017 | Per-user library access grants: administrators assign which users can access which libraries | P0 | M | AppDev |
| L-018 | All user queries for libraries and media items filtered to only accessible items | P0 | M | AppDev |
| L-019 | Authorization failures for hidden resources return `404`, not `403`, to avoid resource enumeration | P0 | S | AppDev |

### EPIC: User Management

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| L-020 | Administrator CRUD for user accounts: create, list, update, disable, remove | P0 | M | AppDev |
| L-021 | Administrator can grant and revoke the administrator role | P0 | S | AppDev |
| L-022 | Administrator deletion permission defaults to off; must be explicitly granted | P0 | S | AppDev |

### EPIC: Web Interface — Library Browse & Admin UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-004 | Library home: grid/list of accessible libraries | P0 | M | AppDev |
| W-005 | Library browse: paginated item grid with filtering and sorting controls | P0 | L | AppDev |
| W-013 | User management: list, create, edit, disable, delete users; assign roles and library access | P0 | L | AppDev |
| W-014 | Library management: create, edit, remove libraries; trigger scans | P0 | L | AppDev |
| W-019 | OIDC provider configuration UI | P0 | M | AppDev |

---

## Milestone v0.3.0 — Metadata, Artwork & Item Detail UI

### EPIC: Metadata Provider Framework

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-001 | Metadata provider interface: fetch by identifier or search term; return structured result | P0 | M | Metadata |
| M-002 | Provider configuration per library (which providers are active, API keys from environment) | P0 | M | Metadata |
| M-003 | Provider fallback chain: try providers in priority order; use first successful result | P1 | M | Metadata |
| M-004 | Provider result caching to avoid redundant external network calls | P1 | M | Metadata |

### EPIC: Movie Metadata

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-005 | Fetch and persist movie metadata: title, year, description, genres, cast, ratings, release info | P0 | M | Metadata |
| M-006 | Movie metadata match via filename/directory heuristics | P0 | M | Metadata |
| M-007 | Failed metadata fetch leaves existing record intact; records fetch status for retry | P0 | S | Metadata |

### EPIC: TV Metadata

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-008 | Fetch and persist TV series metadata: title, year, description, genres, cast, network | P0 | M | Metadata |
| M-009 | Fetch and persist season and episode metadata | P0 | M | Metadata |
| M-010 | TV metadata match via series name and episode identifiers parsed from directory/filename | P0 | M | Metadata |

### EPIC: Personal Media Metadata

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-013 | Personal media items work (browse, play) without any metadata beyond filename and path (already satisfied by v0.2.0 — continuing invariant, no new implementation) | P0 | S | Metadata |

### EPIC: Artwork & Thumbnails

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-014 | Fetch and store poster, backdrop, and logo artwork for movies and TV series | P0 | L | Metadata |
| M-015 | Generate video thumbnail (keyframe extraction via FFmpeg) for media items | P1 | M | Metadata |
| M-016 | Store artwork on configured volume mount; serve via image API endpoint | P0 | M | Metadata |
| M-017 | Missing artwork falls back gracefully; does not block browse or playback | P0 | S | Metadata |

### EPIC: Subtitles

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-018 | Discover sidecar subtitle files (SRT, VTT, ASS) adjacent to media file during scan | P0 | M | Metadata |
| M-019 | Serve subtitle files via compatible subtitle endpoint | P0 | M | AppDev |
| M-020 | Subtitle language and format stored in media record; returned in item detail | P0 | S | Metadata |

### EPIC: Metadata Refresh Background Job

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-021 | Administrator-triggered metadata refresh job (full library or single item) | P1 | M | Metadata |
| M-022 | Metadata refresh runs via background job queue; never blocks HTTP request processing | P0 | M | Metadata |

### EPIC: Item Detail API

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-023 | Item detail API endpoint: aggregate metadata, artwork URLs, cast, subtitles, and media stream info into enriched response | P0 | M | AppDev |

### EPIC: Web Interface — Item Detail UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-006 | Item detail page: metadata, artwork, playback button, subtitle and audio track options | P0 | L | AppDev |

---

## Milestone v0.4.0 — Client Compatibility Layer & Web Search

### EPIC: System & Capabilities Endpoints

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-001 | `GET /System/Info` — server name, version, OS info, startup date; verified via JFOracle | P0 | M | AppDev |
| C-002 | `GET /System/Info/Public` — unauthenticated minimal server info | P0 | S | AppDev |
| C-003 | `GET /Branding/Configuration` — server name, login disclaimer | P0 | S | AppDev |

### EPIC: User & Auth Compatibility Routes

> **Compatibility auth endpoints are not a third authentication mechanism.** Browser auth remains OIDC-only via `AddOpenIdConnect()` + `AddCookie()`. Jellyfin-compatible clients authenticate through the approved `MediaBrowser`/session-token compatibility path handled by `JellyfinTokenHandler`. Do not add browser username/password sign-in or additional authentication mechanisms.

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-004 | `POST /Users/AuthenticateByName` - Jellyfin compatibility authentication boundary; must use the approved Jellyfin/session-token model and must not introduce browser password sign-in or a third auth mechanism; response verified via JFOracle | P0 | M | AppDev |
| C-005 | `GET /Users/{id}` and `GET /Users/Me` — user profile DTO; field contract verified via JFOracle | P0 | M | AppDev |
| C-006 | `POST /Sessions/Logout` — sign-out and session revocation | P0 | S | AppDev |

### EPIC: Library & Browse Compatibility Routes

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-007 | `GET /Library/MediaFolders` — list accessible library roots; verified via JFOracle | P0 | M | AppDev |
| C-008 | `GET /UserViews` — user library views with keyset pagination and `TotalRecordCount`; query param `userId` (Guid, optional) | P0 | M | AppDev |
| C-009 | `GET /Items` — paginated item list with filtering, sorting, and field projection; keyset pagination | P0 | L | AppDev |
| C-010 | `GET /Items/{id}` — single item detail DTO; GUID string IDs; ticks for timestamps; verified via JFOracle | P0 | M | AppDev |
| C-011 | `GET /Shows/{id}/Seasons` and `GET /Shows/{id}/Episodes` — TV hierarchy endpoints | P0 | M | AppDev |

### EPIC: Image Compatibility Routes

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-012 | `GET /Items/{id}/Images/{imageType}` — serve poster, backdrop, logo, and thumbnail | P0 | M | AppDev |
| C-013 | Image resizing and quality parameters supported where clients request them | P1 | M | AppDev |

### EPIC: Search Compatibility

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-014 | `GET /Search/Hints` — returns matching items by search term; verified via JFOracle | P0 | M | AppDev |
| C-015 | Search results filtered to items the requesting user can access | P0 | S | AppDev |

### EPIC: WebSocket Compatibility

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-016 | WebSocket endpoint; message envelope: `{ "MessageType": "...", "MessageId": "<guid>", "Data": ... }` | P0 | M | AppDev |
| C-017 | KeepAlive and session-start WebSocket message types implemented | P0 | S | AppDev |

### EPIC: Compatibility Test Suite

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| C-018 | Define v0.4.0 supported-client compatibility scope: `jellyfin-web` (Browser), `jellyfin-android` (Android), `jellyfin-ios` (iOS) — all at latest stable release. | P0 | S | PM + Protocol |
| C-019 | Automated compatibility test suite: auth, browse, item detail, image endpoints | P0 | L | TestGen |
| C-020 | All covered endpoints verified against Jellyfin reference via JFOracle before milestone ships | P0 | M | Protocol |

### EPIC: React Web Interface — Search

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-008 | React search bar with access-filtered results | P1 | M | AppDev |

### EPIC: Personal Media Metadata (deferred from v0.3.0)

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| M-011 | Read embedded metadata from media file (title, date, duration) when available | P0 | M | Metadata |
| M-012 | Read sidecar NFO/XML metadata for personal media when present | P1 | M | Metadata |

### EPIC: OIDC Claims-Driven User Roles

> **Design decision (to confirm):** Roles are driven exclusively by OIDC claims. Manual role override in the UI is removed. If a manual fallback is needed later, it must be designed with explicit conflict-resolution rules (claim vs. manual precedence). V1 is claims-only.

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| R-001 | Domain: make `User.IsAdmin` settable (from init-only) with self-demotion guard in domain logic | P0 | S | AppDev |
| R-002 | Configuration: add `VENTUS_OIDC_ADMIN_GROUP` and `VENTUS_OIDC_USER_GROUP` environment variables with defaults `ventus_admin` / `ventus_user` | P0 | S | AppDev |
| R-003 | OIDC event handler: read group membership claims on sign-in; map configured group names to Ventus roles; update user record when role changes | P0 | M | AppDev |
| R-004 | First OIDC user auto-admin invariant preserved: setup-created user is not downgraded by claims evaluation | P0 | S | AppDev |
| R-005 | API DTO: `UserResponse` includes `IsAdmin` and a `RoleSource` field (`"Claims"` / `"Setup"`); remove manual role-assignment endpoint | P0 | M | AppDev |
| R-006 | Route handler: remove admin-only `PATCH /api/users/{id}/role` (or equivalent manual toggle); role changes only via claims | P0 | S | AppDev |
| R-007 | Self-demotion protection: `User.SetAdminRole(false)` throws domain exception when the calling user is the same user | P0 | S | AppDev |
| R-008 | Audit log: record role changes from claims evaluation with timestamp, user identity, old role, new role, and source (`Claims`) | P0 | S | AppDev |
| R-009 | Frontend: user management table shows role as read-only badge with claims-derived source indicator; remove admin toggle control | P0 | M | AppDev |
| R-010 | Frontend TypeScript types: update generated `UserResponse` to include `isAdmin: boolean` and `roleSource: "Claims" | "Setup"` | P0 | S | AppDev |
| R-011 | Unit + integration tests: claims evaluation, self-demotion rejection, first-user auto-admin preservation, sign-in role re-evaluation | P0 | M | TestGen |

---

## Milestone v0.5.0 — Playback & Player UI

### EPIC: Direct Playback

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| P-001 | `GET /Videos/{id}/stream` — serve media file directly for capable clients | P0 | M | AppDev |
| P-002 | Range request support (byte-range header handling) for direct playback | P0 | M | AppDev |
| P-003 | Library access check enforced before any media bytes are served | P0 | S | AppDev |

### EPIC: PlaybackInfo & DeviceProfile

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| P-004 | `POST /Items/{id}/PlaybackInfo` — evaluate DeviceProfile; return stream decision; verified via JFOracle | P0 | L | PlaybackDecision |
| P-005 | DeviceProfile parsing from client request payload | P0 | M | PlaybackDecision |
| P-006 | Direct/transcode/remux decision algorithm verified against Jellyfin reference behavior | P0 | M | PlaybackDecision + Protocol |

### EPIC: Transcoding (FFmpeg/HLS)

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| P-007 | FFmpeg process management: spawn, monitor, and terminate transcode sessions | P0 | L | Transcoding |
| P-008 | HLS playlist generation and segment serving at `/Videos/{id}/hls1/{playlistId}/{segId}.{container}` | P0 | L | Transcoding |
| P-009 | FFmpeg argument builder: structured argument vector; never assembled as a shell command string | P0 | M | Transcoding |
| P-010 | All user-controlled values (codec, bitrate, resolution) allowlisted before use in FFmpeg arguments | P0 | M | Transcoding |
| P-011 | Transcode session cleanup: terminate FFmpeg and remove temp segments on stop | P0 | M | Transcoding |
| P-012 | Resource bounding: configurable limit on concurrent transcode sessions per user and globally | P0 | M | Transcoding |

### EPIC: Playback Session Management

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| P-013 | Create playback session on PlaybackInfo; associate with user and device | P0 | M | AppDev |
| P-014 | `POST /Sessions/Playing/Progress` — update playback position and buffering state per user | P0 | M | AppDev |
| P-015 | `DELETE /Sessions/Playing` — mark session ended | P0 | S | AppDev |
| P-016 | Resume state: persist last position per user per item; returned in item detail response | P0 | M | AppDev |
| P-017 | Watched status: mark item watched when playback reaches configured completion threshold | P0 | S | AppDev |
| P-018 | Admin: list active playback sessions; force-terminate a session | P1 | M | AppDev |

### EPIC: Favourites

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| P-019 | `POST /Users/{id}/FavoriteItems/{itemId}` and `DELETE /Users/{id}/FavoriteItems/{itemId}` — add/remove favourite | P0 | S | AppDev |
| P-020 | Favourites reflected in item detail DTO; filterable in browse queries | P0 | S | AppDev |

### EPIC: Web Interface — Player UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-007 | Video player with play/pause, seek, resume from position, subtitle selection, audio track selection | P0 | XL | AppDev |
| W-009 | Continue watching and recently added sections on home screen | P1 | M | AppDev |

---

## Milestone v0.6.0 — Web Interface Completion

### EPIC: User Profile UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-010 | User profile page: display name, avatar, active sessions | P1 | M | AppDev |
| W-011 | Watch history list | P1 | M | AppDev |
| W-012 | Favourites list | P1 | M | AppDev |

### EPIC: Admin Panel UI — Completion

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-015 | Active playback sessions view: list current sessions; terminate a session | P1 | M | AppDev |
| W-018 | System health and configuration view | P1 | M | AppDev |

---

## Milestone v0.7.0 — Background Work, Observability & Jobs UI

### EPIC: Background Job Queue

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| B-001 | Approved durable background job queue: enqueue, process, and retry on transient failure | P0 | L | AppDev |
| B-002 | Job types registered: library scan, metadata refresh, thumbnail generation, playback cleanup, maintenance | P0 | M | AppDev |
| B-003 | Job execution is idempotent: re-running the same job on the same state produces the same result | P0 | M | AppDev |
| B-004 | Jobs support cooperative cancellation via cancellation token or equivalent mechanism | P0 | M | AppDev |

### EPIC: Job Visibility

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| B-005 | Job list API: active, pending, and recent failed jobs with status, progress, and error details | P0 | M | AppDev |
| B-006 | Job cancellation API: administrator can cancel a running or queued job | P1 | S | AppDev |
| B-007 | Recent job failures retained for administrator review; cleared on subsequent success | P1 | S | AppDev |

### EPIC: Graceful Shutdown

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| B-008 | SIGTERM handler: drain in-flight HTTP requests within StopTimeout (default 10 s) | P0 | M | AppDev |
| B-009 | SIGTERM handler: flush structured logs and release FFmpeg processes on shutdown | P0 | M | AppDev |
| B-010 | SIGTERM handler: send cooperative cancellation to running background jobs | P0 | M | AppDev |

### EPIC: Observability

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| B-011 | Metrics endpoint in approved format; key metrics: request rate, error rate, active transcode sessions, job queue depth | P0 | M | AppDev |
| B-012 | Distributed trace spans for HTTP requests and FFmpeg invocations | P0 | M | AppDev |
| B-013 | Log verbosity configurable per environment without code changes | P0 | S | AppDev |

### EPIC: Audit Log

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| B-014 | Audit log query API: filter by user, action type, and time range | P1 | M | AppDev |
| B-015 | Admin UI: paginated audit log viewer | P1 | M | AppDev |

### EPIC: Web Interface — Background Jobs UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-016 | Background jobs view: list jobs with status, progress, and recent failures; support cancellation | P1 | M | AppDev |

---

## Milestone v0.8.0 — Plugin System & Plugin UI

### EPIC: Plugin Contract & Loader

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| PL-001 | Plugin contract: init, start, and graceful-stop phases with timeout and cancellation support | P0 | L | Plugin |
| PL-002 | Plugin discovery: load from `/plugins` volume mount at startup | P0 | M | Plugin |
| PL-003 | Plugin isolation: a faulting plugin must not crash the main process | P0 | L | Plugin |
| PL-004 | Plugins access only approved host capabilities exposed through the plugin contract | P0 | M | Plugin |

### EPIC: Plugin Configuration & State

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| PL-005 | Plugin configuration persisted per plugin; survives container upgrades and restarts | P0 | M | Plugin |
| PL-006 | Plugin configuration API: read and update plugin-specific config | P1 | M | Plugin |

### EPIC: Plugin Administration

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| PL-007 | Plugin status API: list plugins with version, enabled state, and last error | P0 | M | Plugin |
| PL-008 | Enable, disable, and remove plugin actions; each action logged to audit | P1 | M | Plugin |

### EPIC: Web Interface — Plugin Management UI

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| W-017 | Plugin management: list plugins, enable/disable, configure, view status | P1 | L | AppDev |

---

## Milestone v0.9.0 — Multi-Container & Scale

### EPIC: Multi-Container Compose

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| S-001 | Docker Compose profiles: `single` (default) and `multi`; switch modes without code changes | P0 | M | Scaffold |
| S-002 | Separate Compose services: API, Transcoding, LibraryScanner, Metadata | P0 | L | Scaffold |
| S-003 | Shared network and reverse-proxy configuration for multi-container mode | P0 | M | Scaffold |

### EPIC: Distributed State

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| S-004 | Session state stored in approved durable distributed store; never only in-process memory | P0 | L | AppDev |
| S-005 | Playback progress state readable across container replicas | P0 | M | AppDev |

### EPIC: Pagination & Rate Limiting

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| S-006 | Keyset/cursor pagination on all large-collection endpoints (`/Items`, `/Users/{id}/Views`, etc.); never offset-based | P0 | L | AppDev |
| S-007 | Rate limiting on transcoding session creation (per user and global ceiling) | P0 | M | AppDev |
| S-008 | Rate limiting on library scan trigger endpoint | P0 | S | AppDev |

### EPIC: Compatibility Parity

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| S-009 | Full compatibility test suite passes in multi-container mode; results must match single-container | P0 | M | TestGen |

---

## Milestone v1.0.0 — Feature-Complete Release

### EPIC: Full Compatibility Validation

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| R-001 | Run complete Jellyfin compatibility test suite for all declared supported clients | P0 | L | Protocol |
| R-002 | Formally declare v1.0.0 compatibility scope: client names, platforms, minimum versions | P0 | M | PM + Protocol |

### EPIC: Security Review

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| R-003 | Security audit covering OWASP Top 10 and Ventus-specific risk areas | P0 | L | Security |
| R-004 | All open high-severity security findings resolved before release | P0 | M | AppDev |

### EPIC: Documentation

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| R-005 | Operator setup guide: Docker Compose, volumes, environment variables, first-run | P0 | L | Docs |
| R-006 | API contract documentation for all compatibility-boundary endpoints | P0 | L | Docs |
| R-007 | Upgrade guide: data migration path from prior Ventus versions | P1 | M | Docs |

### EPIC: Performance Validation

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| R-008 | Define performance baseline: concurrent streams, library size, and API latency targets | P1 | M | PM + Design |
| R-009 | Run load test against defined baseline; document results | P1 | L | AppDev |

### EPIC: Coverage & Quality Gate

| ID | Story | Priority | Scope | Agent |
|---|---|---|---|---|
| R-010 | 80% line coverage gate passes on all core domain and application logic projects | P0 | M | Validate |
| R-011 | All v0.x items at P0 complete; no open P0 defects | P0 | M | Validate |
