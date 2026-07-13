# Configuration Reference

This document is the source of truth for runtime configuration. Replace the placeholders when the project stack and deployment model are chosen.

## Configuration Rules

- Required values must be validated at startup or at the boundary where they are first needed.
- Secrets must be injected through the approved secret-management mechanism and must never be committed.
- Safe local defaults may be documented, but production values must be explicit.
- Configuration names should use one consistent naming convention across files, environments, and deployment tooling.

## Environment Variables

| Variable | Required | Default | Allowed values | Description |
| --- | --- | --- | --- | --- |
| `PROJECT__EXAMPLE` | TBD | TBD | TBD | Replace with the first project setting. |

## Local Configuration Example

```text
# Use the format required by the selected stack.
PROJECT__EXAMPLE=replace-me
```

## Deployment Example

```yaml
# Replace with the selected deployment format.
services:
  app:
    environment:
      PROJECT__EXAMPLE: ${PROJECT_EXAMPLE}
```

## Operational Notes

- Do not commit real credentials, tokens, private keys, or personal data.
- Document which values may be changed without restarting the application.
- Document persistence, rotation, and migration requirements for secrets and durable configuration.
- Record configuration changes that affect compatibility, security, data, or deployment in an ADR.
