---
description: "Use when: writing or reviewing HTTP endpoints, payload contracts, request processing, Jellyfin compatibility, route design, auth headers, error responses, health endpoints, or an approved API contract"
applyTo: "**/*api*,**/*route*,**/*endpoint*"
---

# API And Compatibility Conventions

## Technology Boundary
- No web framework, request-handler style, configuration library, rate-limit middleware, or API specification tool is selected by this file
- Follow the approved API design and selected implementation technology after those decisions exist

## Error Responses
- Error responses use the approved machine-readable client contract; use RFC 7807 only if selected or externally required
- Request-handler composition, common routing behavior, and input validation follow the approved API design

## Jellyfin Client Compatibility (non-negotiable)
- Current stable official Jellyfin clients and newer stable official client releases must work out-of-the-box — no client-side changes ever required
- Compatibility gaps are defects
- Auth header scheme **must** be `MediaBrowser` — clients hardcode this:
  ```
  Authorization: MediaBrowser Client="...", Device="...", DeviceId="...", Version="...", Token="..."
  ```
- All seek/position timestamps are in **ticks** (100 ns intervals) — never seconds or milliseconds
- Item IDs are **GUID strings** — never integers
- Paginated responses must include `TotalRecordCount`
- HLS segment route must match exactly: `/Videos/{itemId}/hls1/{playlistId}/{segId}.{container}`
- WebSocket envelope: `{ "MessageType": "...", "MessageId": "<guid>", "Data": ... }`
- When in doubt, match Jellyfin's exact response shape — do not invent fields or omit expected ones

## Routes
- No API versioning in URL segments — use Jellyfin-compatible routes as-is (no `/v1/`, `/v2/`)
- Validate new endpoints against Jellyfin source behaviour via `@JFOracle` before shipping

## Contract Recording
- Define or update the canonical client-facing contract before implementing a new endpoint
- Document every intended HTTP status response in the approved contract format and source-level API metadata when supported
- OpenAPI is the canonical contract for the Ventus-native React web API. Commit its reviewed source at the approved documentation path and keep generated TypeScript clients in sync.
- Do not commit generated local contract output unless it is designated as the canonical reviewed source

## Health Endpoints (unauthenticated — both must remain open)
- `GET /health` — liveness: `200` if the process is alive (used by Docker `HEALTHCHECK`)
- `GET /health/ready` — readiness: `200` only when required persistence migrations are complete and the library index is warm; `503` during startup

## Configuration
- Validate configuration at startup and expose domain-meaningful configuration using the approved application framework
- Secrets via environment variables or Docker secrets — never committed to source

## Rate Limiting
- Rate-limit expensive endpoints (transcoding session creation, library scan trigger) through the approved request-processing design
