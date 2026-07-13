# Development Guide

All builds, tests, and migrations run **inside Docker containers**. No host-side .NET SDK, `dotnet` CLI,
or database installation is required.

---

## Prerequisites

| Tool | Minimum version | Notes |
|---|---|---|
| Docker Engine | 24.0 | With Compose V2 (`docker compose`) |
| Git | Any recent | For commit hooks |
| Git Bash or WSL | — | Required on Windows to execute `.sh` hook scripts |

---

## First-Time Setup

### 1. Copy environment configuration

```bash
cp .env.example .env
```

Edit `.env` and set a strong value for `VENTUS_DB_PASSWORD`. This file is git-ignored and
must never be committed.

### 2. Activate commit-message hooks

On Linux / macOS / WSL / Git Bash:

```bash
bash scripts/install-hooks.sh
```

On Windows with PowerShell, the hooks directory is already registered via `git config core.hooksPath .githooks`.
Git for Windows will invoke the hook automatically when it processes `.githooks/commit-msg`.

> **Note:** Hooks use Bash. If your commit environment does not support Bash scripts,
> enforce the [Conventional Commits](https://www.conventionalcommits.org/) format manually.

---

## Building

All build commands use the `builder` service defined in `docker-compose.dev.yml`,
which mounts your workspace into the SDK container. NuGet packages are cached in a
named Docker volume (`nuget_cache`) across runs.

### Build the solution

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder build Ventus.slnx
```

### Build in Release configuration

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder publish src/Ventus.Api/Ventus.Api.csproj \
  --configuration Release --output /tmp/publish
```

### Build the production container image

```bash
docker compose build app
```

---

## Running Tests

### Run all tests

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder test Ventus.slnx --logger "console;verbosity=normal"
```

### Run a specific project's tests

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder test tests/Ventus.Domain.Tests/Ventus.Domain.Tests.csproj
```

### Run tests with code coverage

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder test Ventus.slnx \
  --collect:"XPlat Code Coverage" \
  --results-directory /source/TestResults
```

Coverage reports appear in `TestResults/` on your host (the workspace is mounted).

---

## Running the Application

### Start the full stack (app + postgres)

```bash
docker compose up -d
```

The API is available at `http://localhost:8096` (or the port set in `.env`).

This full-stack Docker run is useful for development and Production Docker Acceptance preparation. It does not satisfy Local Acceptance. Local Acceptance requires the Ventus app process to run on the host machine while dependencies such as PostgreSQL and the supported OIDC backend may run in Docker. Acceptance ownership and the two-stage split follow [Agent Guidance](project/AGENT_GUIDANCE.md).

### View logs

```bash
docker compose logs -f app
```

### Stop the stack

```bash
docker compose down
```

To also remove named volumes (destroys all persisted data — use with caution):

```bash
docker compose down -v
```

---

## Database Migrations

### Apply all pending migrations

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder ef database update \
  --project src/Ventus.Persistence
```

### Add a new migration

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder ef migrations add <MigrationName> \
  --project src/Ventus.Persistence \
  --startup-project src/Ventus.Api
```

### List applied migrations

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder ef migrations list \
  --project src/Ventus.Persistence
```

> Migrations are run automatically at application startup via `app.Services.GetRequiredService<VentusDbContext>().Database.MigrateAsync()`.
> The `dotnet ef database update` command is for local development and CI diagnostics.

---

## Package Versions

NuGet package versions are managed centrally in `Directory.Packages.props`.
Individual project files must **not** include `Version` attributes on `<PackageReference>` elements.

To check current resolved versions inside the container:

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml \
  run --rm builder restore --verbosity normal Ventus.slnx 2>&1 | grep "Resolving"
```

> **Note:** Several `Microsoft.*` and `Npgsql.*` packages are pinned to .NET 10 preview versions.
> Update the version numbers in `Directory.Packages.props` when a newer preview or GA release is available.
> See [NuGet Gallery](https://www.nuget.org) for latest versions.

---

## Project Structure

```
Ventus.slnx                    — .NET solution (XML format, .NET 10)
Directory.Build.props          — Shared MSBuild properties (TFM, Nullable, TreatWarningsAsErrors)
Directory.Packages.props       — Central package version management
NuGet.config                   — Explicit nuget.org source (no ambient feed injection)
global.json                    — .NET SDK version constraint (requires .NET 10)
Dockerfile                     — Multi-stage: build (SDK) + runtime (aspnet)
docker-compose.yml             — Production stack (app + postgres)
docker-compose.dev.yml         — Development overrides (builder service + dev settings)
.env.example                   — Environment template (copy to .env, fill in secrets)

src/
  Ventus.Domain/               — Pure domain types; no framework or infrastructure references
  Ventus.Application/          — Application services and interfaces (no infrastructure details)
  Ventus.Persistence/          — EF Core DbContext, entity configs, migrations (ADR-003)
  Ventus.Infrastructure/       — Serilog/OTel wiring, background workers (ADR-004)
  Ventus.Api/                  — ASP.NET Core host, endpoint registration, middleware (ADR-002)

tests/
  Ventus.Domain.Tests/         — Domain-layer unit tests
  Ventus.Application.Tests/    — Application-layer unit tests
  Ventus.Api.Tests/            — API integration tests (WebApplicationFactory)
```

---

## Conventions

- **Conventional Commits** enforced via `.githooks/commit-msg`. Types: `feat`, `fix`, `docs`, `test`, `chore`, `refactor`, `perf`.
- **Branch names:** `feat/<short-description>`, `fix/<short-description>`, `chore/<short-description>`.
- **Code style:** see `.editorconfig` at the repo root.
- **No host SDK:** never run `dotnet build` or `dotnet test` outside the container — the `global.json` requires .NET 10 which may not match the host SDK.
- **Pre-implementation documentation gate:** before source code, tests, migrations, scripts, or generated files are written, the active milestone must have the required SPEC, ADR, and schema documents accepted with passing owner validation records and linked from the milestone context. Missing, Draft, pending-validation, stale, or unlinked docs block coding; do not backfill them after implementation. If implementation already exists before acceptance, report a pre-implementation gate bypass until the document loop is completed or an explicit user exception is recorded.
- **Framework defaults first:** all specs, ADRs, schemas, tests, and implementations follow [Agent Guidance: Framework Defaults First](project/AGENT_GUIDANCE.md). Prefer native framework/platform behavior and document approved exceptions before writing custom replacements.
