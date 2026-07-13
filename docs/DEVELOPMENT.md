# Development Guide

This document is the project-specific development guide. It starts generic so the first implementation team can record the actual toolchain and commands after the stack is chosen.

## Prerequisites

| Tool | Minimum version | Purpose | Required |
| --- | --- | --- | --- |
| Git | Recent stable version | Source control | Yes |
| Runtime or SDK | TBD | Build and run the project | TBD |
| Package manager | TBD | Resolve dependencies | TBD |
| Container or service tooling | TBD | Optional local dependencies or deployment | TBD |

## First-Time Setup

1. Copy the project environment template when one exists.
2. Install the selected runtime and package-manager versions.
3. Configure local-only secrets through the documented secret mechanism.
4. Run the health check or smallest validation command.
5. Record any machine-specific setup in this guide without committing credentials.

## Build And Run

Replace these placeholders when the stack is selected:

```text
Install dependencies: TBD
Build: TBD
Run locally: TBD
Run the production-shaped local stack: TBD
```

## Tests And Validation

```text
Run focused tests: TBD
Run the full test suite: TBD
Run integration or contract checks: TBD
Run browser or operational checks: TBD
```

Record the execution context and result in the relevant testing document.

## Configuration

- Use `docs/configuration-reference.md` as the source of truth for runtime settings.
- Keep local environment files and secrets out of version control.
- Document safe defaults and required production values.
- Fail clearly when required configuration is missing or invalid.

## Project Structure

Document the chosen structure here after scaffolding:

```text
src/       TBD
tests/     TBD
docs/      Project decisions, specifications, schemas, and validation
scripts/   TBD
```

## Conventions

- Use the repository's chosen commit and branch conventions.
- Keep formatting and linting commands reproducible.
- Prefer small, reviewable changes with focused tests.
- Update state, specifications, decisions, and changelog entries when behavior changes.
