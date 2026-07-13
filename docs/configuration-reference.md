# Configuration Reference

Ventus reads runtime configuration from the `Ventus` section. In Docker deployments, set these values with the `VENTUS__` environment variable prefix. The double underscore maps to the section separator used by ASP.NET Core configuration binding.

Ventus validates this configuration during startup. A missing or invalid required value stops the process before it can serve requests.

## Environment Variable Mapping

| Environment variable | Required | Default | Allowed values | Description |
|---|---|---|---|---|
| `VENTUS__DATABASECONNECTIONSTRING` | Yes | None | Any non-empty PostgreSQL connection string | Database connection string used by the API host. Supply this at deploy time and keep it out of source control. |
| `VENTUS__LOGLEVEL` | No | `Information` | `Verbose`, `Debug`, `Information`, `Warning`, `Error`, `Fatal` | Minimum application log level. Invalid values stop startup validation. |
| `VENTUS__PUBLICORIGIN` | Yes (Production) | None | Absolute HTTPS URL or approved internal HTTP origin | Public-facing origin used to construct OIDC redirect URIs. Internal HTTP is allowed for loopback, private/link-local IPs, single-label internal hostnames, and `.local` names. |
| `VENTUS__DATAPROTECTION__KEYRINGPATH` | No | `/data/config/dataprotection-keys` | Absolute directory path | Filesystem path where ASP.NET Core Data Protection persists its key ring. Must be on a mounted volume for cookie and correlation state to survive container recreation. |
| `VENTUS__SKIPDATABASEMIGRATIONS` | No | `false` | `true` or `false` | Skips `Database.Migrate()`. Use only when migrations are managed externally. |

## Docker Compose Example

```yaml
services:
  app:
    environment:
      VENTUS__DATABASECONNECTIONSTRING: Host=postgres;Port=5432;Database=ventus;Username=ventus;Password=${VENTUS_DB_PASSWORD}
      VENTUS__LOGLEVEL: Information
      VENTUS__PUBLICORIGIN: http://ventus.local:8096
```

## Operational Notes

- Do not commit the database connection string to the repository. Inject it at runtime through Docker Compose, your container platform, or your secret-management workflow.
- If `VENTUS__DATABASECONNECTIONSTRING` is missing or empty, Ventus fails startup with an options validation error.
- If `VENTUS__LOGLEVEL` is omitted, Ventus uses `Information`.
- If `VENTUS__LOGLEVEL` is present, use the exact supported level names shown above.
- `VENTUS__PUBLICORIGIN` must be an origin only: scheme, host, and optional port; no path, query, or fragment.
- Public internet origins should use HTTPS. Internal HTTP origins are supported for local and private deployments.
- First-run setup is available at `/setup` when no validated OIDC provider exists; no setup key is required.