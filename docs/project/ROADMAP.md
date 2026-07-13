# Ventus — Roadmap

> **Status:** Active — greenfield
> **Last updated:** 2026-06-23
> **Scope:** Milestones from first deployable build through the initial feature-complete release. The web stack is resolved by ADR-005 and ADR-011: future web interface items build on React/Vite.

---

## Vision

Ventus is a production-ready, Docker-native, self-hosted media server with full Jellyfin client compatibility at its external boundary, OIDC-first identity, and a first-class web interface. It is an independent product with its own domain vocabulary, architecture, and operating model.

---

## Milestone Summary

| Milestone | Name | Goal |
|---|---|---|
| v0.0.1 | Project Foundation | Deployable, healthy, configurable empty server |
| v0.1.0 | Identity, Access & UI Shell | Secure sign-in, session and device lifecycle, and a thin web interface for sign-in flows |
| v0.2.0 | Media Library Core & Browse UI | Libraries, scanning, media records, access control, library browse, and admin panel |
| v0.3.0 | Metadata, Artwork & Item Detail UI | Rich discovery content and item detail pages in the web interface |
| v0.4.0 | Client Compatibility Layer & Web Search | Supported Jellyfin clients can connect and browse; React web search is functional; OIDC claims-driven user roles replace manual toggling |
| v0.5.0 | Playback & Player UI | Full playback pipeline and in-browser video player |
| v0.6.0 | Web Interface Completion | User profile, watch history, favourites, and remaining admin views |
| v0.7.0 | Background Work, Observability & Jobs UI | Long-running operations visible, bounded, graceful; admin jobs and audit log views |
| v0.8.0 | Plugin System & Plugin UI | Isolated plugin hosting and plugin management interface |
| v0.9.0 | Multi-Container & Scale | Operators can split workloads across containers |
| v1.0.0 | Feature-Complete Release | Fully validated, documented, production-ready release |

---

## Milestone Details

### v0.0.1 — Project Foundation

**Goal:** A container that starts, reports health, loads and validates configuration, and can handle authenticated HTTP requests using the Ventus/Jellyfin auth header scheme. No business logic yet.

**Done when:**
- Container starts from `docker compose up` with a minimal environment file
- `GET /health` returns `200` (liveness) without authentication
- `GET /health/ready` returns `503` when persistence is not ready and `200` when ready
- Configuration is validated at startup; missing required values produce a clear error and non-zero exit
- Structured JSON logs are written to stdout
- Auth header parsing accepts the `MediaBrowser` scheme
- All builds and tests pass inside the container

**Blocked by:** Technology ADRs (language/runtime, web framework, persistence engine, logging abstraction)

---

### v0.1.0 — Identity, Access & UI Shell

**Goal:** Users and administrators can sign in securely; sessions, devices, and auth events have full lifecycle management. A thin web interface shell provides working first-run setup, OIDC sign-in, and sign-out flows so the web interface can grow alongside each subsequent feature milestone.

**Done when:**
- OIDC sign-in flow works end-to-end with a configured external identity provider
- First-run setup mode supports initial OIDC provider configuration without a setup key
- Sessions are created, persisted, expired, and revoked correctly
- Devices are registered and associated with sessions
- Auth events (login, token issue, revoke, failure, setup completion) are audit logged
- Session revocation and sign-out enforce access immediately
- Compatibility-boundary auth header (`MediaBrowser` scheme) is validated on protected routes
- Web interface: OIDC sign-in page routes through the authorization code flow end-to-end
- Web interface: first-run setup page configures the initial OIDC provider and asks for restart
- Web interface: sign-out and session expiry redirect to sign-in correctly

**Blocked by:** v0.0.1; Persistence ADR; Security design for OIDC flow; Web interface technology ADR

---

### v0.2.0 — Media Library Core & Browse UI

**Goal:** Administrators can create and manage Movie, TV, and Personal libraries, trigger scans, and control user access. Media records are persisted. Original media files are never modified. Users can browse accessible libraries through the web interface; administrators can manage users, libraries, and OIDC configuration from an admin panel.

**Done when:**
- Administrators can create, update, and remove library definitions (Movie, TV, Personal)
- Library scanning discovers media files and creates/updates media records
- Scans are idempotent; re-running does not corrupt existing records or user state
- Library access control assigns per-user access to specific libraries
- Users cannot see or access libraries or media items they do not have permission to see
- Administrative removal of a library removes only Ventus-managed records; original files are untouched
- User management: administrators can create, update, disable, and remove user accounts
- Web interface: library home shows accessible libraries
- Web interface: library browse shows a paginated item grid with filtering and sorting controls
- Web interface: administrator user management page supports create, list, edit, disable, and remove actions
- Web interface: administrator library management page supports create, edit, remove, and scan-trigger actions
- Web interface: administrator can configure OIDC providers

**Blocked by:** v0.1.0; Schema design for media records; LibraryScanner design

---

### v0.3.0 — Metadata, Artwork & Item Detail UI

**Goal:** Movie and TV library items display rich metadata and artwork from configured providers, plus sidecar subtitles discovered during scan. Personal media works without third-party metadata (satisfied by v0.2.0 baseline). Users can view full item detail pages in the web interface with artwork, metadata, and an active playback entry point.

**Done when:**
- Configurable metadata providers supply title, description, cast, genres, ratings, and release date for Movies and TV
- Artwork (poster, backdrop, logo) and thumbnails are fetched, stored, and served
- Subtitles are discovered (sidecar files) and served
- Personal media items work without third-party metadata (already satisfied by v0.2.0; continuing invariant)
- Item detail API endpoint aggregates metadata, artwork URLs, cast, subtitles, and media stream info into enriched response
- Background metadata refresh job runs without blocking requests
- Failed metadata fetch does not corrupt existing media records
- Web interface: item detail page shows title, description, artwork, cast, genres, available subtitle and audio options, and a playback button (player delivered in v0.5.0)

**Blocked by:** v0.2.0; Metadata provider design; Background job queue design (prerequisite from v0.7.0 scope, needed here)

---

### v0.4.0 — Client Compatibility Layer & Web Search

**Goal:** Supported Jellyfin clients can authenticate, browse libraries, and view item details without client-side changes. React web search is functional. Personal media metadata is enriched from embedded and sidecar sources.

**Done when:**
- System info and server capabilities endpoints respond correctly
- User/session auth routes match Jellyfin protocol
- Library listing and browse endpoints return Jellyfin-compatible payloads
- Item detail endpoints return complete, field-accurate compatibility DTOs
- Image serving routes work for all covered item types
- Search returns relevant results
- WebSocket connection with correct message envelope works
- A defined set of supported clients (named in the compatibility scope) has passed basic browse and authentication verification
- Personal media metadata reads embedded metadata (title, date, duration) from media files
- Personal media metadata reads sidecar NFO/XML metadata when present
- React web interface: search bar and results page are functional and return access-filtered results

**Blocked by:** v0.3.0; Protocol verification via JFOracle for each endpoint group; Compat agent sign-off

### OIDC Claims-Driven User Roles

**Goal:** User roles (administrator vs. regular user) are driven by OIDC claims — specifically group membership — rather than manual role toggling. The first OIDC user during setup is auto-assigned administrator; all subsequent users receive roles from claims. Self-demotion is prevented.

**Done when:**
- OIDC event handler maps group membership claims to Ventus user roles: membership in the configured admin group → administrator role; membership in the configured user group → regular user role
- Group names are configurable via environment variable (default: `ventus_admin` / `ventus_user`)
- First OIDC user during setup retains auto-admin behavior (already implemented; preserved invariant)
- Role assignment is re-evaluated on each sign-in; stale roles are corrected
- An administrator cannot remove their own administrator role (self-demotion protection)
- User management API reflects claims-derived role; manual role toggling is removed from the API and UI
- Frontend user management page shows role as read-only with an indicator of its claims-derived source
- Audit log records role changes from claims evaluation

**Blocked by:** v0.3.0

---

### v0.5.0 — Playback & Player UI

**Goal:** Users can play media through direct or transcoded paths; playback state is preserved and enforced. The web interface includes a fully functional in-browser video player with resume, seek, subtitle selection, and audio track selection.

**Done when:**
- Direct playback serves media files to capable clients
- PlaybackInfo endpoint evaluates DeviceProfile and returns correct stream decision
- FFmpeg-backed HLS transcoding produces segments at `GET /Videos/{id}/hls1/{playlistId}/{segId}.{container}`
- Playback sessions are created, tracked, and terminated
- Progress, resume position, and watched status are stored per user
- Favourites can be added and removed
- Access rules are enforced before any media content is served
- Resource bounding limits concurrent transcoding sessions per user and globally
- Administrators can see and terminate active playback sessions
- Web interface: video player with play/pause, seek, resume from last position, subtitle track selection, and audio track selection
- Web interface: home screen includes continue watching and recently added sections

**Blocked by:** v0.4.0; Transcoding design (FFmpeg argument structure, HLS pipeline); PlaybackDecision design

---

### v0.6.0 — Web Interface Completion

**Goal:** The Ventus web interface provides a complete user and administrator experience: user profile management, watch history, favourites, active session visibility, and core system health view.

**Done when:**
- Web interface: user profile page shows display name, avatar, and active sessions
- Web interface: watch history list is visible and manageable by the user
- Web interface: favourites list is visible and manageable by the user
- Web interface: administrator can view and force-terminate active playback sessions
- Web interface: system health and configuration overview is accessible to administrators
- Web interface enforces the same access rules as the compatibility boundary throughout all pages

**Blocked by:** v0.5.0

---

### v0.7.0 — Background Work, Observability & Jobs UI

**Goal:** Long-running operations are queued, visible, bounded, retry-safe, and gracefully shut down. Administrators can view and manage background jobs and query the audit log through the web interface.

**Done when:**
- Library scan, metadata refresh, thumbnail generation, and playback cleanup run as background jobs
- Job status, progress, and recent failures are visible to administrators
- Jobs can be cancelled by administrators
- Failed jobs leave persisted data in a recoverable state
- Container shuts down gracefully on SIGTERM: drains in-flight requests, flushes logs, releases FFmpeg processes within 10 s
- Structured metrics and traces are emitted in the approved format
- Audit log is queryable by administrators
- Web interface: background jobs view shows active, pending, and recently failed jobs with progress and error details; supports job cancellation
- Web interface: paginated audit log viewer supports filter by user, action type, and time range

**Blocked by:** v0.6.0; Observability design (metrics/tracing approach)

---

### v0.8.0 — Plugin System & Plugin UI

**Goal:** Third-party behavior can be loaded, configured, and managed through a stable, isolated plugin contract. Administrators can manage plugins through the web interface.

**Done when:**
- Plugins are loaded from `/plugins` volume mount at startup
- Plugin lifecycle has explicit init, start, and graceful-stop phases with timeout/cancellation
- Plugin configuration is persisted and survives upgrades
- Plugin status and failures are visible to administrators
- A faulting plugin cannot crash the main process
- Plugin administrative actions (enable, disable, remove) are auditable
- Web interface: plugin management page lists plugins with version, enabled state, and last error; supports enable, disable, remove, and configuration actions

**Blocked by:** v0.7.0; Plugin contract design; Isolation mechanism ADR

---

### v0.9.0 — Multi-Container & Scale

**Goal:** Operators can split API, transcoding, scanning, and metadata workloads across containers without changing the product model.

**Done when:**
- Docker Compose profile separates single-container and multi-container modes
- All shared state (sessions, progress) lives in durable distributed store, not in-process memory
- API, Transcoding, LibraryScanner, and Metadata containers communicate over defined network interfaces
- Keyset/cursor pagination is implemented on all large-collection endpoints
- Rate limiting is applied to transcoding session creation and library scan triggers
- Multi-container deployment passes the same compatibility tests as single-container

**Blocked by:** v0.8.0; Distributed state design; Inter-service communication design

---

### v1.0.0 — Feature-Complete Release

**Goal:** A production-ready, fully validated, and documented release that any operator can run confidently.

**Done when:**
- All v0.x milestones are complete and tested
- All defined supported Jellyfin clients pass the full compatibility test suite
- Personal media experience is complete and documented
- Security review is complete with no open high-severity findings
- Operator setup and configuration guide is published
- API contract is documented and reviewed
- Compatibility scope for v1.0.0 is formally declared (client names, platforms, versions)
- Performance baseline is validated under representative load
- 80% line coverage gate passes on all core domain and application logic projects

**Blocked by:** v0.9.0; All prior milestones complete

---

## Guiding Constraints

- Every unresolved technology decision remains neutral until an ADR resolves it. The active web frontend is React/Vite per ADR-005 and ADR-011; future web milestones continue on that stack unless a superseding ADR is accepted.
- No milestone implementation may start until the required SPEC, ADR, and schema documents exist or are updated for the work
- Jellyfin client compatibility is an external surface constraint, not a core product model
- Media safety (read-only original files) is enforced from v0.2.0 and never regressed
- OIDC is the normal browser identity path; first-run setup is allowed until one enabled, validated OIDC provider exists
- Docker-only deployment is maintained throughout; no bare-metal or IIS paths
- The 80% line coverage gate applies from v0.2.0 onward for all code shipped in that milestone
