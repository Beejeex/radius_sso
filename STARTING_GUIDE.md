# Ventus - Starting Guide

## Purpose

This guide defines the product outcomes Ventus should satisfy. It describes what Ventus is expected to provide without prescribing code, project structure, architecture, implementation tools, or technical design choices.

## Product Goal

Ventus is a Docker-only, self-hosted media server for people who want to manage and stream their own movies, TV series, and personal media from infrastructure they control.

Ventus should feel reliable for everyday watching, clear for administrators to operate, and careful with original playable media files.

## Product Principles

- Ventus is an independent product with its own vocabulary, behavior, and product direction.
- Original playable media files remain externally owned content and must not be renamed, moved, overwritten, or deleted by Ventus.
- Supported clients should be able to browse, search, and play media without needing special client-side changes.
- Administration should be understandable, auditable, and recoverable.
- Security-sensitive behavior should be explicit, consistent, and respectful of privacy.
- Operators should be able to understand the health and activity of the system.
- Upgrades and replacements should not cause loss of media, configuration, application data, metadata, plugin configuration, or playback state.

## Core Product Experience

Ventus provides:

- Media library management for movies, TV series, and personal media.
- Library scanning that discovers media and keeps application records current.
- Metadata, artwork, thumbnails, subtitles, and other support data for media discovery and playback.
- User accounts, sessions, device awareness, and a recovery path for administrative access.
- Library access control so users only see and play media they are allowed to access.
- A Ventus web interface for users and administrators.
- Playback support across devices and network conditions.
- Resume state, favourites, search, and personal playback history.
- Administrative views for users, libraries, playback activity, plugins, background work, configuration, and system health.
- Plugin hosting with visible lifecycle, status, configuration, and administrative control.

## Web Interface

- Ventus provides its own web interface as a first-class product experience.
- Users can browse libraries, search media, view details, play media, manage their own playback state, and continue watching from the web interface.
- Administrators can manage users, sessions, libraries, library access, plugins, configuration, background work, and system status from the web interface.
- The web interface should make normal media use feel direct and understandable without requiring a compatible third-party client.
- The administrative experience should make setup, recovery, configuration, monitoring, and common maintenance tasks clear to operators.
- The web interface and compatible client boundary should follow the same access rules and media-safety expectations.

## Media Libraries

- Libraries support movies, TV series, and personal media.
- Administrators can create, update, remove, and review libraries.
- Library scans should be repeatable and should not corrupt existing media records or user state.
- Media records should support browsing, search, metadata display, playback, and compatibility behavior.
- Personal media should work even when rich third-party metadata is unavailable.
- Movie and TV libraries should support rich metadata and artwork from configured providers.
- Users should only see libraries, media items, metadata, images, subtitles, and playback options they are allowed to access.

## Shared Libraries And User State

- Media libraries are shared across Ventus users.
- Users can browse and play a shared library only when they have access to it.
- Library media records, metadata, artwork, subtitles, and generated support data are shared library state.
- User-specific state is stored per user and must not affect other users.
- User-specific state includes resume position, watched status, favourites, playback history, and active sessions.
- Updating one user's play state must not change another user's play state for the same media item.

## Media Safety

- Original playable media files are read-only to Ventus.
- Configuring a media location as a Ventus library grants Ventus permission to read, scan, index, and serve allowed media, but does not grant ownership of the original playable media files.
- Normal users can never delete original playable media files through Ventus.
- Administrative deletion or removal actions apply only to Ventus-managed records, generated assets, support files, metadata, artwork, thumbnails, and application state.
- Removing a library, media item, metadata record, artwork record, user state record, or application reference must not imply deletion of the original playable media file.
- Destructive administrative actions require confirmation and should be visible in audit history.
- Ventus may manage non-playable support files only when configuration and permissions allow it.
- Failed cleanup or maintenance work should leave data in a recoverable state.

## Identity And Access

- Ventus supports external identity providers for normal sign-in.
- Ventus includes a first-run setup mode for initial OIDC provider configuration when no provider exists.
- Sessions are tied to users and devices.
- Sign-out, session expiry, and session revocation are required.
- Administrative operations require an administrator.
- Administrators must have an explicit deletion permission before performing removal actions inside Ventus.
- The administrator deletion permission defaults to off.
- Authorization failures should not reveal whether hidden resources exist.

## First-Run Setup Mode

- On first deployment, Ventus enters setup mode when no validated OIDC provider exists.
- Operators navigate to `/setup` and configure the OIDC authority, client ID, and client secret.
- No setup key is required; initial exposure is controlled operationally by installing Ventus before publishing it externally.
- Once the provider is saved, restart Ventus so the native `Oidc` scheme is registered.
- Normal OIDC sign-in then becomes the only browser authentication path.

## Client Compatibility

- Jellyfin client compatibility exists at the external client boundary.
- Supported official Jellyfin clients should work without client-side changes.
- The compatibility scope for each Ventus release should identify supported client names, platforms, and baseline versions.
- Compatibility covers sign-in, session flow, user identity, library listing, browsing, item detail, images, subtitles, playback information, playback start, playback progress, resume state, favourites, search, and graceful handling of unavailable server features.
- Compatibility gaps for supported clients are treated as product defects.
- Compatibility behavior must not make Jellyfin concepts the Ventus product model.
- Third-party clients may work when their behavior matches the supported compatibility surface.

## Playback

- Ventus supports direct playback when client, media, and network conditions allow it.
- Ventus supports playback adaptation when direct playback is not enough.
- Playback should preserve resume state and playback progress.
- Playback should enforce access rules before media content is served.
- Administrators should be able to understand active playback sessions and playback failures.
- Expensive playback work should be bounded so one user or client cannot exhaust shared resources.

## Background Work

- Long-running work should not make the product feel stalled or unresponsive.
- Background work includes library scans, metadata refreshes, thumbnail generation, playback cleanup, and maintenance.
- Background work should be visible to administrators.
- Work can fail without corrupting persisted media, metadata, or user state.
- Retry and cancellation behavior should be understandable to operators.
- Shutdown should complete gracefully where possible.

## Configuration And Deployment

- Ventus is deployed through containers only.
- Configuration should be understandable to operators.
- Secrets must not be exposed in repository content, user-facing errors, administrative views, or operational output.
- Missing or invalid required configuration should be clear to operators.
- Persistent data must survive upgrades and replacements.
- Ventus should behave predictably in common self-hosted and reverse-proxied environments.

## Scalability And Operating Modes

- Ventus supports a simple single-container operating mode where the full product can run together.
- Ventus also supports larger deployments where operating responsibilities can be split across multiple containers.
- Single-container mode remains a first-class product experience.
- Multi-container deployments should increase capacity or operational flexibility without changing the client experience.
- Core features should work in single-container mode unless an accepted specification explicitly defines a scale-only capability.
- Operators should be able to start simple and grow the deployment without changing the product model.

## Administration And Operations

- Administrators can manage users, sessions, libraries, library access, plugins, configuration, background work, and system status.
- Administrators can configure and repair identity-provider settings from the administrative experience.
- Recovery access is intended for setup and emergency administration, not normal everyday use.
- System health and readiness should be visible to operators.
- Operators should be able to diagnose requests, playback sessions, background jobs, startup failures, and administrative actions.
- Operational signals should help administrators understand system load, playback behavior, background work, and service dependencies.
- Security-sensitive events should be audit logged.
- Recent background job failures should be visible to administrators.

## Client And Error Behavior

- Client-facing behavior should be consistent and predictable across first-party, administrative, and compatible client surfaces.
- Errors should be useful without exposing secrets or hidden resource state.
- Validation failures should identify the problem without revealing sensitive values.
- Large collections should be browsable without forcing clients to load everything at once.
- Repeated client retries should not create duplicate destructive effects.
- Backward-incompatible client behavior changes should be intentional and documented.

## Plugins

- Plugins have an explicit lifecycle.
- Plugin configuration is persisted.
- Plugin status is visible to administrators.
- Plugin administrative actions are controlled and auditable.
- Plugin behavior should not leak implementation details into the core Ventus product model.

## Documentation Boundaries

- Product, domain, UX, compatibility, and operational specifications live in the project documentation.
- Specifications should state their status clearly as draft, in review, accepted, or superseded.
- Accepted specifications should describe desired outcomes and constraints without prescribing implementation details unless an approved design artifact owns that decision.
- Project state should record the current phase, active work, accepted specifications, open questions, blockers, risks, and next steps.
- Architectural and implementation decisions belong in accepted design artifacts or decision records, not in this starting guide.
